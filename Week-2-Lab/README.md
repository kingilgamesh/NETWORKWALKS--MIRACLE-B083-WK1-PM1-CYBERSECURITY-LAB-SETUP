# 🛡️ Penetration Testing Report: Footprinting & Network Scanning Phases
**W2-PM-FINAL | Cybersecurity Internship | Networkwalks**

| Field | Details |
| :--- | :--- |
| **Pentester Name** | Miracle Orifa (Cybersecurity Professional) |
| **Program / Batch** | B083 – Networkwalks |
| **Date** | 13 November 2026 |
| **Modules Completed** | W2-PM1 (Multiple Kali Tools) & W2-PM5 (Zenmap Scanning) |
| **Client / Target** | 1. `networkwalks.com` (Written permission secured)<br>2. Local LAN Network (`192.168.0.0/24`) |
| **Permission Secured?** | Yes |
| **Phases Covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phases 3–5: In Progress |

---

## 1. Liability Disclaimer
I have performed these activities only on the systems and devices where I had secured written permission or the devices/systems that I own myself[cite: 9]. All these materials are for educational and research purposes only[cite: 9]. Do not use anything from here to break the law[cite: 9]. The instructor, the authors, and Networkwalks are not responsible for what you do with this knowledge[cite: 9]. Every action you take is your own responsibility[cite: 9]. Misuse can lead to criminal charges, heavy fines, loss of your job, and a permanent record[cite: 9]. In most countries, unauthorized access is a crime even when nothing is damaged[cite: 9].

---

## 2. Introduction
This report covers footprinting the `networkwalks.com` domain using multiple Kali Linux tools (**W2-PM1**) and scanning my own local network with Zenmap (**W2-PM5**)[cite: 9]. Together, these modules demonstrate how a security analyst moves from gathering publicly available OSINT/infrastructure data to discovering live hosts on an internal network[cite: 9]. This completes the Week 2 milestone of my ongoing Cybersecurity internship at **Networkwalks**[cite: 9].

---

## 3. Tools Used

| Tool | Purpose |
| :--- | :--- |
| **Kali Linux & Windows** | Operating systems used for reconnaissance and local scanning activities[cite: 9]. |
| **WHOIS** | Find domain registration details (registrar, dates, name servers)[cite: 9, 15]. |
| **WhatWeb** | Fingerprint web technologies (server, CMS, plugins, IP address)[cite: 9, 14]. |
| **nslookup** | Resolve the domain name to its IPv4 address via DNS[cite: 9, 12]. |
| **curl -I** | Inspect HTTP response headers and exposed API endpoints[cite: 9, 10]. |
| **wafw00f** | Detect and fingerprint any Web Application Firewall (WAF) protecting the site[cite: 9, 13]. |
| **dnsrecon** | Enumerate DNS records (A, MX, TXT, SPF, SRV)[cite: 9, 11]. |
| **Zenmap (Nmap GUI)** | Perform ping sweeps on the local subnet to identify live hosts, IPs, and MAC addresses[cite: 9, 16]. |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance (`networkwalks.com`)
* **WHOIS (`whois networkwalks.com`):** Identified registrar details (`GoDaddy.com, LLC`), domain creation date (`2019-11-06`), expiry date (`2027-11-06`), and HostGator name servers (`NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM`)[cite: 15].
* **WhatWeb (`whatweb networkwalks.com`):** Fingerprinted the web stack running on Apache, Bootstrap `7.0.4`, jQuery `3.7.1`, **WordPress `7.1.4`**, and **WordPress Download Manager `3.3.76`** at IP `192.232.216.135`[cite: 14].
* **Nslookup (`nslookup networkwalks.com`):** Resolved `networkwalks.com` to IPv4 address **`192.232.216.135`**[cite: 12].
* **Curl (`curl -I https://networkwalks.com`):** Inspected HTTP response headers, confirming `Apache` web server, `x-nginx-cache: WordPress`, secure `HttpOnly` cookies, and the exposed WordPress REST API link header (`/wp-json/`)[cite: 10].
* **Wafw00f (`wafw00f networkwalks.com`):** Confirmed that `https://networkwalks.com` is protected behind **ModSecurity (SpiderLabs) WAF**[cite: 13].
* **DNSRecon (`dnsrecon -d networkwalks.com`):** Enumerated `A` and `MX` records pointing to `192.232.216.135` (`mail.networkwalks.com`) along with Google site verification and SPF (`v=spf1`) TXT records[cite: 11].

