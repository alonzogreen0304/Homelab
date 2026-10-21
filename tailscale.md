# Tailscale

**Beginner level:** still learning, documenting as I go.

## What it is (simple version)

Tailscale creates a **private, encrypted network** between my devices, no matter where they are. My phone on cellular data can reach my home server like it's sitting next to it.

It's a VPN, but much easier than a traditional one. No opening ports on my router and no complicated setup. Every device logs in with my account, gets its own private IP (starting with `100.`), and they can all talk to each other securely.

## Why it matters for security

- **No port forwarding:** I don't expose my home server to the open internet, which is a big attack surface reduction
- **Encrypted:** traffic between devices uses WireGuard encryption
- **Identity-based access:** only devices logged into my account can join

## My setup

- **Server:** Dell Wyse 5060 is connected to my tailnet
- **Exit node:** the server is set up as an exit node, so my phone can route its traffic through my home network on public Wi-Fi
- **Other devices:** a few phones (some are old and need to be removed)

## Useful commands

```bash
sudo tailscale status    # list devices and whether they're online
sudo tailscale up        # connect (or reconnect) the server
sudo tailscale ip        # show this device's Tailscale IP
```

## Current status

- ✅ Server connected and online
- ✅ Reached Pi-hole from my phone through Tailscale ([log](remote-access-log.md))
- ⚠️ Exit node kept **off** on my phone until forwarding is fixed. When it was on and the Wyse was offline, my phone's internet was blocked
- ⚠️ Health check warning: **IP forwarding is disabled**, so the exit node feature won't actually work yet
- 🔜 Next: enable IP forwarding in Linux
- 🔜 Next: remove old devices from the Tailscale admin console (asset inventory cleanup)

## What I learned

- "Idle" in `tailscale status` just means no traffic right now, not broken
- "Offline, last seen Xd ago" means that device isn't running Tailscale
- Old, unused devices on your network are a security risk, so you should regularly review what's connected. That's **asset inventory**, a core security control
