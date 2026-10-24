# Cybersecurity Acronyms Cheat Sheet

**Beginner level:** the acronyms I keep running into while studying for Security+ (SY0-701) and building my homelab, with plain-English meanings.

**158 acronyms** across 10 categories. Practice them with the [Acronym Breach game](https://alonzogreen0304.github.io/Homelab/games/acronym-breach.html).

## Categories

- [Networking](#networking)
- [Homelab & Systems](#homelab--systems)
- [Security Basics](#security-basics)
- [Identity & Access](#identity--access)
- [Crypto & PKI](#crypto--pki)
- [Threats & Attacks](#threats--attacks)
- [SOC & Defense](#soc--defense)
- [Incident Response & Continuity](#incident-response--continuity)
- [GRC & Compliance](#grc--compliance)
- [Cloud & Modern IT](#cloud--modern-it)

---

## Networking

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **DNS** | Domain Name System | The internet's phone book: turns names like google.com into IP addresses. Pi-hole is a DNS server. | You type espn.com; DNS answers 199.181.132.250. Pi-hole, Google 8.8.8.8, Cloudflare 1.1.1.1. |
| **DHCP** | Dynamic Host Configuration Protocol | Hands out IP addresses to devices automatically. Your router does this. | Your Comcast router handing your phone 10.0.0.23 when it joins Wi-Fi. |
| **IP** | Internet Protocol | The addressing system every device on a network uses. | 192.168.1.20 (private, at home) or 100.119.39.63 (the Wyse's Tailscale IP). |
| **LAN** | Local Area Network | Your home or office network. | Your phone, TV, PS5 and the Wyse all on your home Wi-Fi. |
| **WAN** | Wide Area Network | Networks spread over large areas; the internet side of your router. | The Comcast cable from your router out to the internet. |
| **VPN** | Virtual Private Network | An encrypted tunnel between devices or networks. Tailscale is a VPN. | Tailscale, NordVPN, or a company's Cisco AnyConnect. |
| **NAT** | Network Address Translation | Lets many private devices share one public IP address. | 10 devices at home all show up online as one Comcast IP. |
| **MAC** | Media Access Control (address) | A hardware ID burned into every network card. | A hardware ID like 3C:22:FB:9A:10:4E burned into a network card. |
| **TCP** | Transmission Control Protocol | Reliable, connection-based delivery: checks every packet arrives. | Loading a web page or an SSH session: every packet is confirmed. |
| **UDP** | User Datagram Protocol | Fast, connectionless delivery with no guarantees. Used by DNS and streaming. | A Zoom call or an online game: speed over perfection. |
| **ICMP** | Internet Control Message Protocol | Used for network diagnostics. ping uses it. | ping 1.1.1.1 and traceroute both use ICMP. |
| **HTTP** | Hypertext Transfer Protocol | How web pages are sent. Unencrypted, port 80. | http://100.119.39.63:3000: your Homepage over plain HTTP. |
| **HTTPS** | Hypertext Transfer Protocol Secure | HTTP encrypted with TLS. Port 443. | https://github.com, with the padlock in the address bar. |
| **SSH** | Secure Shell | Encrypted remote command-line access. Port 22. | ssh me@100.119.39.63 from your laptop or Termius. |
| **RDP** | Remote Desktop Protocol | Microsoft's remote desktop. Port 3389, a big attack target. | Windows Remote Desktop Connection on port 3389. |
| **FTP** | File Transfer Protocol | Old, unencrypted file transfer. Ports 20/21. | FileZilla connecting to an old web host on port 21. |
| **SFTP** | SSH File Transfer Protocol | Secure file transfer over SSH. | WinSCP or FileZilla in SFTP mode, over port 22. |
| **SMB** | Server Message Block | Windows file and printer sharing. Port 445. | A Windows shared folder like \\server\files on port 445. |
| **SMTP** | Simple Mail Transfer Protocol | Sends email. Port 25. | Gmail's servers handing your email to Outlook's servers. |
| **IMAP** | Internet Message Access Protocol | Reads email while keeping it on the server. | The iPhone Mail app syncing your Gmail inbox. |
| **SNMP** | Simple Network Management Protocol | Monitors and manages network devices. | A monitoring tool asking a switch how busy its ports are. |
| **NTP** | Network Time Protocol | Keeps clocks in sync. Accurate time matters for logs. | Your phone setting its clock from time.apple.com. |
| **LDAP** | Lightweight Directory Access Protocol | Queries directories of users and computers, like Active Directory. | A company login page checking your username in Active Directory. |
| **VLAN** | Virtual Local Area Network | Splits one physical network into separate logical networks. | VLAN 10 = your PCs, VLAN 20 = smart TVs and IoT, VLAN 30 = guests. |
| **SSID** | Service Set Identifier | The name of a Wi-Fi network. | "MySpectrumWiFi" or "xfinitywifi" in the Wi-Fi list. |
| **ISP** | Internet Service Provider | The company that sells you internet access. | Comcast (home internet), T-Mobile (phone data). |
| **SDN** | Software-Defined Networking | Network control managed by software instead of individual devices. | A UniFi controller changing every switch from one app. |

---

## Homelab & Systems

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **OS** | Operating System | The core software running a computer: Windows, Linux, macOS. | Windows 11, macOS, iOS, Debian 12 on the Wyse. |
| **CLI** | Command-Line Interface | Controlling a computer by typing commands. | PowerShell on your laptop, bash on the Wyse. |
| **GUI** | Graphical User Interface | Windows, icons and a mouse. XFCE is a GUI. | Windows desktop, the XFCE desktop, any app with buttons. |
| **VM** | Virtual Machine | A full computer running inside another computer. | Kali Linux running inside VirtualBox on your gaming PC. |
| **BIOS** | Basic Input/Output System | Firmware that starts the hardware before the OS loads. | The blue setup screen you get by tapping Delete at boot. |
| **UEFI** | Unified Extensible Firmware Interface | The modern replacement for BIOS. Supports Secure Boot. | The modern BIOS screen on any PC from the last 10 years. |
| **GRUB** | GRand Unified Bootloader | The Linux boot menu where you pick a kernel. | The boot menu that let you pick the older Debian kernel. |
| **TTY** | Teletypewriter | A text console in Linux (Ctrl+Alt+F2 and friends). | The black text login (Ctrl+Alt+F3) with no desktop. |
| **RAM** | Random Access Memory | Fast, temporary working memory. Cleared on power off. | The Wyse's memory: about 1.5GB free on your dashboard. |
| **SSD** | Solid-State Drive | Fast storage with no moving parts. | Your laptop's drive, or the Wyse's small internal flash. |
| **NAS** | Network-Attached Storage | A storage box shared over the network. | A Synology box sharing files to every device at home. |
| **UPS** | Uninterruptible Power Supply | A battery backup that keeps gear running during outages. | Your APC Back-UPS 350. |
| **PDU** | Power Distribution Unit | A rack-mounted power strip. | The power strip that mounts inside a server rack. |
| **TPM** | Trusted Platform Module | A security chip that stores encryption keys in hardware. | The security chip Windows 11 requires; it stores BitLocker keys. |
| **HSM** | Hardware Security Module | A dedicated device that protects and manages crypto keys. | A locked hardware box at a bank that holds signing keys. |
| **RAID** | Redundant Array of Independent Disks | Combines drives for speed, redundancy, or both. | Two drives mirrored (RAID 1) in a NAS: one dies, data survives. |
| **VDI** | Virtual Desktop Infrastructure | Desktops hosted on servers and accessed remotely. | Logging into a work Windows desktop that runs in a data center. |

---

## Security Basics

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **CIA** | Confidentiality, Integrity, Availability | The three core goals of security: secret, unchanged, reachable. | Leaked passwords (C), a changed bank balance (I), a site knocked offline (A). |
| **AAA** | Authentication, Authorization, Accounting | Prove who you are, what you can do, and log what you did. | Log in (authN), allowed into the HR folder (authZ), logged (accounting). |
| **PII** | Personally Identifiable Information | Data that identifies a person: name, SSN, address. | Your name, SSN, address, date of birth, or email. |
| **PHI** | Protected Health Information | Health data covered by HIPAA. | Your diagnosis or prescription linked to your name at a clinic. |
| **BYOD** | Bring Your Own Device | Employees using personal devices for work. | Checking work email on your personal iPhone. |
| **MDM** | Mobile Device Management | Centrally controls and secures phones and tablets. | Microsoft Intune or Jamf managing company phones. |
| **AUP** | Acceptable Use Policy | Rules for how people may use company systems. | "Don't use work laptops for personal crypto mining." |
| **EOL** | End of Life | A product that no longer gets updates or security patches. | Windows 10 after Oct 2025: no more security updates. |
| **IoT** | Internet of Things | Smart devices: cameras, TVs, thermostats. Often poorly secured. | Ring doorbells, smart plugs, smart TVs. |
| **ICS** | Industrial Control System | Systems that run physical processes like factories and power plants. | Machines controlling a factory line or a water plant. |
| **SCADA** | Supervisory Control and Data Acquisition | A type of ICS for monitoring and controlling infrastructure. | The control room software for a power grid. |

---

## Identity & Access

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **MFA** | Multi-Factor Authentication | Two or more proofs: something you know, have, or are. | Password + a code from your phone + Face ID. |
| **2FA** | Two-Factor Authentication | MFA with exactly two factors. | Instagram login + a 6-digit code from an app. |
| **SSO** | Single Sign-On | Log in once, access many apps. | "Sign in with Microsoft" opening Teams, Outlook and Canvas. |
| **IAM** | Identity and Access Management | Managing who users are and what they can access. | Okta or Microsoft Entra ID managing every employee account. |
| **PAM** | Privileged Access Management | Locks down and monitors admin accounts. | CyberArk checking out the admin password for 1 hour. |
| **RBAC** | Role-Based Access Control | Permissions assigned by job role. | "Nurse" role can view charts; "Billing" role can't. |
| **ABAC** | Attribute-Based Access Control | Permissions based on attributes like time, location, or department. | Access allowed only from a company laptop, in the US, 9 to 5. |
| **DAC** | Discretionary Access Control | The owner of a file decides who can access it. | chmod 600 on your .env file: the owner decides who gets in. |
| **SAML** | Security Assertion Markup Language | An XML standard for SSO between identity providers and apps. | Your school login page that lets you into Canvas. |
| **OAuth** | Open Authorization | Lets an app access your data without your password ("Sign in with Google"). | "Sign in with Google" on a new app. |
| **RADIUS** | Remote Authentication Dial-In User Service | Central authentication for network access, like enterprise Wi-Fi. | Typing your school username and password to join campus Wi-Fi. |
| **TACACS+** | Terminal Access Controller Access-Control System Plus | Cisco's AAA protocol for managing network device logins. | Network admins logging into Cisco routers. |
| **EAP** | Extensible Authentication Protocol | A framework for authentication methods on networks. | The login method under the hood of campus/work Wi-Fi. |
| **OTP** | One-Time Password | A code that works only once, like an authenticator app code. | A 6-digit code your bank texts you. |
| **TOTP** | Time-based One-Time Password | A code that changes every 30 seconds. | Google Authenticator codes that change every 30 seconds. |

---

## Crypto & PKI

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **PKI** | Public Key Infrastructure | The system of certificates and authorities that makes HTTPS trust work. | The system of CAs that makes the padlock on https sites trustworthy. |
| **CA** | Certificate Authority | A trusted organization that issues digital certificates. | Let's Encrypt, DigiCert. |
| **CSR** | Certificate Signing Request | What you send a CA to get a certificate. | The file you send Let's Encrypt to ask for a certificate. |
| **CRL** | Certificate Revocation List | A list of certificates that are no longer trusted. | A published list of certificates that are no longer trusted. |
| **OCSP** | Online Certificate Status Protocol | Checks one certificate's revocation status in real time. | Your browser checking "is this cert still valid?" in real time. |
| **TLS** | Transport Layer Security | The encryption behind HTTPS. Replaced SSL. | What protects https sites and email in transit. |
| **SSL** | Secure Sockets Layer | TLS's insecure predecessor. Deprecated. | The old version of TLS; "SSL certificate" is a leftover name. |
| **AES** | Advanced Encryption Standard | The standard symmetric encryption algorithm. | BitLocker and Wi-Fi WPA2/WPA3 encrypt with AES. |
| **RSA** | Rivest-Shamir-Adleman | A widely used asymmetric (public/private key) algorithm. | Older SSH keys (ssh-rsa) and many website certificates. |
| **ECC** | Elliptic Curve Cryptography | Asymmetric crypto with smaller keys than RSA. Great for mobile. | Your ED25519 SSH keys use elliptic curves. |
| **SHA** | Secure Hash Algorithm | A family of hash functions used to check integrity. | SHA-256 checksums on Linux download pages. |
| **MD5** | Message Digest 5 | An old hash function. Broken; don't use it for security. | Old checksums; broken for security, still used for quick file checks. |
| **HMAC** | Hash-based Message Authentication Code | A hash plus a secret key: proves integrity and authenticity. | How an API checks a webhook request really came from GitHub. |
| **IPsec** | Internet Protocol Security | Encrypts traffic at the IP layer. Common in site-to-site VPNs. | A tunnel linking a company's two offices. |
| **WPA3** | Wi-Fi Protected Access 3 | The current Wi-Fi security standard. | The security setting on a modern home router's Wi-Fi. |
| **FDE** | Full Disk Encryption | Encrypts an entire drive, like BitLocker. | BitLocker (Windows) or FileVault (Mac). |

---

## Threats & Attacks

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **APT** | Advanced Persistent Threat | A skilled, well-funded attacker who stays hidden long-term. | APT29 (Cozy Bear), APT41: nation-state hacking groups. |
| **DoS** | Denial of Service | Overloading a system so real users can't reach it. | Flooding one website from one machine until it crashes. |
| **DDoS** | Distributed Denial of Service | A DoS attack from many machines at once, like a botnet. | A botnet of 100,000 hacked cameras flooding a game server. |
| **MITM** | Man-in-the-Middle (on-path attack) | An attacker secretly sits between two parties and intercepts traffic. | A fake "Free Airport Wi-Fi" reading your traffic. |
| **XSS** | Cross-Site Scripting | Injecting malicious scripts into a website other users load. | A comment box that runs a hacker's script in every visitor's browser. |
| **CSRF** | Cross-Site Request Forgery | Tricks a logged-in user's browser into making unwanted requests. | A hidden link that makes your logged-in bank transfer money. |
| **SQLi** | SQL Injection | Injecting database commands through input fields. | Typing ' OR 1=1 -- into a login box to skip the password. |
| **RCE** | Remote Code Execution | An attacker can run their own code on your system remotely. | Log4Shell (2021): one text string let attackers run code on servers. |
| **BEC** | Business Email Compromise | Email fraud impersonating executives or vendors to steal money. | A fake "CEO" email asking finance to wire $50K today. |
| **RAT** | Remote Access Trojan | Malware that gives an attacker remote control. | Malware that lets a hacker watch your webcam and control your PC. |
| **PUP** | Potentially Unwanted Program | Junk software bundled with installs, like adware. | A toolbar or "PC cleaner" bundled into a free installer. |
| **CVE** | Common Vulnerabilities and Exposures | The public ID system for known vulnerabilities (CVE-2024-1234). | CVE-2021-44228 (Log4Shell), CVE-2024-3094 (xz backdoor). |
| **CVSS** | Common Vulnerability Scoring System | Rates how severe a vulnerability is, 0 to 10. | Log4Shell scored 10.0, the maximum. |
| **IoC** | Indicator of Compromise | Evidence of a breach: a bad IP, file hash, or domain. | A malicious IP, a bad file hash, or a weird domain in your logs. |
| **TTP** | Tactics, Techniques, and Procedures | How an attacker operates. MITRE ATT&CK catalogs these. | Phishing for access, then using PowerShell to move around. |
| **OSINT** | Open-Source Intelligence | Info gathered from public sources like social media and search. | Googling a target, checking LinkedIn, SpiderFoot scans. |
| **0-day** | Zero-Day | A vulnerability with no patch available yet. | A bug attackers use before the vendor even knows about it. |

---

## SOC & Defense

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **SOC** | Security Operations Center | The team that monitors and responds to threats 24/7. Your target job. | The 24/7 team watching alerts, the job you're aiming for. |
| **SIEM** | Security Information and Event Management | Collects and correlates logs to find threats. Splunk, Wazuh. | Splunk, Microsoft Sentinel, Wazuh. |
| **SOAR** | Security Orchestration, Automation, and Response | Automates SOC playbooks and responses. | Splunk SOAR auto-blocking an IP after a phishing alert. |
| **EDR** | Endpoint Detection and Response | Monitors and responds to threats on individual devices. | CrowdStrike Falcon, Microsoft Defender for Endpoint. |
| **XDR** | Extended Detection and Response | EDR expanded across endpoints, network, cloud and email. | Palo Alto Cortex XDR tying endpoint + network + email together. |
| **IDS** | Intrusion Detection System | Detects and alerts on suspicious activity. Doesn't block. | Snort or Suricata watching traffic; CrowdSec's detection engine. |
| **IPS** | Intrusion Prevention System | Detects AND blocks suspicious activity. | CrowdSec's firewall bouncer banning an attacker's IP. |
| **HIDS** | Host-based Intrusion Detection System | An IDS running on a single device. | A Wazuh agent on a server watching its own logs and files. |
| **NIDS** | Network-based Intrusion Detection System | An IDS watching network traffic. | Suricata watching all traffic on a network segment. |
| **WAF** | Web Application Firewall | Filters web traffic to block attacks like XSS and SQLi. | Cloudflare WAF blocking SQL injection against a website. |
| **NGFW** | Next-Generation Firewall | A firewall that understands apps and users, not just ports. | Palo Alto, Fortinet FortiGate. |
| **UTM** | Unified Threat Management | One box doing firewall, IPS, antivirus and filtering. | A Sophos box doing firewall + antivirus + web filter. |
| **DLP** | Data Loss Prevention | Stops sensitive data from leaving the organization. | Blocking an email that contains 50 credit card numbers. |
| **ACL** | Access Control List | Rules that allow or deny traffic or access. | "Allow laptop to Wyse port 22, deny everything else." |
| **DMZ** | Demilitarized Zone (screened subnet) | A buffer network for public-facing servers. | A company's public web server sitting between two firewalls. |
| **NAC** | Network Access Control | Checks devices before letting them on the network. | An unknown laptop plugged into the office gets quarantined. |

---

## Incident Response & Continuity

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **IR** | Incident Response | The process of handling a security incident. | Your Wyse boot-failure write-up: detect, fix, document. |
| **IRP** | Incident Response Plan | The written playbook for handling incidents. | A playbook: "ransomware hits? Step 1, isolate the machine." |
| **DFIR** | Digital Forensics and Incident Response | Investigating what happened and cleaning it up. | Imaging a hacked laptop's drive before wiping it. |
| **BCP** | Business Continuity Plan | How the business keeps running during a disruption. | "If the office floods, staff work from home with laptops." |
| **DRP** | Disaster Recovery Plan | How to restore IT systems after a disaster. | "Restore servers from backup in the cloud within 8 hours." |
| **BIA** | Business Impact Analysis | Figures out which systems matter most and the cost of losing them. | "Payroll down 1 day costs $10K, 1 week costs $200K." |
| **RTO** | Recovery Time Objective | Max acceptable downtime before a system must be back. | "The website must be back within 4 hours." |
| **RPO** | Recovery Point Objective | Max acceptable data loss, measured in time. | "We can lose at most 1 hour of data" = backups every hour. |
| **MTTR** | Mean Time to Repair/Recover | Average time to fix something after it breaks. | Pi-hole down at 9:00, fixed at 9:20: MTTR 20 minutes. |
| **MTBF** | Mean Time Between Failures | Average time a system runs before failing. | A drive rated for 1,000,000 hours between failures. |
| **MTTD** | Mean Time to Detect | Average time to notice an incident. | Uptime Kuma got your MTTD down to about 2 minutes. |

---

## GRC & Compliance

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **GRC** | Governance, Risk, and Compliance | Managing policies, risk, and regulations. Your other career path. | Writing policies, running risk assessments, passing audits. |
| **NIST** | National Institute of Standards and Technology | US agency that publishes key security frameworks. | NIST SP 800-53, the NIST Cybersecurity Framework. |
| **CSF** | Cybersecurity Framework | NIST's framework: Govern, Identify, Protect, Detect, Respond, Recover. | NIST CSF 2.0: Govern, Identify, Protect, Detect, Respond, Recover. |
| **RMF** | Risk Management Framework | NIST's process for managing risk in systems. | The 7-step process federal agencies use to approve a system. |
| **CIS** | Center for Internet Security | Publishes the CIS Controls and secure configuration benchmarks. | CIS Controls, CIS Benchmarks for hardening Debian or Windows. |
| **ISO** | International Organization for Standardization | Publishes standards like ISO 27001 for security management. | ISO 27001 certification for a company's security program. |
| **PCI DSS** | Payment Card Industry Data Security Standard | Required security rules for handling credit cards. | Rules every store that takes credit cards must follow. |
| **HIPAA** | Health Insurance Portability and Accountability Act | US law protecting health information. | Why your doctor's office can't post your records online. |
| **GDPR** | General Data Protection Regulation | EU privacy law. Applies to anyone handling EU residents' data. | EU privacy law behind all those cookie consent pop-ups. |
| **SLA** | Service-Level Agreement | A contract defining expected service levels, like uptime. | "99.9% uptime or you get a refund" from a cloud provider. |
| **NDA** | Non-Disclosure Agreement | A contract to keep information confidential. | Signing that you won't share a company's secrets. |
| **MOU** | Memorandum of Understanding | An informal agreement between parties. | Two organizations agreeing to share threat intel, not legally binding. |
| **SOW** | Statement of Work | Defines exactly what work a vendor will deliver. | A pentest contract: which IPs, what dates, what's off-limits. |
| **SLE** | Single Loss Expectancy | Cost of one incident: asset value x exposure factor. | One stolen laptop = $5,000 loss. |
| **ARO** | Annualized Rate of Occurrence | How many times per year an incident is expected. | Laptops stolen 3 times a year = ARO of 3. |
| **ALE** | Annualized Loss Expectancy | Expected yearly loss: SLE x ARO. | SLE $5,000 x ARO 3 = ALE $15,000 a year. |
| **KRI** | Key Risk Indicator | A metric that warns risk is increasing. | "Unpatched critical servers" rising from 2 to 20. |

---

## Cloud & Modern IT

| Acronym | Stands for | What it means | Example |
|---|---|---|---|
| **IaaS** | Infrastructure as a Service | Rent servers and networks in the cloud (AWS EC2). | AWS EC2, Azure virtual machines. |
| **PaaS** | Platform as a Service | Rent a platform to run your code without managing servers. | Heroku, Azure App Service, Google App Engine. |
| **SaaS** | Software as a Service | Use software over the internet (Gmail, Office 365). | Gmail, Microsoft 365, Canvas, Spotify. |
| **CASB** | Cloud Access Security Broker | Sits between users and cloud apps to enforce security policy. | Microsoft Defender for Cloud Apps blocking uploads to personal Dropbox. |
| **SASE** | Secure Access Service Edge | Networking and security delivered together from the cloud. | Zscaler or Cloudflare One securing staff wherever they work. |
| **ZTNA** | Zero Trust Network Access | Never trust, always verify, for every connection. | Tailscale: every device proves who it is before it can connect. |
| **IaC** | Infrastructure as Code | Defining servers and networks in code files (Terraform). | Your docker-compose.yml files; Terraform at bigger companies. |
| **API** | Application Programming Interface | How programs talk to each other. | The Pi-hole API key your Homepage widget uses for stats. |
| **CI/CD** | Continuous Integration / Continuous Delivery | Automatically testing and shipping code changes. | GitHub Actions testing and deploying code on every push. |
| **SDLC** | Software Development Life Cycle | The stages of building software, from planning to retirement. | Plan, design, build, test, deploy, maintain. |
| **SBOM** | Software Bill of Materials | A list of every component inside a piece of software. | A list of every library inside an app, like an ingredients label. |

---

## Study tips

- Learn them in categories, not alphabetically: they stick better with context
- Say the full name out loud, then explain it in your own words
- Pay extra attention to look-alikes: IDS vs IPS, RTO vs RPO, SLE vs ALE, EDR vs XDR, SSL vs TLS
- Connect them to the homelab: DNS = Pi-hole, VPN = Tailscale, SSH = laptop access, SIEM = Wazuh (coming soon)
