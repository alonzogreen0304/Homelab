# Wyse Safety Net: Running Pi-hole for the Whole House

**Beginner level:** still learning, documenting as I go.

Once my router points its DNS at Pi-hole, every device in the house depends on the Wyse to load websites. That makes the Wyse **production**: if it goes down, the house loses internet. This page covers what I do and don't do on it, and how to set up (and undo) each safety net.

---

## Production rules: what runs where

The Wyse isn't banned from learning. The rule is: **if it could break networking, DNS, or booting, it goes on a VM on my PC instead.**

### Safe on the Wyse

| Activity | Why it's safe |
|---|---|
| Reading logs, `docker ps`, `systemctl status`, `df -h` | Read-only, changes nothing |
| Practicing basic Linux commands in my home folder | Doesn't touch system config |
| Pi-hole blocklists, whitelists, dashboard settings | Built for this; easy to undo |
| Tailscale settings | Doesn't affect local DNS |
| Small, light Docker containers (e.g. an uptime monitor or dashboard) | Isolated, as long as they don't use ports 53 or 80 |
| Planned updates (`sudo apt upgrade`) | Needed for security; do them when nobody's using the internet |

### Keep these OFF the Wyse (do them on PC VMs)

| Activity | Why |
|---|---|
| Editing `/etc/network/interfaces`, DNS, or IP settings | One typo = whole house offline |
| Firewall experiments (`iptables`, `nftables`, `ufw`) | Easy to block DNS by accident |
| Editing `/etc/fstab`, GRUB, or kernel settings | Can stop it from booting (been there) |
| Kali, attack tools, scanning, malware samples | Never run risky tools on the box the house depends on |
| Wazuh, VMs, or other heavy software | Not enough RAM/storage; slows down DNS |
| Containers that use port 53 or 80 | Fights with Pi-hole for the same port |
| Frequent reboots or unplugging the external drive | Every reboot = a short outage for everyone |

If I have to make a risky change on the Wyse anyway, I do it late at night, back up the file first, and have the undo steps ready.

---

## Safety net 1: DHCP reservation (do this first)

**What it does:** makes sure the Wyse always gets the same local IP. If its IP changed, every device would be pointing at a dead address.

**Set it up**
1. Find the router address on the Wyse: `ip route` (the IP after `default via`).
2. Open that IP in a browser and log in (login is usually on the router sticker).
3. Find **DHCP Reservation** / **Address Reservation** / **Static Lease** (usually under LAN or DHCP settings).
4. Pick the Wyse from the connected devices list (or match its MAC address from `ip a`) and reserve its current IP.
5. Save.

**Undo:** delete the reservation in the same menu.

---

## Safety net 2: Know the rollback before changing router DNS

**What it does:** gets the house back online in about a minute if the Wyse ever dies.

**Before changing anything**
1. Screenshot the router's current DNS settings (LAN/DHCP section).
2. Save the router login in a password manager.

**Set Pi-hole as DNS**
1. In the router's **LAN / DHCP** settings (not WAN/Internet), set **Primary DNS** to the Wyse's local IP.
2. Leave **Secondary DNS** blank. Adding `8.8.8.8` lets devices skip Pi-hole.
3. Save, then reconnect devices (toggle Wi-Fi / restart the TV).

