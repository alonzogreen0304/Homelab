# Alonzo's Homelab

Hey! I'm a cybersecurity student, and this repo documents my homelab: what I build, what breaks, and how I fix it.

> **Beginner disclaimer:** I'm still learning. A lot of this is me figuring things out in real time, making mistakes, and writing down what I learned. If you're a beginner too, I hope this helps. If you're experienced, feedback is welcome.

---

## Why I'm doing this

- Get hands-on with Linux, networking, and security, not just read about them
- Build real experience for a SOC Analyst / GRC career path
- Practice documenting work the way security teams do (incident reports, configs, lessons learned)

---

## Hardware

| Device | Role |
|---|---|
| Dell Wyse 5060 thin client | Always-on home server (Debian 12) |
| 1.8TB external drive | Extra storage for the server |
| Gaming PC (Windows) | Future virtual machine lab |
| Laptop | Remote access and study |

---

## What's running on the server

| Service | What it does | Status |
|---|---|---|
| [Pi-hole](pihole.md) | Blocks ads and trackers at the DNS level | ✅ Running in Docker, not yet network-wide |
| [Tailscale](tailscale.md) | Secure remote access to my network from anywhere | ✅ Connected, exit node needs a fix |
| Docker | Runs services in isolated containers | ✅ Running |
| [SSH](ssh-setup.md) | Control the server from my laptop, no monitor needed | 🔜 Setup planned |
| XFCE desktop | Lightweight graphical desktop | ✅ Installed |

---

## Logs and write-ups

- [Wyse boot failure recovery](wyse-boot-recovery.md): fixing emergency mode, network, desktop, and a kernel panic
- [Wyse safety net](wyse-safety-net.md): production rules and how to set up (and undo) every safety net before running Pi-hole for the whole house

---

## Study tools

- [Acronyms cheat sheet](acronyms.md): 158 Security+ and homelab acronyms with plain-English meanings
- [Acronym Breach](https://alonzogreen0304.github.io/Homelab/games/acronym-breach.html): a terminal-style hacker game for drilling them. Play it in the browser, or add it to your phone's home screen ([source](games/acronym-breach.html))

---

## Roadmap

- [ ] Point my router's DNS at Pi-hole so every device gets ad blocking
- [ ] Fix IP forwarding so Tailscale exit node works
- [ ] Remove old devices from Tailscale
- [ ] Set up SSH from my laptop, then switch to key-based login ([guide](ssh-setup.md))
- [ ] Build a VM lab on my PC (Kali, Ubuntu, Windows Server, Wazuh SIEM)
- [ ] Audit the homelab against NIST CSF / CIS Controls

---

## Security note

Private details like internal IPs, device IDs, and account info are intentionally left out of this repo.
