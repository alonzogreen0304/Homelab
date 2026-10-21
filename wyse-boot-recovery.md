# Homelab Log: Dell Wyse 5060 Boot Failure Recovery

**Date:** October 20, 2026
**Device:** Dell Wyse 5060 thin client (homelab server)
**OS:** Debian GNU/Linux 12 (Bookworm)
**Role:** Always-on box for Docker, Pi-hole, Tailscale
**Outcome:** Restored to a working state with a graphical desktop (XFCE)

---

## Summary

My Wyse server stopped booting properly and only showed a black screen or text console. I troubleshot it hands-on and found **four separate problems stacked on top of each other**: a missing external drive breaking boot, no desktop environment installed, the ethernet port not set to start at boot, and a kernel update that left the new kernel unbootable. I fixed all four permanently: the server now boots cleanly on the latest kernel, straight to a desktop, with networking up automatically.

---

## Symptoms

- After boot, only a blinking cursor or a text login, no desktop
- Boot messages showed `/dev/sda1: recovering journal` (filesystem recovering from an unclean shutdown)
- System dropped into **emergency mode**: `Give root password for maintenance`
- After updates: `Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)`

---

## Troubleshooting and Fixes

### Issue 1: Emergency mode from a missing drive

**Investigation**
```bash
systemctl --failed     # check failed services
cat /etc/fstab         # check auto-mount config
lsblk                  # list connected drives
```
Boot log showed: `Timed out waiting for device ... /mnt/agents`

**Root cause:** `/etc/fstab` was set to mount an external 1.8TB drive at `/mnt/agents` on every boot. When the drive wasn't plugged in, systemd treated it as a critical failure and dropped into emergency mode.

**Fix**
- Plugged in the correct external drive
- Added the `nofail` option so the system boots with or without the drive:
```bash
cp /etc/fstab /etc/fstab.bak
nano /etc/fstab
# changed:  defaults  →  defaults,nofail
systemctl daemon-reload
```

### Issue 2: Server offline (no network)

**Investigation**
```bash
ping -c 3 1.1.1.1     # result: Network is unreachable
ip a                  # ethernet port enp4s0 showed state DOWN, no IP
cat /etc/network/interfaces
```

**Root cause:** `/etc/network/interfaces` only configured the loopback interface. Nothing told the ethernet port (`enp4s0`) to come up at boot, so the server never got an IP. That also broke DNS and package updates.

**Fix**
```bash
ip link set enp4s0 up       # bring port up manually
dhclient enp4s0             # request IP from router via DHCP
```
Made it permanent by adding to `/etc/network/interfaces`:
```
auto enp4s0
iface enp4s0 inet dhcp
```

### Issue 3: No desktop (text only)

**Investigation**
```bash
systemctl get-default        # graphical.target
ls /usr/share/xsessions      # No such file or directory
```

**Root cause:** The system was set to boot into graphical mode, but no desktop environment was installed, so it fell back to text.

**Fix:** Installed XFCE, a lightweight desktop suited to the Wyse's limited 7.5GB drive.
```bash
apt update
apt install xfce4 lightdm -y
```

### Issue 4: Kernel panic after updates

**Symptom:** `Kernel panic - VFS: Unable to mount root fs` on new kernel `6.1.0-54`

**Root cause:** The system update installed a new kernel, but its initramfs (the startup file that loads drivers needed to find the disk) was missing or incomplete.

**Recovery:** Opened the GRUB boot menu, then booted the previous working kernel (`6.1.0-44`). The system came up with the new XFCE desktop.

**Permanent fix:** From the working kernel, finished the interrupted update and rebuilt the new kernel's initramfs, then refreshed the boot menu:
```bash
dpkg --configure -a
update-initramfs -u -k 6.1.0-54-amd64
update-grub
```
The `W: Possible missing firmware ... amdgpu` warnings during the rebuild were harmless, since they refer to GPUs not present in this hardware. Rebooted on the default `6.1.0-54` kernel and it booted cleanly to the desktop.

Also installed `sudo` and added my user to the `sudo` group so admin tasks no longer require logging in as root.

---

## Key Takeaways

- **Read the error, don't guess.** Every fix came from an actual log line or command output.
- **Back up config files before editing.** I copied `fstab`, `interfaces`, and `resolv.conf` before every change.
- **Removable drives need `nofail` in fstab**, or one unplugged USB can stop the whole server from booting.
- **Never hard power-off a Linux server.** Use `sudo shutdown now`. Unclean shutdowns caused the journal recovery.
- **Keep old kernels.** Having a fallback kernel in GRUB is what saved this recovery.
- **Troubleshoot in layers:** storage → network → DNS → services → GUI.

---

## Skills Demonstrated

- Linux boot troubleshooting (systemd emergency mode, GRUB, kernel selection)
- Filesystem and mount configuration (`/etc/fstab`, UUIDs, `lsblk`, `df`)
- Network troubleshooting (`ip a`, `ping`, DHCP, DNS, `/etc/network/interfaces`)
- Package management (`apt`, `dpkg`) and root/privilege management (`su`, `sudo`)
- Root cause analysis and incident documentation

---

## Next Steps

- [x] Rebuild initramfs for kernel `6.1.0-54`
- [x] Install `sudo` for non-root admin
- [ ] Confirm all security updates applied (`sudo apt update && sudo apt upgrade`)
- [ ] Set up SSH with key-based auth and disable password login
- [ ] Verify Pi-hole, Docker, and Tailscale are running after the recovery
- [ ] Assign a static IP / DHCP reservation for the server
