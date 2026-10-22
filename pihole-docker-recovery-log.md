# Homelab Log: Pi-hole Vanished After Reboot + Pi-hole Over Tailscale

**Date:** October 22, 2026
**Device:** Dell Wyse 5060 (Debian 12), Pi-hole in Docker, Tailscale
**Outcome:** Recovered Pi-hole without losing settings, fixed the boot-order bug that caused it, and got Pi-hole answering DNS for Tailscale devices. Laptop **and phone** now use Pi-hole from anywhere through Tailscale.

**Beginner level:** still learning, documenting as I go.

---

## Part 1: Exit node verified

Approved the Wyse as an exit node in the Tailscale admin console and tested from my phone on cellular. ISP showed **Comcast** (home) with the exit node on and **T-Mobile** (carrier) with it off. Details and screenshots in [tailscale.md](tailscale.md).

---

## Part 2: Pi-hole was gone

### Symptom
After pointing Tailscale DNS at the Wyse, my phone said "can't connect to server." Then:

```bash
sudo docker ps -a          # empty: no containers at all, not even stopped ones
sudo ss -lunp | grep ':53 '   # empty: nothing answering DNS
```

### Investigation

```bash
sudo docker info | grep -i "root dir"     # Docker Root Dir: /mnt/agents/docker
cat /etc/docker/daemon.json               # "data-root": "/mnt/agents/docker"
sudo du -sh /mnt/agents/docker            # 1.3M (basically empty)
ls /mnt/agents                            # pihole/ dnsmasq/ docker/ npm-global/ ...
sudo find / -xdev -name "docker-compose*.yml"   # /opt/pihole/docker-compose.yml
```

### Root cause (my best understanding)

Docker is configured to keep its data on the **external drive** (`/mnt/agents/docker`). Nothing told Docker to **wait for that drive** at boot. On earlier boots Docker likely started *before* the drive mounted, so it quietly used the empty folder on the internal drive underneath the mount point. After my cable-cleanup reboot the order changed, Docker saw the real (empty) folder on the external drive, and had no containers to run.

This is a classic **race condition** between two boot-time services.

### Recovery

Pi-hole's **settings** are stored separately in bind-mounted folders on the external drive (`/mnt/agents/pihole/etc-pihole`, `etc-dnsmasq.d`), which I confirmed with `ls` before doing anything. Since those were safe, I just recreated the container from the compose file:

```bash
cd /opt/pihole && sudo docker compose up -d
sudo docker ps                                  # pihole  Up (health: starting)
dig @<wyse-tailscale-ip> google.com +short      # got an IP back: DNS working
```

Settings, blocklists, and history all came back.

### Permanent fix: make Docker wait for the drive

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
printf '[Unit]\nRequiresMountsFor=/mnt/agents\n' | sudo tee /etc/systemd/system/docker.service.d/wait-for-drive.conf
sudo systemctl daemon-reload
```

`RequiresMountsFor` tells systemd: don't start Docker until `/mnt/agents` is mounted.

---

## Part 3: Pi-hole ignoring Tailscale devices

### Symptom
Watched Pi-hole live:

```bash
sudo docker exec -it pihole pihole -t
```
Every line from my laptop said:
```
ignoring query from non-local network 100.x.x.x
```

### Root cause
Pi-hole's default **listening mode** only answers devices on the local network. Tailscale devices (100.x addresses) count as "non-local," so Pi-hole refused them.

### Fix
```bash
sudo docker exec pihole pihole-FTL --config dns.listeningMode ALL
```
Safe here because the Wyse is **not exposed to the internet**: "all" only means my home network plus my tailnet. The setting is saved in Pi-hole's config on the external drive, so it persists.

Result: the live feed immediately showed real lookups from my laptop (`query[A] ... from 100.x.x.x` → `forwarded` → `reply`).

### Tailscale admin DNS
- Global nameserver: the Wyse's **Tailscale IP** (not its local `192.168` IP)
- **Override DNS servers:** on

---

## Status

- ✅ Laptop uses Pi-hole over Tailscale from anywhere
- ✅ **Phone uses Pi-hole over Tailscale too.** At first it sent nothing, even though the app said "Using Tailscale DNS" (the MagicDNS name `wyse` didn't resolve either). After turning off iCloud Private Relay, the fix was: turn **Use Tailscale DNS** off → **restart the iPhone** → connect Tailscale → turn **Use Tailscale DNS** back on. Right after, the live feed showed `query[A] clients4.google.com from <phone-tailscale-ip>`

---

## Key takeaways

- **`docker ps -a` empty ≠ data gone.** Check where Docker keeps its data (`docker info`) and where the app keeps its *settings* (volumes in the compose file) before recreating anything.
- **Separate config from containers.** Because Pi-hole's settings were in bind mounts, rebuilding the container lost nothing.
- **Boot order matters.** Services that depend on an external drive need `RequiresMountsFor=`.
- **Watch logs live** (`pihole -t`). The "ignoring query from non-local network" line pointed straight at the fix.
- **Sometimes it really is "turn it off and on again."** The iPhone held stale VPN/DNS state until a restart.
- **Test one device at a time.** The laptop working proved the server side was correct, which narrowed the phone issue to the phone.

## Next steps

- [ ] Change the weak Pi-hole web password stored in the compose file
- [ ] Reclaim internal-drive space used by Docker's old hidden data under `/mnt/agents`
