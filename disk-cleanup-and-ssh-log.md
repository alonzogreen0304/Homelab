# Homelab Log: Disk Space Cleanup + First SSH Connection

**Date:** October 21, 2026 (night)
**Device:** Dell Wyse 5060 (Debian 12), Windows laptop
**Outcome:** Main drive went from **93% → 81%** full, and I can now control the Wyse from my laptop over SSH through Tailscale, from anywhere

**Beginner level:** still learning, documenting as I go.

---

## Part 1: Disk space cleanup

### What triggered it

The Pi-hole dashboard showed a diagnosis message: **`DISK_EXTENDED: Disk shortage ahead`**. The Wyse only has a ~6.4GB system drive, and it was **92–93% full** (about 460MB free). A full drive can break Pi-hole's logging, make updates fail, and even cause boot problems.

### Step 1: Measure first

```bash
df -h /
```
`6.4G size, 5.6G used, 462M avail, 93%`

### Step 2: Safe, standard cleanup

```bash
sudo apt clean                          # delete downloaded update packages
sudo apt autoremove -y                  # remove packages nothing depends on anymore
sudo docker image prune -a              # delete Docker images no container uses
sudo journalctl --vacuum-size=100M      # shrink system logs to 100MB
```

The log cleanup alone freed **184.9MB**. Result: **90%, 647MB free**. Better, but still tight.

### Step 3: Find what's actually using the space

```bash
sudo du -xh / --max-depth=2 2>/dev/null | sort -h | tail -15
```

| Folder | Size | What it is |
|---|---|---|
| `/usr` | 4.0G | The OS itself, programs, XFCE desktop, drivers for two kernels |
| `/home/me` | 651M | My personal folder (suspiciously big for a fresh server) |
| `/var` | 603M | Docker, Pi-hole, logs |
| `/boot` | 154M | Kernel boot files |

Then drilled into my home folder:

```bash
du -sh ~/* ~/.[!.]* 2>/dev/null | sort -h | tail -10
```

The biggest item was **`~/.npm-global` at 486MB**: globally installed Node.js tools (OpenClaw, my AI agent project).

### Step 4: Investigate before deleting

Instead of deleting or moving it right away, I checked what was actually in use:

```bash
pgrep -af openclaw          # what's running, and from where?
npm config get prefix       # where does npm install global packages now?
which openclaw              # which copy does the command actually run?
ls -la ~ | grep npm         # is ~/.npm-global a real folder or a shortcut?
du -sh /mnt/agents/npm-global
```

**What I found:**
- The running OpenClaw gateway was running from **`/mnt/agents/npm-global`**, which is on my 1.8TB external drive
- `npm` prefix and `which openclaw` both pointed to `/mnt/agents/...`
- `~/.npm-global` was a real folder dated **May 9**, an **old duplicate** left over from before I moved OpenClaw to the big drive

**Good catch:** my original plan was to copy `~/.npm-global` onto the big drive. If I had done that, I would have overwritten the live install that was running. Checking first saved me from breaking it.

### Step 5: Remove the duplicate safely (rename → test → delete)

```bash
mv ~/.npm-global ~/.npm-global.old      # rename instead of deleting
openclaw --version                      # still works? → OpenClaw 2026.5.7
pgrep -af openclaw                      # gateway still running from /mnt/agents? → yes
rm -rf ~/.npm-global.old                # only now delete it
rm -rf ~/.cache/*                       # cache is safe to clear
```

### Result

```bash
df -h /
```
`6.4G size, 4.9G used, 1.2G avail, 81%`

| | Before | After |
|---|---|---|
| Used | 93% | **81%** |
| Free | 462MB | **1.2GB** |

---

## Part 2: SSH from my laptop

### Install SSH on the Wyse

```bash
sudo apt install openssh-server -y
```

The install automatically:
- Generated the server's **host keys** (RSA, ECDSA, ED25519): its identity
- Enabled SSH to **start on every boot**

### Connect from my laptop through Tailscale

1. Installed **Tailscale on my Windows laptop** and logged in with the same account
2. Opened **PowerShell** and connected using the Wyse's **Tailscale IP** (works from any network):
   ```powershell
   ssh me@<wyse-tailscale-ip>
   ```

### The security check: host key fingerprint

On the first connection, SSH warned:

```
The authenticity of host '<wyse-tailscale-ip>' can't be established.
ED25519 key fingerprint is SHA256:tdtP5nx...ZjWLY.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Before typing `yes`, I **compared that fingerprint with the one the Wyse printed when SSH was installed**. They matched, which proves I was talking to my real Wyse and not an impostor sitting in the middle.

This is how SSH protects against **man-in-the-middle (MITM)** attacks. The laptop now remembers this key in its `known_hosts` file, and if it ever changes unexpectedly, SSH will loudly warn me.

(A host key fingerprint is public by design. It identifies the server but can't be used to log in.)

### It worked

Logged in, saw `me@Wyse:~$` on my laptop, and ran `df -h /` remotely to confirm the 81%.

**No more monitor cable swapping.** The Wyse is now fully headless.

---

## Key takeaways

- **Measure → investigate → act.** `df` showed the problem, `du` found the cause, and `pgrep`/`which`/`npm config` proved what was safe to remove.
- **Rename before you delete.** A rename is instantly reversible; `rm -rf` isn't.
- **Check what's running before moving files.** Copying over a live install could have broken it.
- **Verify SSH fingerprints on first connect.** That one `yes` is a real security decision.
- **Remote admin over a VPN, not open ports.** SSH is never exposed to the internet; it's only reachable through Tailscale.

---

## Next steps

- [ ] Set up SSH keys on the laptop and disable password login ([ssh-setup.md](ssh-setup.md))
- [ ] Check that the OpenClaw gateway (port 18789) only listens on localhost, not the whole network
- [ ] Fix IP forwarding so the Tailscale exit node works
- [ ] Switch the network config to `allow-hotplug`
- [ ] Consider removing the old fallback kernel once the new one has run stable for a while (~300–400MB)
