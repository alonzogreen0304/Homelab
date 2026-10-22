# Homelab Log: Disk Cleanup, SSH, and Hardening

**Date:** October 21, 2026 (night)
**Device:** Dell Wyse 5060 (Debian 12), Windows laptop
**Outcome:** Main drive went from **93% → 81%** full, I can control the Wyse from my laptop over SSH through Tailscale, SSH is key-only, every open port is audited, and IP forwarding is fixed

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

## Part 3: SSH key login + disabling passwords

### Make a key pair on the laptop (PowerShell)

```powershell
ssh-keygen -t ed25519
```
Pressed Enter for the default location and set a passphrase. This creates:
- `id_ed25519`: the **private key**, which never leaves my laptop
- `id_ed25519.pub`: the **public key**, which goes on the server

**Mistake I made:** I pasted the next command into the "Enter file in which to save the key" prompt. Fixed it with **Ctrl+C** and ran it again, this time just pressing Enter. Lesson: wait for each prompt to finish before pasting.

### Copy the public key to the Wyse

Windows has no `ssh-copy-id`, so I piped it over SSH:
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh me@<wyse-tailscale-ip> "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```
Then `ssh me@<wyse-tailscale-ip>` logged in **without the Wyse password**, so keys worked.

### Turn off password logins (with a safety net)

Kept my working SSH session open the whole time so I couldn't lock myself out. Created a drop-in config file:

```bash
sudo nano /etc/ssh/sshd_config.d/10-hardening.conf
```
```
PasswordAuthentication no
KbdInteractiveAuthentication no
PermitRootLogin no
```
```bash
sudo sshd -t                  # test the config for errors before applying
sudo systemctl reload ssh     # apply without dropping my session
```

### Proved it from a new laptop window

```powershell
ssh -o PubkeyAuthentication=no me@<wyse-tailscale-ip>
```
Result: **`Permission denied (publickey)`**. Password logins are blocked, so only my laptop's private key can get in.

**Undo, if ever needed:** `sudo rm /etc/ssh/sshd_config.d/10-hardening.conf && sudo systemctl reload ssh`

---

## Part 4: Open-port audit

```bash
sudo ss -tlnp
```
This lists every program waiting for network connections, which is basically an inventory of every open door on the server.

| Port | Program | Listening on | Verdict |
|---|---|---|---|
| 18789, 18791 | OpenClaw gateway (`node`) | `127.0.0.1` / `::1` only | ✅ Only the Wyse itself can reach it |
| 22 | SSH | All interfaces | ✅ Key-only, and not port-forwarded on the router |
| 53 | Pi-hole DNS | All interfaces | ✅ Expected for a DNS server |
| 80, 443 | Pi-hole dashboard | All interfaces | ✅ Home network + tailnet only |
| (random) | Tailscale | Tailscale IP only | ✅ Normal |

**No changes needed.** The AI agent gateway was the one I was worried about, and it's correctly bound to localhost.

---

## Part 5: IP forwarding (Tailscale exit node fix)

**Problem:** `tailscale status` warned *"Subnet routing is enabled, but IP forwarding is disabled."* Linux won't pass traffic between networks unless forwarding is turned on.

**Another lesson:** my SSH connection dropped while I was inside `nano`, and the lines I pasted after that went into **Windows PowerShell** instead, which threw red errors. The PowerShell window got scrambled, so I closed it, opened a fresh one, and switched to editor-free commands so a dropped connection can't cause damage:

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
sudo tailscale status
```
The forwarding warning is **gone**, and the settings survive reboots since they live in `/etc/sysctl.d/`.

(I also confirmed last night's typo file, `99-tailscae.conf`, was already gone.)

---

## Part 6: allow-hotplug

**Problem from earlier today:** with `auto enp4s0`, the ethernet port only comes up if the cable is plugged in **at boot**.

```bash
sudo cp /etc/network/interfaces /etc/network/interfaces.bak
sudo sed -i 's/^auto enp4s0/allow-hotplug enp4s0/' /etc/network/interfaces
cat /etc/network/interfaces
```
Now it reads `allow-hotplug enp4s0`, so the port comes up whenever a cable is detected. It takes effect on the **next boot**. I didn't restart networking over SSH, because that would cut my own connection.

**Undo, if ever needed:** `sudo cp /etc/network/interfaces.bak /etc/network/interfaces`

---

## Key takeaways

- **Measure → investigate → act.** `df` showed the problem, `du` found the cause, and `pgrep`/`which`/`npm config` proved what was safe to remove.
- **Rename before you delete.** A rename is instantly reversible; `rm -rf` isn't.
- **Check what's running before moving files.** Copying over a live install could have broken it.
- **Verify SSH fingerprints on first connect.** That one `yes` is a real security decision.
- **Always keep a way back in.** Keep one SSH session open while changing SSH settings, and test configs (`sshd -t`) before applying them.
- **Prefer editor-free commands over SSH** (`tee`, `sed`) so a dropped connection can't leave half-finished edits.
- **Audit what's listening** (`ss -tlnp`). If you don't know why a port is open, find out.
- **Remote admin over a VPN, not open ports.** SSH is never exposed to the internet; it's only reachable through Tailscale.

---

## Next steps

- [x] SSH keys + disable password login
- [x] Check that the OpenClaw gateway only listens on localhost
- [x] Fix IP forwarding
- [x] Switch the network config to `allow-hotplug`
- [ ] After the next reboot, confirm the Wyse still comes online on its own (green dot in Tailscale)
- [ ] Approve and test the Wyse as an exit node in the Tailscale admin console
- [ ] Consider limiting SSH to the Tailscale interface only
- [ ] Consider removing the old fallback kernel once the new one has been stable for a while (~300–400MB)
