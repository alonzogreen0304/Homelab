# Unbound: My Own Recursive DNS Resolver

**Beginner level:** still learning, documenting as I go.
**Set up:** October 22, 2026, on the Dell Wyse 5060 alongside Pi-hole
**Project:** [NLnet Labs Unbound](https://github.com/NLnetLabs/unbound)

## What it is (simple version)

Before: when Pi-hole didn't know a website's address, it asked **Google's DNS** (8.8.8.8). Google got a list of every site my devices looked up.

Now: Pi-hole asks **Unbound**, which runs on my own server and asks the **original sources** directly:

```
My phone → Pi-hole (blocks ads) → Unbound → root servers → .com servers → espn.com's own server
```

**Analogy:** instead of calling an operator who looks up the number for you (and remembers who you called), I walk the directory myself: the main index, then the ".com" section, then the actual listing. No single company sees my whole browsing history.

## Why it matters for security

- **Privacy:** no third-party DNS provider (Google, Cloudflare, my ISP) logs every lookup
- **DNSSEC validation:** Unbound checks cryptographic signatures and **rejects forged answers**, which protects against DNS spoofing / cache poisoning
- **No single point of trust:** answers come from the authoritative servers themselves

## My setup

| Setting | Value | Why |
|---|---|---|
| Listens on | `127.0.0.1` port `5335` | Only the Wyse itself can use it; port 53 is Pi-hole's |
| Config file | `/etc/unbound/unbound.conf.d/pi-hole.conf` | Based on the Pi-hole Unbound guide |
| Pi-hole upstream | `127.0.0.1#5335` | Pi-hole forwards to Unbound instead of Google |
| DNSSEC | Hardened (`harden-dnssec-stripped: yes`) | Rejects answers with stripped signatures |
| Prefetch | On | Refreshes popular entries before they expire, so they stay fast |

## Install steps

```bash
# 1. Write config first (127.0.0.1:5335 so it doesn't fight Pi-hole for port 53)
sudo mkdir -p /etc/unbound/unbound.conf.d
sudo tee /etc/unbound/unbound.conf.d/pi-hole.conf   # (config from the Pi-hole Unbound guide)

# 2. Install
sudo apt update && sudo apt install unbound -y

# 3. Stop Unbound from taking over the Wyse's own DNS settings
sudo systemctl disable --now unbound-resolvconf.service
sudo systemctl restart unbound

# 4. Point Pi-hole (Docker, host networking) at Unbound
sudo docker exec pihole pihole-FTL --config dns.upstreams '["127.0.0.1#5335"]'
```

## Testing

| Test | Command | Result |
|---|---|---|
| Unbound answers | `dig pi-hole.net @127.0.0.1 -p 5335` | ✅ `NOERROR`, IP returned. First try timed out while it walked the chain cold; retry answered from cache in **0 ms** |
| DNSSEC valid site | `dig dnssec.works @127.0.0.1 -p 5335` | ✅ `NOERROR` with the **`ad`** flag (Authenticated Data: signature verified) |
| DNSSEC broken site | `dig fail01.dnssec.works @127.0.0.1 -p 5335` | ✅ `SERVFAIL`: Unbound **refused** the deliberately forged answer. DNSSEC protection working |
| Pi-hole upstream | `pihole-FTL --config dns.upstreams` | ✅ `[ 127.0.0.1#5335 ]` |
| Full chain | `dig espn.com @127.0.0.1 +short` | ✅ IPs returned through Pi-hole → Unbound |

## DNSSEC explained

**Simple terms:** websites can sign their DNS records with a digital signature. Unbound checks that signature on every answer. If it matches, Unbound passes the answer along. If it's missing when it should be there, or doesn't match, Unbound **refuses** to answer instead of handing my device a possibly fake address.

**Analogy: a sealed envelope.**
> Imagine asking for someone's address and getting the answer in an envelope with an official wax seal. Unbound is the mail clerk who checks every seal before handing it over.
> - **Seal intact** → "Here you go" (`NOERROR` with the `ad` flag: *Authenticated Data*)
> - **Seal broken or forged** → "I'm not giving you this" (`SERVFAIL`)
>
> Without the clerk, a scammer could slip in a fake envelope pointing me to a fake bank site, and my device would never know.

**My proof:**

| Test | What it is | Result |
|---|---|---|
| `dnssec.works` | Correctly signed site | ✅ `NOERROR` + `ad` flag: seal checked and valid |
| `fail01.dnssec.works` | Deliberately forged signature | ✅ `SERVFAIL`: Unbound refused it |

**What it protects against:** DNS spoofing / cache poisoning, where an attacker tricks a resolver into handing out a fake IP for a real domain.

**What it doesn't do:** DNSSEC proves answers are **authentic**, not **private**. The lookups themselves aren't encrypted. Unbound's privacy win comes from not sending everything to one company.

## What I learned

- **Recursive vs. forwarding DNS:** a forwarder asks someone else; a recursive resolver does the whole lookup itself
- **Caching tradeoff:** the first lookup of a new site is a little slower; repeats are instant
- The **`ad` flag** in `dig` output means the answer was DNSSEC-validated
- Binding a service to `127.0.0.1` keeps it private to the machine. Least exposure
