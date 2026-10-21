# Cybersecurity Acronyms Cheat Sheet

**Beginner level:** the acronyms I keep running into while studying for Security+ (SY0-701) and building my homelab, with plain-English meanings.

**158 acronyms** across 10 categories. Practice them with the [Acronym Breach game](games/acronym-breach.html).

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

| Acronym | Stands for | What it means |
|---|---|---|
| **DNS** | Domain Name System | The internet's phone book: turns names like google.com into IP addresses. Pi-hole is a DNS server. |
| **DHCP** | Dynamic Host Configuration Protocol | Hands out IP addresses to devices automatically. Your router does this. |
| **IP** | Internet Protocol | The addressing system every device on a network uses. |
| **LAN** | Local Area Network | Your home or office network. |
| **WAN** | Wide Area Network | Networks spread over large areas; the internet side of your router. |
| **VPN** | Virtual Private Network | An encrypted tunnel between devices or networks. Tailscale is a VPN. |
| **NAT** | Network Address Translation | Lets many private devices share one public IP address. |
| **MAC** | Media Access Control (address) | A hardware ID burned into every network card. |
| **TCP** | Transmission Control Protocol | Reliable, connection-based delivery: checks every packet arrives. |
| **UDP** | User Datagram Protocol | Fast, connectionless delivery with no guarantees. Used by DNS and streaming. |
| **ICMP** | Internet Control Message Protocol | Used for network diagnostics. ping uses it. |
| **HTTP** | Hypertext Transfer Protocol | How web pages are sent. Unencrypted, port 80. |
| **HTTPS** | Hypertext Transfer Protocol Secure | HTTP encrypted with TLS. Port 443. |
| **SSH** | Secure Shell | Encrypted remote command-line access. Port 22. |
| **RDP** | Remote Desktop Protocol | Microsoft's remote desktop. Port 3389, a big attack target. |
| **FTP** | File Transfer Protocol | Old, unencrypted file transfer. Ports 20/21. |
| **SFTP** | SSH File Transfer Protocol | Secure file transfer over SSH. |
| **SMB** | Server Message Block | Windows file and printer sharing. Port 445. |
| **SMTP** | Simple Mail Transfer Protocol | Sends email. Port 25. |
| **IMAP** | Internet Message Access Protocol | Reads email while keeping it on the server. |
| **SNMP** | Simple Network Management Protocol | Monitors and manages network devices. |
| **NTP** | Network Time Protocol | Keeps clocks in sync. Accurate time matters for logs. |
| **LDAP** | Lightweight Directory Access Protocol | Queries directories of users and computers, like Active Directory. |
| **VLAN** | Virtual Local Area Network | Splits one physical network into separate logical networks. |
| **SSID** | Service Set Identifier | The name of a Wi-Fi network. |
| **ISP** | Internet Service Provider | The company that sells you internet access. |
| **SDN** | Software-Defined Networking | Network control managed by software instead of individual devices. |

---

## Homelab & Systems

| Acronym | Stands for | What it means |
|---|---|---|
| **OS** | Operating System | The core software running a computer: Windows, Linux, macOS. |
| **CLI** | Command-Line Interface | Controlling a computer by typing commands. |
| **GUI** | Graphical User Interface | Windows, icons and a mouse. XFCE is a GUI. |
| **VM** | Virtual Machine | A full computer running inside another computer. |
| **BIOS** | Basic Input/Output System | Firmware that starts the hardware before the OS loads. |
| **UEFI** | Unified Extensible Firmware Interface | The modern replacement for BIOS. Supports Secure Boot. |
| **GRUB** | GRand Unified Bootloader | The Linux boot menu where you pick a kernel. |
| **TTY** | Teletypewriter | A text console in Linux (Ctrl+Alt+F2 and friends). |
| **RAM** | Random Access Memory | Fast, temporary working memory. Cleared on power off. |
| **SSD** | Solid-State Drive | Fast storage with no moving parts. |
| **NAS** | Network-Attached Storage | A storage box shared over the network. |
| **UPS** | Uninterruptible Power Supply | A battery backup that keeps gear running during outages. |
| **PDU** | Power Distribution Unit | A rack-mounted power strip. |
| **TPM** | Trusted Platform Module | A security chip that stores encryption keys in hardware. |
| **HSM** | Hardware Security Module | A dedicated device that protects and manages crypto keys. |
| **RAID** | Redundant Array of Independent Disks | Combines drives for speed, redundancy, or both. |
| **VDI** | Virtual Desktop Infrastructure | Desktops hosted on servers and accessed remotely. |

