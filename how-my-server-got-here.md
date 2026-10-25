# How My Server Got Here

**From a $40 used office computer to an always-on home server.**

## What it is (simple version)

The Dell Wyse 5060 was never built to be a server. It's a **thin client**: a small office computer made to be a "dumb screen" that logs into a bigger computer somewhere else. Companies replace them in bulk, so they sell used for cheap. "Server" describes a **job**, not a special machine, so I gave it that job.

> **Analogy:** I bought an old empty storefront and turned it into a restaurant. The building came empty (the hardware). Installing Debian was gutting it and hiring a manager (the OS). Plugging into the network got it a street address. SSH and Tailscale are the back door and a phone line, so I can run it without standing inside. Then the cooks moved in (the programs).

---

## Visual: the journey

```mermaid
flowchart LR
    A["🛒 Used Wyse 5060<br/>thin client, ~$40<br/>Dell's locked-down OS"] --> B["💾 Debian 12 installer<br/>from debian.org"]
    B --> C["🧹 Install Debian<br/>old system wiped<br/>I'm the admin now"]
    C --> D["🔌 Plug into network<br/>ethernet to my<br/>Wi-Fi extender"]
    D --> E["🔑 Remote control<br/>SSH + Tailscale"]
    E --> F["🖥️ Go headless<br/>no monitor or keyboard"]
    F --> G["🐳 Move in the workers<br/>Docker, Pi-hole, Unbound,<br/>Jellyfin, Uptime Kuma,<br/>CrowdSec, Dozzle, Homepage"]
```

## Visual: before vs after

```mermaid
flowchart TB
    subgraph Before["Before: thin client"]
        direction TB
        B1["Monitor + keyboard required"] --> B2["Dell's locked system"]
        B2 --> B3["Only job: log into<br/>someone else's computer"]
    end
    subgraph After["After: my server"]
        direction TB
        A1["No screen (headless)"] --> A2["Debian 12 Linux<br/>(I control everything)"]
        A2 --> A3["Serves my devices 24/7:<br/>ad blocking, DNS, streaming,<br/>monitoring, attack blocking"]
    end
    Before -->|"wipe + install Debian"| After
```

## Visual: how it's connected today

```mermaid
flowchart LR
    W["Wyse server"] -->|"ethernet cable"| X["Wall-plug Wi-Fi extender"]
    X -->|"Wi-Fi"| R["Home router"]
    R --> I["Internet"]
    P["My phone / laptop<br/>(anywhere)"] -->|"Tailscale tunnel"| W
```

> **Analogy:** the extender is a **bridge**. The Wyse speaks "cable," the router speaks "Wi-Fi," and the extender translates between them.

---

## The steps

| # | Step | Why it matters |
|---|---|---|
| 1 | Bought a used Wyse 5060 | Cheap, quiet, low power, so it can run 24/7 |
| 2 | Downloaded Debian 12 from the official site | Trusted source (supply-chain safety) |
| 3 | Booted the installer and installed Debian | Replaced Dell's OS; I'm root/admin now |
| 4 | Connected it by ethernet through my Wi-Fi extender | Other devices can reach it |
| 5 | Set up SSH (key-only) and Tailscale | Control it from my laptop or phone, from anywhere |
| 6 | Unplugged the monitor and keyboard | Runs **headless**, like real data-center servers |
| 7 | Installed Docker and the services | It finally has a job to *serve* |

## Security angle

- **Clean, trusted build:** wiping the old system and installing from the official source means I know what's on the machine. It's the same idea as companies **re-imaging** used laptops before giving them out.
- **Headless + remote:** fewer physical attack points. Access goes through SSH keys and Tailscale, not someone sitting at a keyboard.

## Related notes

- [Wyse boot failure recovery](wyse-boot-recovery.md)
- [First headless boot + remote access](remote-access-log.md)
- [SSH setup](ssh-setup.md) · [Tailscale](tailscale.md)
- Interactive version: [Lab House → "How did my server get there?"](https://alonzogreen0304.github.io/Homelab/games/lab-house.html)
