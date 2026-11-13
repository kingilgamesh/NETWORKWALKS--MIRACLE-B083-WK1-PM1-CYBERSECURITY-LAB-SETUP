# 🛡️ Week 2: Footprinting & Network Scanning Lab (`W2-PM-FINAL`)

**Author:** Miracle Orifa (Orifa Miracle Oyimafu)  
**Program / Batch:** Cybersecurity Internship — Batch B083 (`Networkwalks`)  
**Instructor:** Sir Waqas Karim CCIE  
**Date:** 13 November 2026  

---

## ⚠️ Liability Disclaimer

> I have performed these activities only on the systems and devices where I had secured written permission or the devices/systems that I own myself. All materials in this repository are for **educational and research purposes only**. Unauthorized access or scanning of networks without explicit permission is illegal. Every action you take is your own responsibility.

---

## 📌 Project Overview

This repository documents my **Week 2** practical cybersecurity lab work for **Networkwalks**, covering the first two phases of penetration testing:
1. **Phase 1 — Reconnaissance & Footprinting (`W2-PM1`):** Gathering domain, DNS, HTTP header, technology stack, and WAF intelligence on `networkwalks.com` (with written permission) using Kali Linux command-line tools.
2. **Phase 2 — Scanning & Network Discovery (`W2-PM5`):** Mapping active hosts, latency, and MAC addresses on my local LAN subnet (`192.168.0.0/24`) using Zenmap (Nmap GUI).

---

## 🛠️ Tools & Environment

| Tool / Platform | Purpose |
| :--- | :--- |
| **Kali Linux (VirtualBox) & Windows** | Isolated lab environment and host OS for reconnaissance and scanning |
| `whois` | Domain registration, registrar, creation/expiry dates, and name server lookup |
| `whatweb` | Web application stack, CMS, plugin, and server fingerprinting |
| `nslookup` | DNS resolution to map domain names to IPv4 addresses |
| `curl -I` | HTTP response header inspection and API endpoint discovery |
| `wafw00f` | Web Application Firewall (WAF) detection and fingerprinting |
| `dnsrecon` | Comprehensive DNS record enumeration (`A`, `MX`, `TXT`, `SPF`, `SRV`) |
| **Zenmap (`nmap -sn`)** | Ping sweep host discovery across the local subnet |

---

## 🔍 Summary of Execution & Findings

### 1. Domain Footprinting (`networkwalks.com`)

| Command Executed | Key Observations |
| :--- | :--- |
| `whois networkwalks.com` | Registrar: **GoDaddy.com, LLC** \| Created: `2019-11-06` \| Expires: `2027-11-06` \| Name Servers: `NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM` \| DNSSEC: `unsigned` |
| `whatweb networkwalks.com` | Web Server: **Apache** \| IP: `192.232.216.135` (US) \| CMS/Stack: **WordPress 7.1.4**, **WP Download Manager 3.3.76**, Bootstrap 7.1.4, jQuery 3.7.1 |
| `nslookup networkwalks.com` | Resolved `networkwalks.com` to **`192.232.216.135`** |
| `curl -I https://networkwalks.com` | Status: `HTTP/1.1 200 OK` \| Server: `Apache` \| Cache: `x-nginx-cache: WordPress` \| Exposed REST API link: `/wp-json/` |
| `wafw00f networkwalks.com` | Detected active WAF protection: **ModSecurity (SpiderLabs)** |
| `dnsrecon -d networkwalks.com` | Enumerated `A` (`192.232.216.135`), `MX` (`mail.networkwalks.com`), Google site-verification `TXT`, and `SPF` record (`ip4:50.87.144.87`) |

### 2. Local Network Discovery (`192.168.0.0/24`)

* **Command:** `nmap -sn 192.168.0.0/24` (Zenmap Ping Scan)
* **Result:** Scanned 256 IP addresses in `8.31 seconds` and identified **3 live hosts**:
  * `192.168.0.1` — Up (`0.023s` latency) | MAC: `B8:D4:BC:C1:B6:C6` *(ZTE Gateway)*
  * `192.168.0.2` — Up *(Local Scanning Host)*
  * `192.168.0.3` — Up (`1.1s` latency) | MAC: `16:C1:48:E0:FE:F2` *(Client Device)*

---

## 📊 Risk Summary & Key Takeaways

* **Information Disclosure (Medium/Low):** Publicly accessible metadata (`WhatWeb`, `curl -I`, `dnsrecon`) exposes CMS/plugin versions (`WordPress 7.1.4`), origin IP (`192.232.216.135`), and `/wp-json/` endpoints. Suppressing version headers and enabling DNSSEC helps reduce the external reconnaissance footprint.
* **Active Perimeter Defense:** `wafw00f` confirmed **ModSecurity (SpiderLabs)** is actively filtering traffic, providing a strong defensive layer against common web attacks.
* **Network Visibility:** Regular local subnet sweeps (`192.168.0.0/24`) ensure all connected endpoints and MAC addresses are accounted for.

---

## 📸 Screenshots & Lab Evidence

> *Note: Ensure all screenshot filenames below match the uploaded images in this repository folder.*

1. **WHOIS Lookup:** `Whois screenshot.jpg`
2. **WhatWeb Fingerprinting:** `whatweb screenshots.jpg`
3. **Nslookup Resolution:** `nslookup screenshots.png`
4. **Curl HTTP Headers:** `curl screenshots.jpg`
5. **Wafw00f WAF Detection:** `wa00f screenshots.jpg`
6. **DNSRecon Enumeration:** `dns screenshot.jpg`
7. **Zenmap Ping Scan Results:** `zenmap results.PNG`

---

## 📂 Repository Contents

* `W2-PM-FINAL-Miracle-Orifa.pdf` — Full Week 2 Penetration Testing Report
* `README.md` — Project overview, commands, findings, and evidence index
* Lab evidence screenshots (`*.jpg` / `*.png` / `*.PNG`)