---

## Security Basics

| Acronym | Stands for | What it means |
|---|---|---|
| **CIA** | Confidentiality, Integrity, Availability | The three core goals of security: secret, unchanged, reachable. |
| **AAA** | Authentication, Authorization, Accounting | Prove who you are, what you can do, and log what you did. |
| **PII** | Personally Identifiable Information | Data that identifies a person: name, SSN, address. |
| **PHI** | Protected Health Information | Health data covered by HIPAA. |
| **BYOD** | Bring Your Own Device | Employees using personal devices for work. |
| **MDM** | Mobile Device Management | Centrally controls and secures phones and tablets. |
| **AUP** | Acceptable Use Policy | Rules for how people may use company systems. |
| **EOL** | End of Life | A product that no longer gets updates or security patches. |
| **IoT** | Internet of Things | Smart devices: cameras, TVs, thermostats. Often poorly secured. |
| **ICS** | Industrial Control System | Systems that run physical processes like factories and power plants. |
| **SCADA** | Supervisory Control and Data Acquisition | A type of ICS for monitoring and controlling infrastructure. |

---

## Identity & Access

| Acronym | Stands for | What it means |
|---|---|---|
| **MFA** | Multi-Factor Authentication | Two or more proofs: something you know, have, or are. |
| **2FA** | Two-Factor Authentication | MFA with exactly two factors. |
| **SSO** | Single Sign-On | Log in once, access many apps. |
| **IAM** | Identity and Access Management | Managing who users are and what they can access. |
| **PAM** | Privileged Access Management | Locks down and monitors admin accounts. |
| **RBAC** | Role-Based Access Control | Permissions assigned by job role. |
| **ABAC** | Attribute-Based Access Control | Permissions based on attributes like time, location, or department. |
| **DAC** | Discretionary Access Control | The owner of a file decides who can access it. |
| **SAML** | Security Assertion Markup Language | An XML standard for SSO between identity providers and apps. |
| **OAuth** | Open Authorization | Lets an app access your data without your password ("Sign in with Google"). |
| **RADIUS** | Remote Authentication Dial-In User Service | Central authentication for network access, like enterprise Wi-Fi. |
| **TACACS+** | Terminal Access Controller Access-Control System Plus | Cisco's AAA protocol for managing network device logins. |
| **EAP** | Extensible Authentication Protocol | A framework for authentication methods on networks. |
| **OTP** | One-Time Password | A code that works only once, like an authenticator app code. |
| **TOTP** | Time-based One-Time Password | A code that changes every 30 seconds. |

---

## Crypto & PKI

| Acronym | Stands for | What it means |
|---|---|---|
| **PKI** | Public Key Infrastructure | The system of certificates and authorities that makes HTTPS trust work. |
| **CA** | Certificate Authority | A trusted organization that issues digital certificates. |
| **CSR** | Certificate Signing Request | What you send a CA to get a certificate. |
| **CRL** | Certificate Revocation List | A list of certificates that are no longer trusted. |
| **OCSP** | Online Certificate Status Protocol | Checks one certificate's revocation status in real time. |
| **TLS** | Transport Layer Security | The encryption behind HTTPS. Replaced SSL. |
| **SSL** | Secure Sockets Layer | TLS's insecure predecessor. Deprecated. |
| **AES** | Advanced Encryption Standard | The standard symmetric encryption algorithm. |
| **RSA** | Rivest-Shamir-Adleman | A widely used asymmetric (public/private key) algorithm. |
| **ECC** | Elliptic Curve Cryptography | Asymmetric crypto with smaller keys than RSA. Great for mobile. |
| **SHA** | Secure Hash Algorithm | A family of hash functions used to check integrity. |
| **MD5** | Message Digest 5 | An old hash function. Broken; don't use it for security. |
| **HMAC** | Hash-based Message Authentication Code | A hash plus a secret key: proves integrity and authenticity. |
| **IPsec** | Internet Protocol Security | Encrypts traffic at the IP layer. Common in site-to-site VPNs. |
| **WPA3** | Wi-Fi Protected Access 3 | The current Wi-Fi security standard. |
| **FDE** | Full Disk Encryption | Encrypts an entire drive, like BitLocker. |

