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
| [Tailscale](tailscale.md) | Secure remote access to my network from anywhere | ✅ Reaching Pi-hole from my phone anywhere; DNS fixed; exit node working |
| Docker | Runs services in isolated containers | ✅ Running |
| [SSH](ssh-setup.md) | Control the server from my laptop, no monitor needed | ✅ Working from my laptop over Tailscale, key-only (passwords disabled) |
| [Unbound](unbound.md) | My own recursive DNS resolver: Pi-hole asks the internet's root servers directly instead of Google, with DNSSEC validation | ✅ Running, Pi-hole's only upstream |
| [Jellyfin](jellyfin.md) | My own streaming server ("personal Netflix"), reachable from anywhere over Tailscale | ✅ Running, Movies + Shows libraries set up |
| [Homepage](homepage.md) | One dashboard linking every service: live Pi-hole, Uptime Kuma and Jellyfin widgets, status dots, weather, and study links | ✅ Running, purple redesign |
| [Uptime Kuma](uptime-kuma.md) | Checks every service every 60 seconds (web pages and real DNS queries) and shows uptime history | ✅ Running, 4 monitors with phone alerts (fire drill passed) |
| [CrowdSec](crowdsec.md) | Reads SSH logs, detects brute-force attacks, and bans attacker IPs at the firewall (IDS + IPS) | ✅ Running, caught and banned a simulated attack |
| XFCE desktop | Lightweight graphical desktop | ✅ Installed, but the server runs headless (no monitor needed) |

---

## Progress so far

| Date | What I did |
|---|---|
| Oct 24, 2026 | Login screen flagged disk growth (79% → 86%); measured it, removed Discord, Firefox (least functionality) and the apt cache: back to 81% ([log](disk-cleanup-and-ssh-log.md)) |
| Oct 24, 2026 | Custom neofetch logo (AZ + Matrix rain + heart + bat) shown at every login ([notes](ssh-setup.md)) |
| Oct 24, 2026 | Custom purple login screen with live stats (uptime, disk, containers, CrowdSec bans); it immediately flagged the main disk at 86% ([notes](ssh-setup.md)) |
| Oct 24, 2026 | Added an SSH legal warning banner ("Authorized access only"), a GRC control, and verified it from an attacker's view ([notes](ssh-setup.md)) |
| Oct 24, 2026 | Closed CrowdSec's Docker side door (`DOCKER-USER` chain) and proved it with a self-expiring 2-minute ban on my own phone ([notes](crowdsec.md)) |
| Oct 23, 2026 (night) | Redesigned Homepage: purple theme, custom background, weather, status dots, Uptime Kuma widget, Security + Study rows ([notes](homepage.md)) |
| Oct 23, 2026 (night) | Installed CrowdSec + firewall bouncer, fixed it reading a log file Debian 12 doesn't have, then ran a red-team/blue-team drill: my phone brute-forced SSH and got detected (`ssh-bf`) and banned ([notes](crowdsec.md)) |
| Oct 23, 2026 (night) | Installed Uptime Kuma and set up 4 monitors (Pi-hole web, Pi-hole DNS, Jellyfin, Homepage), all from my phone; fixed a self-signed cert failure; push alerts to my phone with ntfy ([notes](uptime-kuma.md)) |
| Oct 23, 2026 (night) | From my phone over SSH: removed 200MB of leftover containerd image data, system drive 82% → 79% ([notes](jellyfin.md)) |
| Oct 23, 2026 (night) | SSH from my iPhone over Tailscale with its own key, so I can manage the server from anywhere ([notes](ssh-setup.md)) |
| Oct 23, 2026 | Built a Homepage dashboard for all my services ([notes](homepage.md)) |
| Oct 22, 2026 (night) | Installed Jellyfin; fixed a `no space left` failure by moving Docker's image storage off the small system drive ([notes](jellyfin.md)) |
| Oct 22, 2026 (night) | Installed Unbound as a private recursive DNS resolver with DNSSEC; Pi-hole now asks it instead of Google ([notes](unbound.md)) |
| Oct 22, 2026 (evening) | Verified the exit node, recovered Pi-hole after it vanished on reboot (Docker started before its drive mounted), made Docker wait for the drive, and got Pi-hole blocking ads for my laptop and phone from anywhere over Tailscale ([log](pihole-docker-recovery-log.md)) |
| Oct 22, 2026 | Shut the Wyse down cleanly for a cable cleanup; it came back headless on its own after reboot (Tailscale green, `allow-hotplug` working) |
| Oct 21, 2026 (night) | Freed disk space from 93% to 81%, set up SSH from my laptop over Tailscale, switched SSH to key-only login, audited every open port, fixed IP forwarding, and switched to `allow-hotplug` ([log](disk-cleanup-and-ssh-log.md)) |
| Oct 21, 2026 | Ran the Wyse headless for the first time, reached Pi-hole from my phone through Tailscale, and fixed a Tailscale DNS setting that broke my phone's internet off the home network ([log](remote-access-log.md)) |
| Oct 21, 2026 | Built a 158-acronym cheat sheet and the Acronym Breach practice game, published with GitHub Pages |
| Oct 21, 2026 | Wrote the SSH setup guide and the Wyse safety net plan |
| Oct 20, 2026 | Recovered the Wyse from a boot failure: emergency mode, no network, no desktop, and a kernel panic ([log](wyse-boot-recovery.md)) |

