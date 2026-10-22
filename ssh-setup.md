# SSH: Controlling the Wyse from My Laptop

**Beginner level:** still learning, documenting as I go.

## What it is (simple version)

SSH (Secure Shell) lets me open a terminal on my laptop and control the Wyse over the network, encrypted. No monitor, keyboard, or cable swapping needed. Servers in real companies are managed this way.

**Why not just plug the laptop into the Wyse with HDMI?** Laptop HDMI ports only send video out, they can't receive it. SSH replaces the cable entirely.

## Status

- [x] One-time setup on the Wyse
- [x] First connection from the laptop (over Tailscale, fingerprint verified, see [log](disk-cleanup-and-ssh-log.md))
- [ ] Hardening (SSH keys, disable password login)

---

## One-time setup (on the Wyse, with a monitor)

1. Check if SSH is already running:
   ```bash
   systemctl status ssh
   ```
   `active (running)` = skip to step 3.
2. If it's not installed:
   ```bash
   sudo apt install openssh-server -y
   ```
3. Get the Wyse's local IP (the first one, starts with `192.168`):
   ```bash
   hostname -I
   ```
   Write it down. After this, the monitor can go back to the PC.

## Connecting (from the laptop, every time)

1. Laptop on the **same home Wi-Fi** (the Wyse being on ethernet is fine, same router).
2. Open **PowerShell** (Windows) or **Terminal** (Mac).
3. Connect:
   ```bash
   ssh me@<wyse-ip>
   ```
4. First time only: it asks `Are you sure you want to continue connecting?` → type `yes`.
5. Enter the Wyse password (nothing shows while typing, that's normal).
6. Seeing `me@Wyse:~$` = connected. Every command works like I'm at the Wyse.

**Disconnect:** `exit` (the Wyse keeps running).

**From outside the house:** turn on Tailscale on the laptop and use the Wyse's Tailscale IP (`100.x.x.x`) instead. See [tailscale.md](tailscale.md).

## Troubleshooting

| Error | Meaning | Fix |
|---|---|---|
| `Connection refused` | SSH isn't installed or running on the Wyse | Redo setup step 2, then `sudo systemctl enable --now ssh` |
| `Connection timed out` | Wrong IP or laptop on a different network | Check Wi-Fi, recheck `hostname -I` on the Wyse |
| `Permission denied` | Wrong username or password | Username is `me`; retype the password slowly |
| `REMOTE HOST IDENTIFICATION HAS CHANGED` | The Wyse was reinstalled or its IP now points to a different device | Only if I know why: `ssh-keygen -R <wyse-ip>`, then reconnect |

## Rules over SSH

- Don't run `reboot`, network changes, or firewall changes unless I mean it. If the network drops, I lose the connection and need the monitor again.
- Follow the production rules in [wyse-safety-net.md](wyse-safety-net.md).
- Never open port 22 on the router to the internet. Use Tailscale for remote access.

## Optional: see the desktop remotely

If I want the XFCE desktop on the laptop instead of just a terminal, install `xrdp` on the Wyse and connect with Windows' built-in **Remote Desktop Connection** app. Heavier than SSH, but shows the full screen.

## Next: hardening (later)

- Set up **SSH keys** so I log in without a password
- Then **disable password login** in `/etc/ssh/sshd_config`
- Keep a second SSH session open while changing SSH settings, so a mistake doesn't lock me out

## What I learned

- SSH runs on **port 22** and encrypts everything, including the password
- Servers are usually "headless" (no monitor) and managed remotely
- Exposing SSH to the internet is a top attack target, so remote access goes through a VPN like Tailscale instead