---

## Threats & Attacks

| Acronym | Stands for | What it means |
|---|---|---|
| **APT** | Advanced Persistent Threat | A skilled, well-funded attacker who stays hidden long-term. |
| **DoS** | Denial of Service | Overloading a system so real users can't reach it. |
| **DDoS** | Distributed Denial of Service | A DoS attack from many machines at once, like a botnet. |
| **MITM** | Man-in-the-Middle (on-path attack) | An attacker secretly sits between two parties and intercepts traffic. |
| **XSS** | Cross-Site Scripting | Injecting malicious scripts into a website other users load. |
| **CSRF** | Cross-Site Request Forgery | Tricks a logged-in user's browser into making unwanted requests. |
| **SQLi** | SQL Injection | Injecting database commands through input fields. |
| **RCE** | Remote Code Execution | An attacker can run their own code on your system remotely. |
| **BEC** | Business Email Compromise | Email fraud impersonating executives or vendors to steal money. |
| **RAT** | Remote Access Trojan | Malware that gives an attacker remote control. |
| **PUP** | Potentially Unwanted Program | Junk software bundled with installs, like adware. |
| **CVE** | Common Vulnerabilities and Exposures | The public ID system for known vulnerabilities (CVE-2024-1234). |
| **CVSS** | Common Vulnerability Scoring System | Rates how severe a vulnerability is, 0 to 10. |
| **IoC** | Indicator of Compromise | Evidence of a breach: a bad IP, file hash, or domain. |
| **TTP** | Tactics, Techniques, and Procedures | How an attacker operates. MITRE ATT&CK catalogs these. |
| **OSINT** | Open-Source Intelligence | Info gathered from public sources like social media and search. |
| **0-day** | Zero-Day | A vulnerability with no patch available yet. |

---

## SOC & Defense

| Acronym | Stands for | What it means |
|---|---|---|
| **SOC** | Security Operations Center | The team that monitors and responds to threats 24/7. Your target job. |
| **SIEM** | Security Information and Event Management | Collects and correlates logs to find threats. Splunk, Wazuh. |
| **SOAR** | Security Orchestration, Automation, and Response | Automates SOC playbooks and responses. |
| **EDR** | Endpoint Detection and Response | Monitors and responds to threats on individual devices. |
| **XDR** | Extended Detection and Response | EDR expanded across endpoints, network, cloud and email. |
| **IDS** | Intrusion Detection System | Detects and alerts on suspicious activity. Doesn't block. |
| **IPS** | Intrusion Prevention System | Detects AND blocks suspicious activity. |
| **HIDS** | Host-based Intrusion Detection System | An IDS running on a single device. |
| **NIDS** | Network-based Intrusion Detection System | An IDS watching network traffic. |
| **WAF** | Web Application Firewall | Filters web traffic to block attacks like XSS and SQLi. |
| **NGFW** | Next-Generation Firewall | A firewall that understands apps and users, not just ports. |
| **UTM** | Unified Threat Management | One box doing firewall, IPS, antivirus and filtering. |
| **DLP** | Data Loss Prevention | Stops sensitive data from leaving the organization. |
| **ACL** | Access Control List | Rules that allow or deny traffic or access. |
| **DMZ** | Demilitarized Zone (screened subnet) | A buffer network for public-facing servers. |
| **NAC** | Network Access Control | Checks devices before letting them on the network. |

---

## Incident Response & Continuity

