# CrowdSec: Detecting and Blocking Attacks

**Beginner level:** still learning, documenting as I go.
**Set up:** Oct 23, 2026

## What it is (simple version)

CrowdSec reads my server's logs, spots attack patterns (like someone hammering SSH with wrong logins), and **bans** the attacker's IP at the firewall. It's "crowd" powered: servers around the world share attacker IPs, so I also get a community blocklist.

**Analogy:** [Uptime Kuma](uptime-kuma.md) is the guard checking the doors still open. CrowdSec is the guard watching the **security cameras**. When it sees someone trying every key on the lock, it bans them from the building, and neighbors share their mugshot boards.

Two parts:
- **crowdsec (the engine):** the brain. It reads logs, matches patterns ("scenarios"), and decides
- **firewall bouncer:** the muscle. It pulls decisions from the engine and blocks IPs with iptables

In security terms, that's an **IDS** (detect) plus an **IPS** (prevent).

## Install

```bash
df -h /                                         # installs on the small drive, so check space first
sudo apt update
curl -s https://install.crowdsec.net | sudo sh  # adds CrowdSec's official repo
apt-cache policy crowdsec                       # Candidate should come from packagecloud.io/crowdsec, not Debian's older copy
sudo apt install crowdsec -y
sudo apt install crowdsec-firewall-bouncer-iptables -y
```

**Supply-chain note:** `curl ... | sudo sh` runs a remote script **as root**. I trusted it because it's CrowdSec's official domain. For anything unofficial, download it and read it first.

Verify:
```bash
sudo cscli collections list   # detection packs installed (sshd, linux, etc.)
sudo cscli bouncers list      # firewall bouncer registered
sudo cscli metrics            # what's being read, parsed, and decided
```

## Problem: the cameras were pointed at an empty hallway

My first test attack produced **no alerts**.

**Root cause:** CrowdSec watches `/var/log/auth.log` for SSH by default, but **Debian 12 doesn't create that file**. SSH logs live in the **systemd journal** instead. CrowdSec was watching a file that didn't exist.

**Fix:** added a journal source for SSH:
```bash
sudo tee /etc/crowdsec/acquis.d/ssh-journal.yaml > /dev/null <<'EOF'
source: journalctl
journalctl_filter:
  - "_SYSTEMD_UNIT=ssh.service"
labels:
  type: syslog
EOF
sudo systemctl restart crowdsec
```

**Lesson:** a detection tool is only as good as the logs it can see. "No alerts" can mean "no attacks" **or** "not looking." Always verify that log ingestion is working (`Acquisition Metrics`) before trusting silence.

## Attack simulation (red team vs. blue team)

- **Attacker:** my iPhone (Termius), connecting over Tailscale as a fake user `hacker` with no key, failing 6–8 times
- **Defender:** my laptop, SSH'd into the Wyse

**Result (`sudo cscli metrics`):**

| What | Value | Meaning |
|---|---|---|
| Source | `journalctl:..._SYSTEMD_UNIT=ssh.service` | Reading SSH logs from the journal ✅ |
| Lines read / parsed / unparsed | 48 / 16 / 32 | 16 lines matched attack patterns; the rest is normal SSH chatter |
| Alerts | `crowdsecurity/ssh-bf` ×1, `crowdsecurity/ssh-slow-bf` ×1 | Brute force detected, plus the "slow" variant that spaces attempts out to dodge detection |
| Decisions | `ssh-bf` → **ban** ×1 | The bouncer blocked my phone's IP at the firewall |

With the ban active, my phone couldn't reach anything on the Wyse. Then I unbanned it from the laptop:
```bash
sudo cscli decisions list
sudo cscli decisions delete --ip <phone-tailscale-ip>
```

## Finding: Docker's side door

While my phone was banned, **SSH was blocked, but Uptime Kuma (a Docker container) still loaded**. My screenshot timestamp (9:25) was ~3 minutes into the ban.

**Why:** the bouncer blocks banned IPs in the **INPUT** chain, which covers services running directly on the host (SSH). Traffic to **Docker-published ports** is forwarded to containers through Docker's own rules and **never passes INPUT**. The attacker was banned at the front door and walked in the side door.

**Fix (pending re-test):** add Docker's `DOCKER-USER` chain to the bouncer:
```bash
sudo sed -i 's/^  - INPUT$/  - INPUT\n  - DOCKER-USER/' /etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml
grep -A4 iptables_chains /etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml
sudo systemctl restart crowdsec-firewall-bouncer
```

**Lesson:** "the ban fired" isn't the same as "the attacker is blocked everywhere." Test the **effect** of a control, not just that it triggered. Docker bypassing host firewall rules is a well-known gotcha.

## Security concepts

- **IDS vs. IPS:** the engine detects; the bouncer prevents
- **Brute force detection:** a leaky-bucket scenario. Too many failures in a short window triggers it
- **Log ingestion / visibility:** you can't detect what you don't collect (the Debian 12 journal gap)
- **Defense in depth:** SSH is already key-only *and* Tailscale-only; CrowdSec adds a third layer
- **Threat intelligence sharing:** the community blocklist (CAPI)

## Open question: whitelist my tailnet?

CrowdSec's default whitelist covers home LAN ranges (192.168.x.x) but **not** Tailscale's `100.x` range, which is why it banned my own phone. Options:
- **Whitelist the whole tailnet:** no accidental self-bans, but a **compromised** tailnet device could brute-force freely
- **Leave it:** I might lock myself out, but any device misbehaving gets caught, including mine

For now I'm leaving it on. Treating internal devices as untrusted by default is **zero trust**.

## Next steps

- [ ] Enroll in the CrowdSec Console (web dashboard of alerts)
- [ ] Send CrowdSec alerts to my phone (ntfy), like Uptime Kuma
- [ ] Apply the `DOCKER-USER` fix and re-test: a banned phone should NOT reach any container
- [ ] Later: AI log triage, with read-only access and a human in the loop
