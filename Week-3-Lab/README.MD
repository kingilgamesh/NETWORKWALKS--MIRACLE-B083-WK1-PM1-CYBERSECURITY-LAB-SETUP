# 🔐 NETWORKWALKS - WEEK 3: PDF HASH EXTRACTION & PASSWORD CRACKING

**Program:** Cybersecurity Internship @ Networkwalks  
**Author:** Orifa Miracle Oyimafu  
**Module:** WK3-PM2 - Cryptographic Hashing, Dictionary Attacks & Password Recovery  
**Instructor:** Sir Waqas Karim (CCIE)  

---

## 📌 Project Overview
This project demonstrates practical cryptographic hash extraction and offline dictionary-based password recovery on encrypted PDF documents. Using a combination of browser-based hash extractors, **Johnny** (the GUI frontend for John the Ripper), and **Networkwalks Security Tools**, four password-protected PDF files were analyzed, cracked, and unlocked to capture embedded flags.

---

## 🧰 Tools & Technologies Used
* **Networkwalks Hash Calculator:** Local browser-based extraction of `$pdf$` hashes in `pdf2john` / `hashcat` compatible format.
* **OnlineHashCrack (PDF Hash Extractor):** Automated conversion of encrypted PDF structures into crackable hash strings.
* **Johnny (John the Ripper GUI):** Offline password auditing and wordlist-based hash cracking.
* **Networkwalks Password Cracker:** Web-based dictionary attack lab simulating wordlist matching against extracted PDF hashes.

---

## 🛠️ Step-by-Step Execution & Flag Capture

### Phase 1: Cryptographic Hash Extraction
To audit the encrypted PDFs without directly brute-forcing the document viewer, the cryptographic metadata hashes were first extracted:

1. **Using Networkwalks Hash Calculator:**
   - Loaded `My Locked PDF2.pdf` into the local parser.
   - Extracted the 128-bit RC4/AES revision hash (`$pdf$4*4*128*...`) ready for cracking.

![Networkwalks Hash Calculator](hash%20calculator%20.png)

2. **Using OnlineHashCrack PDF Extractor:**
   - Uploaded `My Locked PDF1.pdf` to extract its corresponding `$pdf$` hash string for cross-verification.

![OnlineHashCrack PDF Extractor](OnlineHashCrack.png)

---

### Phase 2: Offline Dictionary Attack with Johnny (John the Ripper)
1. Imported the extracted `$pdf$` hashes into **Johnny**.
2. Configured the format to `PDF` and initiated a dictionary attack.
3. Successfully recovered the plaintext password **`good-luck`** for `My Locked PDF2.pdf`.

![Johnny Password Recovery](johnny%20screenshot.png)

---

### Phase 3: Dictionary Attack via Networkwalks Password Cracker
1. Pasted the extracted hash from `My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf` into the **Networkwalks Password Cracker** lab.
2. Executed the automated wordlist attack, matching the hash at attempt 35 to recover the password **`1qaz2wsx`**.

![Networkwalks Password Cracker](password%20cracker.png)

---

## 🚩 Captured Flags & Verification

Using the recovered passwords, all four target PDF files were unlocked and verified:

| Target Document | Recovered Password / Method | Captured Flag |
| :--- | :--- | :--- |
| `My Locked PDF1.pdf` | Johnny / Hash Extractor | `nw{cybersecurity_flag_captured_2608}` |
| `My Locked PDF2.pdf` | `good-luck` (Johnny) | `nw{networkwalks_persistence_jtr_270521}` |
| `My Locked PDF3.pdf` | Johnny / Dictionary Attack | `nw{networkwalks_flag_260821_1}` |
| `My Locked PDF1 (NW Tools).pdf` | `1qaz2wsx` (NW Password Cracker) | `nw{networkwalks_flag1_jtr_270521_1}` |

### Unlocked Document Proofs:

**1. My Locked PDF1:**

![Unlocked PDF 1](locked%20pdf%201.png)

**2. My Locked PDF2:**

![Unlocked PDF 2](locked%20pdf%202.png)

**3. My Locked PDF3:**

![Unlocked PDF 3](locked%20pdf%203.png)

**4. My Locked PDF1 (Networkwalks Tools Lab):**

![Unlocked PDF 1 NW Tools](locked%20pdf%201%20NW%20tools.png)

---

## 💡 Key Security Takeaways
* **Why Weak Passwords Fail Offline:** Once an attacker extracts a file's cryptographic hash (`$pdf$`), password verification happens entirely offline at high speeds—bypassing viewer lockout limits.
* **Wordlist Vulnerability:** Common patterns and keyboard walks (such as `1qaz2wsx` or dictionary phrases like `good-luck`) are cracked within seconds using standard wordlists.
* **Defensive Mitigation:** Sensitive documents must be protected with high-entropy, randomly generated passphrases (14+ characters) and modern AES-256 encryption standards.

---

## 👨‍🏫 Acknowledgments
Special thanks to **Sir Waqas Karim (CCIE)** and the **Networkwalks IT Solutions & Training** team for the hands-on labs and practical guidance throughout Week 3.
