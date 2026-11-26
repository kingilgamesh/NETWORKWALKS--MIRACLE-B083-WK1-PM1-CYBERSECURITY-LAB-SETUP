# 🛡️ Penetration Testing Report: Web Application & Database Vulnerability Assessment
**W3-PM-FINAL | Cybersecurity Internship | Networkwalks**

| Field | Details |
| :--- | :--- |
| **Pentester Name** | Miracle Orifa (Cybersecurity Professional) |
| **Program / Batch** | B083 – Networkwalks |
| **Date** | 26 November 2026 |
| **Module / Scope** | Web Application Reconnaissance, Enumeration & Exploitation (`medirozahospital.com`) |
| **Client / Target** | `medirozahospital.com` (`199.188.201.16`) – Authorized Lab Environment |
| **Permission Secured?** | Yes |
| **Phases Covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Enumeration<br>Phase 3: Vulnerability Assessment & Exploitation |

---

## 1. Liability Disclaimer
I have performed these activities only on the systems and devices where I had secured written permission or the authorized lab environments provided for my training. All materials in this repository are for educational and research purposes only. Do not use anything from here to break the law. The instructor, the authors, and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility.

---

## 2. Executive Summary & Scope
During Week 3 of my Cybersecurity internship at **Networkwalks**, I conducted an authorized black-box security assessment against the target web application **`medirozahospital.com`** (`199.188.201.16`). The engagement progressed from passive domain reconnaissance and active port scanning to directory brute-forcing, sensitive file discovery, database dump inspection, authentication enumeration, and encrypted document recovery.

---

## 3. Commands Executed

```bash
# 1. Domain Registration & WHOIS Footprinting
whois medirozahospital.com

# 2. ICMP Connectivity Check & SYN Stealth Port Scan (Top 1000 Ports)
ping -c 25 medirozahospital.com
nmap 199.188.201.16 -vvv

# 3. Directory & File Brute-Force Enumeration
gobuster dir -u [https://medirozahospital.com](https://medirozahospital.com) -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,pdf,zip,bak

# 4. Inspecting Exposed Paths & Backup Files via Curl / Browser
curl -I [https://medirozahospital.com/robots.txt](https://medirozahospital.com/robots.txt)
curl -O [https://medirozahospital.com/old/mediroza_db_backup_2019.sql](https://medirozahospital.com/old/mediroza_db_backup_2019.sql)