### 4.2 Network Scanning with Zenmap (`192.168.0.0/24`)
* Executed a Ping Scan (`nmap -sn 192.168.0.0/24`) across my local LAN subnet[cite: 16].
* Scanned 256 IP addresses in 8.31 seconds and discovered **3 active hosts**[cite: 16]:
  * `192.168.0.1` — Up (`0.023s` latency) | MAC: `B8:D4:BC:C1:B6:C6` (ZTE)[cite: 16]
  * `192.168.0.2` — Up (Local scanning host)[cite: 16]
  * `192.168.0.3` — Up (`1.1s` latency) | MAC: `16:C1:48:E0:FE:F2`[cite: 16]

---

## 5. Risk Analysis & Observations

| # | Observation / Finding | Evidence | Potential Impact | Risk Level |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Web CMS & plugin versions exposed | WhatWeb identified WordPress `7.1.4` and WP Download Manager `3.3.76`[cite: 14] | Version disclosure helps attackers check for known CVEs if patching lags[cite: 9] | Medium |
| 2 | Origin IP address identifiable | `nslookup` and `dnsrecon` resolved `192.232.216.135`[cite: 11, 12] | Exposes hosting location and infrastructure details[cite: 9] | Low |
| 3 | HTTP headers & REST API exposed | `curl -I` returned server headers and `/wp-json/` endpoint[cite: 10] | Assists attackers in technology fingerprinting and user/route enumeration[cite: 9] | Low |
| 4 | WAF technology identifiable | `wafw00f` identified ModSecurity (SpiderLabs)[cite: 13] | Reveals defensive architecture (though active WAF provides strong protection)[cite: 9] | Low |
| 5 | DNS & mail records enumerated | `dnsrecon` exposed `MX` and `TXT` (SPF) records[cite: 11] | Helps map mail infrastructure and domain verification records[cite: 9] | Medium |
| 6 | Multiple live hosts on local LAN | Zenmap discovered 3 active hosts on `192.168.0.0/24`[cite: 16] | Unmonitored or rogue devices on a LAN increase lateral movement risk[cite: 9] | Medium |

> **Note:** These findings represent passive/active reconnaissance observations from authorized educational labs, not exploited vulnerabilities[cite: 9].

---

## 6. Security Recommendations
1. **Minimize Banner & Version Disclosure:** Configure Apache and WordPress to suppress generator meta tags and detailed server headers[cite: 9].
2. **Maintain Regular Patch Cycles:** Keep WordPress core and plugins (such as WP Download Manager) updated against the latest security advisories[cite: 9].
3. **Restrict Unnecessary REST API Endpoints:** Limit public access to `/wp-json/` routes if anonymous API access is not required[cite: 9].
4. **Keep WAF Active & Tuned:** Maintain ModSecurity rulesets to filter automated scanning and web-layer attacks[cite: 9].
5. **Periodic Internal Network Audits:** Regularly scan local subnets to verify all connected MAC/IP addresses and flag unauthorized devices[cite: 9].

---

## 7. Evidences Collected

### 1. WHOIS Lookup
![WHOIS Screenshot](Whois%20screenshot.jpg)

### 2. WhatWeb Fingerprinting
![WhatWeb Screenshot](whatweb%20screenshots.jpg)

### 3. Nslookup DNS Resolution
![Nslookup Screenshot](nslookup%20screenshots.png)

### 4. Curl HTTP Header Inspection
![Curl Screenshot](curl%20screenshots.jpg)

### 5. Wafw00f WAF Detection
![Wafw00f Screenshot](wa00f%20screenshots.jpg)

### 6. DNSRecon Enumeration
![DNSRecon Screenshot](dns%20screenshot.jpg)

### 7. Zenmap Local Network Scan
![Zenmap Screenshot](zenmap%20results.PNG)

---

## 👤 Author & Mentorship
* **Author:** Miracle Orifa (Batch B083)
* **Instructor:** Sir Waqas Karim CCIE | [LinkedIn](https://www.linkedin.com/in/waqaskarim/)[cite: 9]
* **Program:** Cybersecurity Internship at **Networkwalks** | Week 02[cite: 9]
