# Jellyfin: My Own Streaming Server

**Beginner level:** still learning, documenting as I go.
**Set up:** October 22, 2026, on the Dell Wyse 5060 in Docker
**Project:** [Jellyfin](https://github.com/jellyfin/jellyfin) (free, open-source media server)

## What it is (simple version)

My own Netflix. I put movies and shows on my 1.8TB drive, and Jellyfin streams them to my phone, laptop, or TV, from anywhere, through Tailscale. No subscription, no ads, no tracking.

## My setup

| Setting | Value |
|---|---|
| Runs in | Docker (`jellyfin/jellyfin`), host networking |
| Recipe | `/opt/jellyfin/docker-compose.yml` |
| Runs as | My normal user (`1000:1000`), **not root**: least privilege |
| Settings | `/mnt/agents/jellyfin/config` (external drive) |
| Media | `/mnt/agents/jellyfin/media/movies`, `/media/tv` (external drive) |
| Web UI | `http://<wyse-tailscale-ip>:8096` (only reachable on my tailnet / home network) |

Same pattern as Pi-hole: **settings and media live on the drive, not in the container**, so the container can be deleted and rebuilt anytime without losing anything.

## Theme: Abyss (CSS-only)

Applied the [Abyss theme](https://github.com/AumGupta/abyss-jellyfin) with one line in **Dashboard → Branding → Custom CSS**:

```css
@import url('https://cdn.jsdelivr.net/gh/AumGupta/abyss-jellyfin@main/abyss.css');
```

**Why CSS-only instead of the setup script:** the theme also ships an `abyss-setup.sh` meant to run with `sudo`. Running someone else's script as root on my server is a real risk (I'd read it first), and it edits files *inside* the container, which get wiped whenever the container is rebuilt. The CSS import needs no root, survives rebuilds (it's saved in Jellyfin's config on my drive), and is undone by deleting one line. The only thing it skips is the optional "Spotlight" home banner.

**If a theme doesn't load:** hard refresh (Ctrl+Shift+R), and check Pi-hole's Query Log in case it's blocking the CDN (`cdn.jsdelivr.net`).

## Plugins: rules I follow

- Official catalog first. Plugins run inside the server with full access, so third-party repos are a **supply-chain risk**
- Keep it light: the Wyse CPU can't handle heavy video processing
- Restart Jellyfin after installing (`sudo docker restart jellyfin`)

## Limitation

The Wyse's CPU is too weak to **transcode** (convert video on the fly). Media should be in widely supported formats (MP4 / H.264) so devices can **direct play** it.

## The problem I hit installing it

The first install failed with `no space left on device` in `/var/lib/containerd/...`.

**Root cause:** newer Docker versions store images through **containerd's image store**, which keeps image layers in `/var/lib/containerd` on the small internal drive. My `data-root` setting (`/mnt/agents/docker`) didn't cover that, so the ~500MB Jellyfin download filled the 6.4GB system drive.

**Confirmed with:**
```bash
sudo docker info | grep -iE "storage driver|driver-type"
# Storage Driver: overlayfs
#  driver-type: io.containerd.snapshotter.v1
```

**Fix:** switched Docker back to its classic storage driver so *everything* lives in `data-root` on the external drive:
```bash
cd /opt/pihole && sudo docker compose down          # stop Pi-hole cleanly
sudo cp /etc/docker/daemon.json /etc/docker/daemon.json.bak
# daemon.json:
# {
#   "data-root": "/mnt/agents/docker",
#   "features": { "containerd-snapshotter": false }
# }
sudo systemctl restart docker
sudo docker info | grep -iE "storage driver|root dir"
# Storage Driver: overlay2
# Docker Root Dir: /mnt/agents/docker
cd /opt/pihole && sudo docker compose up -d          # re-pulled onto the big drive
```

Before taking Pi-hole down, I turned off **Use Tailscale DNS** on my phones and laptop so their internet kept working during the ~2 minutes it was offline. That's planned maintenance with a workaround.

**Verified nothing was lost:** Pi-hole's upstream was still Unbound (`127.0.0.1#5335`) and its listening mode was still `ALL`, because those settings live in its config on the drive.

Then Jellyfin installed cleanly (~211s download), and the system drive stayed at **82%**.

## What I learned

- **Read the path in the error message.** `/var/lib/containerd` pointed straight at the cause
- A config setting (`data-root`) doesn't always cover everything you think it does. **Verify with `docker info`**
- **Plan maintenance windows:** know what depends on a service before stopping it
- **Separate data from containers** so rebuilds are safe

## Next steps

- [x] Finish the setup wizard: admin account, separate **Movies** (`/media/movies`) and **Shows** (`/media/tv`) libraries. They're separate because Jellyfin organizes and matches metadata differently per content type
- [x] First movie added and playing: *Big Buck Bunny* (open-licensed Blender film), downloaded straight onto the Wyse with `wget` from archive.org (blender.org returned `403 Forbidden` to wget). Gotcha: an alphabet filter ("C") hid it until I hit **Reset filters**
- [ ] Copy my own media with `scp` from my laptop (one line: `scp "<file>" me@<wyse>:/mnt/agents/jellyfin/media/movies/`)
- [ ] Install the Jellyfin app on my phone/TV
- [ ] Reclaim the old containerd image data still sitting on the internal drive