| Acronym | Stands for | What it means |
|---|---|---|
| **IR** | Incident Response | The process of handling a security incident. |
| **IRP** | Incident Response Plan | The written playbook for handling incidents. |
| **DFIR** | Digital Forensics and Incident Response | Investigating what happened and cleaning it up. |
| **BCP** | Business Continuity Plan | How the business keeps running during a disruption. |
| **DRP** | Disaster Recovery Plan | How to restore IT systems after a disaster. |
| **BIA** | Business Impact Analysis | Figures out which systems matter most and the cost of losing them. |
| **RTO** | Recovery Time Objective | Max acceptable downtime before a system must be back. |
| **RPO** | Recovery Point Objective | Max acceptable data loss, measured in time. |
| **MTTR** | Mean Time to Repair/Recover | Average time to fix something after it breaks. |
| **MTBF** | Mean Time Between Failures | Average time a system runs before failing. |
| **MTTD** | Mean Time to Detect | Average time to notice an incident. |

---

## GRC & Compliance

| Acronym | Stands for | What it means |
|---|---|---|
| **GRC** | Governance, Risk, and Compliance | Managing policies, risk, and regulations. Your other career path. |
| **NIST** | National Institute of Standards and Technology | US agency that publishes key security frameworks. |
| **CSF** | Cybersecurity Framework | NIST's framework: Govern, Identify, Protect, Detect, Respond, Recover. |
| **RMF** | Risk Management Framework | NIST's process for managing risk in systems. |
| **CIS** | Center for Internet Security | Publishes the CIS Controls and secure configuration benchmarks. |
| **ISO** | International Organization for Standardization | Publishes standards like ISO 27001 for security management. |
| **PCI DSS** | Payment Card Industry Data Security Standard | Required security rules for handling credit cards. |
| **HIPAA** | Health Insurance Portability and Accountability Act | US law protecting health information. |
| **GDPR** | General Data Protection Regulation | EU privacy law. Applies to anyone handling EU residents' data. |
| **SLA** | Service-Level Agreement | A contract defining expected service levels, like uptime. |
| **NDA** | Non-Disclosure Agreement | A contract to keep information confidential. |
| **MOU** | Memorandum of Understanding | An informal agreement between parties. |
| **SOW** | Statement of Work | Defines exactly what work a vendor will deliver. |
| **SLE** | Single Loss Expectancy | Cost of one incident: asset value x exposure factor. |
| **ARO** | Annualized Rate of Occurrence | How many times per year an incident is expected. |
| **ALE** | Annualized Loss Expectancy | Expected yearly loss: SLE x ARO. |
| **KRI** | Key Risk Indicator | A metric that warns risk is increasing. |

---

## Cloud & Modern IT

| Acronym | Stands for | What it means |
|---|---|---|
| **IaaS** | Infrastructure as a Service | Rent servers and networks in the cloud (AWS EC2). |
| **PaaS** | Platform as a Service | Rent a platform to run your code without managing servers. |
| **SaaS** | Software as a Service | Use software over the internet (Gmail, Office 365). |
| **CASB** | Cloud Access Security Broker | Sits between users and cloud apps to enforce security policy. |
| **SASE** | Secure Access Service Edge | Networking and security delivered together from the cloud. |
| **ZTNA** | Zero Trust Network Access | Never trust, always verify, for every connection. |
| **IaC** | Infrastructure as Code | Defining servers and networks in code files (Terraform). |
| **API** | Application Programming Interface | How programs talk to each other. |
| **CI/CD** | Continuous Integration / Continuous Delivery | Automatically testing and shipping code changes. |
| **SDLC** | Software Development Life Cycle | The stages of building software, from planning to retirement. |
| **SBOM** | Software Bill of Materials | A list of every component inside a piece of software. |

---

## Study tips

- Learn them in categories, not alphabetically: they stick better with context
- Say the full name out loud, then explain it in your own words
- Pay extra attention to look-alikes: IDS vs IPS, RTO vs RPO, SLE vs ALE, EDR vs XDR, SSL vs TLS
- Connect them to the homelab: DNS = Pi-hole, VPN = Tailscale, SSH = laptop access, SIEM = Wazuh (coming soon)
