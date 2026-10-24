# Uptime Kuma: Monitoring My Lab

**Beginner level:** still learning, documenting as I go.
**Set up:** Oct 23, 2026, entirely from my phone (SSH in Termius + Safari over Tailscale)

## What it is (simple version)

Uptime Kuma checks every service in my lab every 60 seconds and records whether it answered. Green bar = up, red bar = down.

**Analogy:** a security guard doing rounds, knocking on every door once a minute and writing down who answered.

**Why I added it:** when Pi-hole vanished after a reboot ([log](pihole-docker-recovery-log.md)), I only found out because ads came back. With monitoring, I'd know within a minute.

## Install

Config in `/opt`, data on the big drive (same pattern as my other services):

```bash
sudo mkdir -p /opt/uptime-kuma /mnt/agents/uptime-kuma
sudo tee /opt/uptime-kuma/docker-compose.yml > /dev/null <<'EOF'
services:
  uptime-kuma:
    image: louislam/uptime-kuma:2
    container_name: uptime-kuma
    restart: unless-stopped
    ports:
      - "3001:3001"
    volumes:
      - /mnt/agents/uptime-kuma:/app/data
EOF
cd /opt/uptime-kuma && sudo docker compose up -d
```

Then I opened the web UI on port 3001 over Tailscale, chose **SQLite** (lightest database, right for a thin client), and created an admin account with a strong password.

**Security choice: no Docker socket.** Many guides mount `/var/run/docker.sock` into the container. That would give Uptime Kuma control over *every* container on the server. It only needs to *see* whether services answer, so I left it out. That's **least privilege**.

## My monitors

| Monitor | Type | What it checks | Analogy |
|---|---|---|---|
| Pi-hole Web | HTTP | Dashboard page returns `200 OK` | Does the store's front door open? |
| Pi-hole DNS | DNS | Asks Pi-hole to resolve `google.com` on port 53 | Walk in and actually **buy something**. Proves the cashier *and* the stockroom work |
| Jellyfin | HTTP | Jellyfin's built-in `/health` endpoint | Ask the theater manager "you open?" |
| Homepage | HTTP | Dashboard loads | Is the lobby directory still on the wall? |

**Why the DNS monitor matters most:** the Pi-hole web page can be up while DNS is broken. A real DNS query tests the whole chain, **Pi-hole → Unbound → root servers**, so it also covers Unbound (which only listens on localhost and can't be checked directly from a container).

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Pi-hole Web: `self-signed certificate in certificate chain` | I typed `https://`. Pi-hole v6 serves HTTPS with a **self-signed cert** no Certificate Authority vouches for, so Uptime Kuma refused it | Switched the URL to `http://`. Traffic is still encrypted by Tailscale (WireGuard) |
| Pi-hole DNS: `None of the configured resolver servers could be resolved to an IP address` | The Resolver Server field had extra text in it, not a clean IP | Cleared the field and typed only the IP; port goes in its own box (`53`) |

Low uptime percentages right after setup are just those early red checks. They climb to ~100% on their own.

## Security concepts

- **Availability** (the A in the CIA triad): monitoring is how you *prove* a service is available, not just hope it is
- **Continuous monitoring**: a core control in NIST CSF (Detect) and CIS Controls
- **PKI / chain of trust**: why self-signed certs fail validation by default
- **Least privilege**: no Docker socket
- **Known gap:** if the whole Wyse goes down, Uptime Kuma goes down with it and can't alert ("who watches the watchman"). Fix later: a second monitor on a different device

## Phone access

Saved to my iPhone home screen (Safari → Share → Add to Home Screen). It opens full-screen like an app. Requires Tailscale on.

## Next steps

- [ ] Phone push alerts with ntfy
- [ ] Add an Uptime Kuma tile to my [Homepage](homepage.md) dashboard
- [ ] Second monitor on another device to watch the Wyse itself