**Undo (emergency rollback)**
1. Log into the router.
2. Set DNS back to **Automatic** / ISP default (or what's in the screenshot).
3. Save, then reconnect devices. Internet works again without the Wyse.

---

## Safety net 3: Start small (per-device DNS)

**What it does:** tests Pi-hole on one or two devices before the whole house. If it breaks, only those devices are affected.

**iPhone**
1. Settings → Wi-Fi → tap the **(i)** next to your network.
2. **Configure DNS** → **Manual**.
3. Delete the existing entry, add the Wyse's local IP, tap **Save**.

**Undo:** same screen → **Automatic** → Save.

**Smart TV** (menus vary by brand)
1. Settings → Network → network status / IP settings.
2. Change DNS from **Automatic** to **Manual** and enter the Wyse's local IP.

**Undo:** set DNS back to **Automatic**.

---

## Safety net 4: Auto power-on after an outage (BIOS)

**What it does:** the Wyse turns itself back on when power returns, instead of staying off until someone presses the button.

**Status:** ⬜ To do. Needs a monitor + keyboard one time.

**Why it matters:** everything *after* boot already comes back on its own (Docker `restart: unless-stopped`, Docker waits for the big drive, Tailscale/SSH/Unbound/CrowdSec start at boot, `allow-hotplug` ethernet). But if the Wyse never powers on, none of that runs. Right now it's a lamp that stays off after a blackout until someone flips the switch.

**Set it up**
1. Reboot the Wyse and tap **Delete** over and over right away to enter the BIOS (if that doesn't work, try **F2**).
2. If it asks for a BIOS password, Dell's default for Wyse thin clients is commonly `Fireport`.
3. Go to **Power Management** → **AC Recovery** (name may vary slightly).
4. Set it to **Power On** (or **Last Power State**).
5. Apply / Save and Exit.

**Undo:** same menu → set **AC Recovery** to **Power Off**.

---

## Safety net 5: Make sure Pi-hole restarts on its own

**What it does:** Docker automatically starts Pi-hole after a reboot or crash.

**Check it** (every running container at once)
```bash
sudo docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' $(sudo docker ps -q)
```
`unless-stopped` or `always` = good. `no` = it won't come back by itself.

**Fix it**
```bash
sudo docker update --restart unless-stopped pihole
```

**Undo**
```bash
sudo docker update --restart no pihole
```

**Quick fixes if Pi-hole acts up**
- Restart it: `sudo docker restart pihole`
- Pause ad blocking without breaking DNS: Pi-hole dashboard → **Disable blocking** (pick a time). **Undo:** **Enable blocking**.

---

## Safety net 6: Back up Pi-hole settings

**What it does:** saves blocklists and settings so I can restore them if the Wyse or its drive dies.

**Back up**
1. Pi-hole dashboard → **Settings** → **Teleporter**.
2. Click **Export** / **Backup** and save the file somewhere off the Wyse (PC or cloud).
3. Repeat after any big change.

**Restore**
1. Same page → **Import** / **Restore** → choose the backup file.

---

## Safety net 7: Battery backup (UPS)

**What it does:** keeps the Wyse (and ideally the router + modem) running through short power blips and outages.

**Status:** ⬜ To do. I already own an **APC Back-UPS 350**; it just needs to be wired in.

**Set it up**
1. Plug the **Wyse and the Wi-Fi extender** (and router + modem if they reach) into the **battery** outlets, not the surge-only ones.
2. Short flickers: the Wyse never notices. Long outages: the battery runs out, then **Safety net 4 (AC Recovery)** powers the Wyse back on when power returns. The two work together.
3. **Later:** if the UPS has a USB data port, install `apcupsd` so the Wyse **shuts down cleanly** before the battery dies, so a hard power cut can't corrupt files.

**How I'll know recovery worked:** [Uptime Kuma](uptime-kuma.md) sends **UP** alerts to my phone as each service comes back.

**Undo:** plug them back into the wall.

---

## Safety net 8: Fallback kernel

**What it does:** if an update breaks booting, I can boot the previous kernel (this saved me during the [boot recovery](wyse-boot-recovery.md)).

**Use it**
1. Reboot and tap **Esc** (or hold **Shift**) to open the GRUB menu.
2. **Advanced options** → pick the older kernel (not recovery mode).
3. Once booted, fix the new one, then reboot normally.

**Rule:** don't remove old kernels manually. Let `apt` manage them.

---

## What I learned

- Once a service is critical, it needs **change control**: plan changes, back up first, know the rollback
- **Separating production from lab** is a real security and IT practice
- Every safety net has an **undo**. If I can't undo a change, I shouldn't make it on production
