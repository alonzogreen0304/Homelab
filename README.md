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
| Wall-plug Wi-Fi extender | Gives the Wyse its ethernet connection |
| Gaming PC (Windows) | Future virtual machine lab |
| Laptop | Remote access and study |

---

## What's running on the server

| Service | What it does | Status |
|---|---|---|
| [Pi-hole](pihole.md) | Blocks ads and trackers at the DNS level | ✅ Running in Docker, not yet network-wide |
| [Tailscale](tailscale.md) | Secure remote access to my network from anywhere | ✅ Reaching Pi-hole from my phone anywhere; DNS fixed; exit node waiting on IP forwarding |
| Docker | Runs services in isolated containers | ✅ Running |
| [SSH](ssh-setup.md) | Control the server from my laptop, no monitor needed | 🔜 Setup planned |
| XFCE desktop | Lightweight graphical desktop | ✅ Installed, but the server runs headless (no monitor needed) |

---

## Progress so far

| Date | What I did |
|---|---|
| Oct 21, 2026 | Ran the Wyse headless for the first time, reached Pi-hole from my phone through Tailscale, and fixed a Tailscale DNS setting that broke my phone's internet off the home network ([log](remote-access-log.md)) |
| Oct 21, 2026 | Built a 158-acronym cheat sheet and the Acronym Breach practice game, published with GitHub Pages |
| Oct 21, 2026 | Wrote the SSH setup guide and the Wyse safety net plan |
| Oct 20, 2026 | Recovered the Wyse from a boot failure: emergency mode, no network, no desktop, and a kernel panic ([log](wyse-boot-recovery.md)) |

---

## Logs and write-ups

- [Wyse boot failure recovery](wyse-boot-recovery.md): fixing emergency mode, network, desktop, and a kernel panic
- [First headless boot + remote access](remote-access-log.md): exit node kill switch, a missing ethernet cable, and reaching Pi-hole from my phone through Tailscale
- [Wyse safety net](wyse-safety-net.md): production rules and how to set up (and undo) every safety net before running Pi-hole for the whole house

---

## Study tools

- [Acronyms cheat sheet](acronyms.md): 158 Security+ and homelab acronyms with plain-English meanings
- [Acronym Breach](https://alonzogreen0304.github.io/Homelab/games/acronym-breach.html): a terminal-style hacker game for drilling them. Play it in the browser, or add it to your phone's home screen ([source](games/acronym-breach.html))

---

## Roadmap

- [ ] Point my router's DNS at Pi-hole so every device gets ad blocking
- [ ] Free up space on the Wyse's 7.5GB drive (Pi-hole flagged "Disk shortage ahead") and move Docker data to the 1.8TB drive
- [ ] Fix IP forwarding so the Tailscale exit node works
- [ ] Switch the network config to `allow-hotplug` so a cable plugged in after boot still works
- [ ] Bring back Pi-hole ad blocking on the go (point Tailscale DNS at the Wyse's Tailscale IP)
- [ ] Remove old devices from Tailscale
- [x] Reach the server remotely through Tailscale
- [x] Run the server headless
- [ ] Set up SSH from my laptop, then switch to key-based login ([guide](ssh-setup.md))
- [ ] Build a VM lab on my PC (Kali, Ubuntu, Windows Server, Wazuh SIEM)
- [ ] Audit the homelab against NIST CSF / CIS Controls

---

## Security note

Private details like internal IPs, device IDs, and account info are intentionally left out of this repo.
