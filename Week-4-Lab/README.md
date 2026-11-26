# 🛡️ Mediroza General Hospital — Web Application Penetration Test
**Networkwalks | Batch B083 | Week 4 Engagement (`W4-PM-FINAL`)**

| Item | Detail |
| :--- | :--- |
| **Engagement** | Networkwalks, Batch B083, Week 4 |
| **Assessment Type** | Black-box penetration test |
| **Duration** | Five days (`22/11/2026` – `26/11/2026`) |
| **Target** | `https://medirozahospital.com` (`199.188.201.16`) |
| **Prepared By** | Orifa Miracle Oyimafu |
| **Report Version** | 1.0 (26 November 2026) |
| **Classification** | Confidential — Authorised personnel only |

---

## 1. Executive Summary & Scope
An authorised black-box security assessment was conducted against **`https://medirozahospital.com`** (`199.188.201.16`) within the strict boundaries of the engagement brief (no social engineering, no denial-of-service testing, and no out-of-scope host exploitation).

Reconnaissance and directory discovery uncovered critical exposure risks across the application:
* **Overall Risk Rating:** **Critical**
* **Key Findings:** A publicly accessible SQL database backup (`/old/mediroza_db_backup_2019.sql`) containing employee HR records and shareholder tables, unauthenticated access to patient laboratory reports protected by weak PDF passwords, login username enumeration, and open directory listings across `/old/`, `/staff/`, and `/patient/`.

---

## 2. Commands Executed

```bash
# 1. Domain Registration & WHOIS Footprinting
whois medirozahospital.com

# 2. ICMP Connectivity Check & TCP SYN Stealth Scan (Top 1000 Ports)
ping -c 25 medirozahospital.com
nmap 199.188.201.16 -vvv

# 3. Web Directory & File Extension Enumeration
gobuster dir -u [https://medirozahospital.com](https://medirozahospital.com) -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,pdf,zip,bak

# 4. Inspecting robots.txt & Exposed Database Backup
curl -I [https://medirozahospital.com/robots.txt](https://medirozahospital.com/robots.txt)
curl -O [https://medirozahospital.com/old/mediroza_db_backup_2019.sql](https://medirozahospital.com/old/mediroza_db_backup_2019.sql)
```

---

## 3. Findings Summary

| ID | Finding | Severity | Evidence Status |
| :--- | :--- | :--- | :--- |
| **F-01** | Publicly accessible database backup containing employee & shareholder data (`/old/mediroza_db_backup_2019.sql`) | **Critical** | Confirmed by directory listing & SQL dump screenshots |
| **F-02** | Unauthenticated portal access & weak password protection on patient laboratory PDFs | **High** | Confirmed by portal, password recovery & unlocked PDF screenshots |
| **F-03** | Login responses enable username enumeration (`/patient/login.php`) | **Medium** | Confirmed by contrasting login error response screenshots |
| **F-04** | Directory listings and `robots.txt` disclose sensitive application paths (`/old/`, `/staff/`, `/patient/`) | **Low** | Confirmed; directly facilitated discovery of F-01 and F-02 |
| **OBS-01** | Multiple network services exposed on resolved hosting IP (`199.188.201.16`) | **Informational** | 12 open TCP ports identified; no service exploitation performed |

---

## 4. Technical Walkthrough & Remediation

### F-01: Exposed SQL Database Backup (`Critical`)
* **Observation:** Browsing to `/old/` revealed directory indexing enabled on LiteSpeed Web Server and an exposed 7 KB backup (`mediroza_db_backup_2019.sql`) from the `mediroza_hr` database containing 30 employee records (`staff` table: names, job titles, departments, emails, phone numbers, national IDs, ZAR salaries, hire dates) and 10 `shareholders` table records.
* **Remediation:** Immediately remove all `.sql` and `.bak` files from public web directories, audit access logs, rotate exposed credentials, and store backups in encrypted, non-public storage.