---

## Logs and write-ups

- [Wyse boot failure recovery](wyse-boot-recovery.md): fixing emergency mode, network, desktop, and a kernel panic
- [First headless boot + remote access](remote-access-log.md): exit node kill switch, a missing ethernet cable, and reaching Pi-hole from my phone through Tailscale
- [Disk cleanup, SSH, and hardening](disk-cleanup-and-ssh-log.md): finding a 486MB duplicate safely, verifying an SSH fingerprint, key-only SSH, and an open-port audit
- [Pi-hole vanished after reboot + Pi-hole over Tailscale](pihole-docker-recovery-log.md): a Docker boot-order race condition and Pi-hole's listening mode
- [Wyse safety net](wyse-safety-net.md): production rules and how to set up (and undo) every safety net before running Pi-hole for the whole house

---

## Study tools

- [Acronyms cheat sheet](acronyms.md): 158 Security+ and homelab acronyms with plain-English meanings
- [Know Your Lab](https://alonzogreen0304.github.io/Homelab/games/know-your-lab.html): Learn cards (simple explanation + analogy + where it is in my lab) and a 37-question quiz on everything I've built ([source](games/know-your-lab.html))
- [Lab House](https://alonzogreen0304.github.io/Homelab/games/lab-house.html): a beginner study guide to my whole lab, explained as a house. Starts with "what is a homelab / server / port", then every underlined term (DNS, VPN, IDS, etc.) is tappable with a plain meaning and a real example, plus a deep-dive on hardware, operating systems, Linux, what a package manager is and where programs come from, and what programs/services/containers are, and a 50-term A-Z glossary. Tap a room (mail sorter = Pi-hole, camera guard = CrowdSec, etc.) for an animation, what it does, how it's set up in my lab, and the security idea behind it. Includes a guided tour and a "follow a website visit" mode ([source](games/lab-house.html))
- [Command Center](https://alonzogreen0304.github.io/Homelab/games/command-center.html): every command from my lab (51 of them). **Learn** cards (what it does, analogy, where I used it, flag-by-flag breakdown), a **Quiz** (what does it do, which command, and 20 "what we did" troubleshooting scenarios), and **Type It**, where I type commands from memory. Tracks XP, streaks, and per-command mastery ([source](games/command-center.html))
- [Acronym Breach](https://alonzogreen0304.github.io/Homelab/games/acronym-breach.html): a terminal-style hacker game for drilling them. Play it in the browser, or add it to your phone's home screen ([source](games/acronym-breach.html))

---

## Roadmap

- [ ] Point my router's DNS at Pi-hole so every device gets ad blocking
- [x] Free up space on the Wyse's drive (93% → 81%)
- [x] Fix IP forwarding
- [x] Approve and test the Wyse as an exit node (ISP flips from T-Mobile to Comcast)
- [x] Switch the network config to `allow-hotplug` (survived a full power-down and reboot)
- [x] Pi-hole ad blocking on my laptop and phone from anywhere, over Tailscale
- [ ] Change the weak Pi-hole web password
- [x] Review Tailscale devices (old iPhones reconnected and in use)
- [x] Reach the server remotely through Tailscale
- [x] Run the server headless (no monitor needed)
- [x] Set up SSH from my laptop
- [x] Switch SSH to key-based login and disable passwords
- [x] SSH from my phone with its own key
- [x] Audit open ports (OpenClaw gateway is localhost-only)
- [x] Set up Unbound (recursive DNS + DNSSEC)
- [ ] Second Pi-hole on another always-on device, synced with nebula-sync
- [x] Install Jellyfin
- [x] Reclaim old containerd image data on the internal drive (203M → 668K)
- [x] Homepage dashboard
- [x] Uptime monitoring (Uptime Kuma)
- [x] Phone push alerts when a service goes down (ntfy)
- [x] Intrusion detection + prevention (CrowdSec)
- [ ] Auto power-on after outages: BIOS AC Recovery → Power On ([safety net 4](wyse-safety-net.md))
- [ ] Wyse + extender on the APC UPS battery outlets; later `apcupsd` for clean shutdowns ([safety net 7](wyse-safety-net.md))
- [ ] AI SOC analyst: nightly read-only Claude briefing of CrowdSec / Uptime Kuma / disk status pushed to my phone via ntfy (locked-down user, key in `chmod 600`, logs treated as untrusted for prompt injection)
- [x] CrowdSec `DOCKER-USER` fix + re-test: bans now block containers too ([notes](crowdsec.md))
- [x] Find what grew the main disk from 79% to 86% (spotted by the new login screen): new programs; removed Discord + Firefox, 86% → 81%
- [ ] Build a VM lab on my PC (Kali, Ubuntu, Windows Server, Wazuh SIEM)
- [ ] Audit the homelab against NIST CSF / CIS Controls

---

## Security note

Private details like internal IPs, device IDs, and account info are intentionally left out of this repo.
