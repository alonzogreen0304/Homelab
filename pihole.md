# Pi-hole

**Beginner level:** still learning, documenting as I go.

## What it is (simple version)

Every time a device visits a website or loads an ad, it first asks a **DNS server** "what's the address for this name?" Think of DNS as the internet's phone book.

**Pi-hole is my own phone book.** When a device asks for a known ad or tracking domain, Pi-hole just doesn't answer, so the ad never loads. Normal sites work like usual.

Because it works at the network level, it can block ads on devices where you can't install an ad blocker, like smart TVs and game consoles.

## My setup

- **Runs on:** Dell Wyse 5060 (Debian 12)
- **Installed with:** Docker (`pihole/pihole` image)
- **Network mode:** host (uses the server's network directly)
- **Blocklist size:** ~82,000 domains
- **Dashboard:** `http://<server-ip>/admin`

## Useful commands

```bash
sudo docker ps                                   # is Pi-hole running? look for "Up" and "(healthy)"
sudo docker restart pihole                       # restart it
sudo docker exec -it pihole pihole setpassword   # reset the dashboard password
sudo ss -tulpn | grep :53                        # confirm something is answering DNS (port 53)
```

## Current status

- ✅ Running and healthy, starts automatically on boot
- ⚠️ Only the server itself is using it so far
- 🔜 Next: set my router's DNS to Pi-hole so every device on Wi-Fi is covered

## What I learned

- DNS runs on **port 53**
- If Pi-hole is your only DNS server and it goes down, the whole house loses internet, so the server needs to be reliable
- Don't add a backup DNS like `8.8.8.8` on the router, or devices will skip Pi-hole
- It **can't** block YouTube ads, because those come from the same servers as the videos
