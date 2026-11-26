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
gobuster dir -u https://medirozahospital.com -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,pdf,zip,bak

# 4. Inspecting Exposed Paths & Backup Files via Curl / Browser
curl -I https://medirozahospital.com/robots.txt
curl -O https://medirozahospital.com/old/mediroza_db_backup_2019.sql
```

---

## 4. Key Findings & Exploitation Chain

### 4.1 Reconnaissance & Port Scanning
* **WHOIS Lookup:** Confirmed domain registration through `NAMECHEAP INC` (created `2026-08-14`, expiring `2027-08-14`), hosted on Namecheap name servers (`dns1.namecheaphosting.com`, `dns2.namecheaphosting.com`) with privacy protection enabled.
* **Nmap Scan (`nmap 199.188.201.16 -vvv`):** Identified target host `server274-1.web-hosting.com` (`199.188.201.16`) as live with **12 open TCP ports**: `21` (FTP), `26` (RSFTP), `53` (DNS), `80` (HTTP), `110` (POP3), `143` (IMAP), `443` (HTTPS), `465` (SMTPS), `587` (Submission), `993` (IMAPS), `995` (POP3S), and `20/tcp` closed.

### 4.2 Directory Enumeration & `robots.txt` Disclosure
* **Gobuster Enumeration:** Discovered `200 OK` and `301 Redirect` paths including `/about.html`, `/contact.html`, `/index.html`, `/wp-admin`, `/wp-login.php`, `/old/`, `/staff/`, `/patient/`, `/bandwidth/`, and `/mailman/`.
* **`robots.txt` Misconfiguration:** Inspecting `[https://medirozahospital.com/robots.txt](https://medirozahospital.com/robots.txt)` explicitly revealed restricted directories (`Disallow: /patient/`, `Disallow: /staff/`, `Disallow: /old/`) to any external visitor.

### 4.3 Critical Exposure: Unprotected SQL Database Backup & Directory Listing
* **Open Directory Indexing:** Navigating to `/old/`, `/staff/`, and `/patient/` revealed full directory listings enabled on LiteSpeed Web Server.
* **Full Database Dump (`mediroza_db_backup_2019.sql`):** Inside `/old/`, an unauthenticated backup file (`mediroza_db_backup_2019.sql`) was publicly downloadable. Inspecting the SQL dump exposed:
  * **30 Hospital Staff Records (`staff` table):** Full names, job titles, departments, email addresses, phone numbers, South African National ID numbers, monthly salaries (ZAR), and hire dates.
  * **10 Shareholder Records (`shareholders` table):** Shareholder names, ownership percentages, shares held, and share classes.

### 4.4 Patient Portal Access, Username Enumeration & PDF Cracking
* **Username Enumeration (`patient/login.php`):** Testing login inputs revealed distinct error messages—returning *"Incorrect password"* for valid usernames (e.g., `admin`) versus *"Username not found"* for invalid ones (e.g., `Naledi`)—allowing attackers to enumerate valid accounts.
* **Unauthenticated Lab Reports & Password Cracking:** Accessing `/patient/portal.php` exposed three confidential Pathology Laboratory Reports (`patient_report_1.pdf`, `patient_report_2.pdf`, `patient_report_3.pdf`) protected with weak PDF passwords. Using dictionary/hash recovery tools (`Networkwalks Password Cracker` and `OnlineHashCrack`), all three PDF hashes (`$pdf$2*3*128...`) were cracked: 1. **Report 1 (`LR-2024-1187` – Sipho Dlamini):** Cracked password **`123456`** 2. **Report 2 (`LR-2024-1192` – Priya Reddy):** Cracked password **`password`** 3. **Report 3 (`LR-2024-1205` – Emily Thompson):** Cracked password **`!@#$%^&`**

---

## 5. Risk Summary & Recommendations

| # | Vulnerability / Finding | Severity | Remediation |
| :--- | :--- | :--- | :--- |
| 1 | Publicly accessible SQL database backup (`/old/mediroza_db_backup_2019.sql`) | **Critical** | Remove all `.sql`, `.bak`, and legacy backup files from public web roots immediately. |
| 2 | Unauthenticated access to Patient Portal & PII/PHI lab reports | **Critical** | Enforce strict server-side session authentication checks on `portal.php` and `download.php`. |
| 3 | Directory listing enabled across `/old/`, `/staff/`, and `/patient/` | **High** | Disable directory indexing (`Options -Indexes` in `.htaccess` or LiteSpeed vhost config). |
| 4 | Weak passwords protecting confidential PDF medical records | **High** | Enforce strong, randomly generated passwords or authenticated portal delivery rather than static weak passwords. |
| 5 | Username enumeration on `patient/login.php` | **Medium** | Standardize authentication error responses to a generic *"Invalid username or password"* message. |
| 6 | Sensitive paths listed in `robots.txt` | **Low** | Rely on proper access controls rather than `robots.txt` to hide sensitive directories. |

---

## 6. Evidences Collected

### 1. WHOIS Domain Reconnaissance
![WHOIS Lookup](Screenshot%202026-11-24%20141732.jpg)

### 2. Nmap SYN Stealth Scan
![Nmap Scan](nmap%20scan.jpg)

### 3. Gobuster Directory Enumeration (Part 1 & Part 2)
![Gobuster Part 1](gobuster%201.jpg)
![Gobuster Part 2](gobuster%202.jpg)

### 4. Exposed `robots.txt` File
![Robots.txt](robot%20txt%20.jpg)

### 5. Open Directory Listings (`/old/`, `/staff/`, `/patient/`)
![Index of /old/](index%20old.jpg)
![Index of /staff/](index%20staff.jpg)
![Index of /patient/](index%20patient.jpg)

### 6. Exposed SQL Database Dump (`mediroza_db_backup_2019.sql`)
![Staff Table Dump](shareholder%20details.jpg)
![Shareholders Table Dump](shareholder%202.PNG)

### 7. Login Portal Username Enumeration
![Valid Username Response](Username%20adm%20.png)
![Invalid Username Response](username%20n.png)

### 8. Patient Portal & Extracted Pathology Reports
![Patient Lab Reports Portal](lab%20report%20file.jpg)
![Patient Report 1](patient%20report%201.png)
![Patient Report 2](patient%20report%202%20.png)
![Patient Report 3](patient%203%20report.png)

### 9. PDF Password Cracking Results
![Report 1 Password Cracked](report%201%20pawd.jpg)
![Report 2 Password Cracked](report%202%20pawd.png)
![Report 3 Password Cracked](report%203%20pawd.png)

---

## 👤 Author & Mentorship
* **Author:** Miracle Orifa (Batch B083)
* **Instructor:** Sir Waqas Karim CCIE | [LinkedIn](https://www.linkedin.com/in/waqaskarim/)
* **Program:** Cybersecurity Internship at **Networkwalks** | Week 03