### F-02: Patient Portal Access & Weak PDF Encryption (`High`)
* **Observation:** Accessing `/patient/portal.php` allowed downloading three encrypted patient pathology reports (`LR-2024-1187`, `LR-2024-1192`, `LR-2024-1205`). Extracting their R3/128-bit hashes (`$pdf$2*3*128...`) and running dictionary recovery cracked all three passwords (`123456`, `password`, and `!@#$%^&`).
* **Remediation:** Enforce server-side session authentication and patient-level authorization checks on `portal.php` and `download.php`, and replace weak static PDF passwords with authenticated download tokens.

### F-03: Username Enumeration on Login Form (`Medium`)
* **Observation:** `/patient/login.php` returned *"Incorrect password"* for a valid username (`admin`) versus *"Username not found"* for an unknown username (`Naledi`).
* **Remediation:** Return a unified generic message (*"Invalid username or password"*) for all failed login attempts and apply rate limiting.

### F-04 & OBS-01: Path Disclosure & Network Services (`Low / Informational`)
* **Observation:** `robots.txt` disclosed `/patient/`, `/staff/`, and `/old/`, while `/patient/` exposed `error_log` (251 KB). Nmap identified 12 open TCP ports (`21`, `26`, `53`, `80`, `110`, `143`, `443`, `465`, `587`, `993`, `995`, with `20/tcp` closed).
* **Remediation:** Disable directory indexing (`Options -Indexes`), move logs outside the document root, and disable legacy cleartext protocols (FTP, POP3, IMAP).

---

## 5. Limitations & Operational Notes
* **Authentication Path:** The exact request/response path leading to `/patient/portal.php` requires code-level verification and is not classified as SQL injection without controlled confirmation.
* **Data Handling Best Practice:** In future assessments, perform PDF hash extraction and password recovery offline (`pdf2john` + `John the Ripper`/`Hashcat`) rather than uploading sensitive client documents to third-party web tools.

---

## 6. Evidences Collected

### E-01: WHOIS Domain Reconnaissance
![WHOIS Lookup](Screenshot%202026-11-24%20141732.png)

### E-02: Nmap SYN Stealth Scan (`199.188.201.16`)
![Nmap Scan](nmap%20scan.png)

### E-03: Gobuster Directory Enumeration (Part 1 & Part 2)
![Gobuster Part 1](gobuster%201.png)
![Gobuster Part 2](gobuster%202.png)

### E-04: Exposed `robots.txt` File
![Robots.txt](robot%20txt%20.png)

### E-05: Legacy Directory Listing (`/old/`)
![Index of /old/](index%20old.png)

### E-06: Exposed SQL Database Dump (`mediroza_db_backup_2019.sql`)
![Staff Table Dump](shareholder%20details.png)
![Shareholders Table Dump](shareholder%202.PNG)

### E-07 & E-08: Staff Directory Listing & Login Username Enumeration
![Index of /staff/](index%20staff.png)
![Valid Username Response](Username%20adm%20.png)
![Invalid Username Response](username%20n.png)

### E-09 & E-10: Patient Directory Listing & Lab Reports Portal
![Index of /patient/](index%20patient.png)
![Patient Lab Reports Portal](lab%20report%20file.png)

### E-11 to E-13: PDF Password Recovery & Unlocked Pathology Reports
![Report 1 Password Cracked](report%201%20pawd.png)
![Patient Report 1](patient%20report%201.png)
![Report 2 Password Cracked](report%202%20pawd.png)
![Patient Report 2](patient%20report%202%20.png)
![Report 3 Password Cracked](report%203%20pawd.png)
![Patient Report 3](patient%203%20report.png)

---

## 👤 Author & Mentorship
* **Prepared By:** Orifa Miracle Oyimafu (Batch B083)
* **Instructor:** Sir Waqas Karim CCIE | [LinkedIn](https://www.linkedin.com/in/waqaskarim/)
* **Program:** Cybersecurity Internship at **Networkwalks** | Week 04
