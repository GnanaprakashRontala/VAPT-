# VAPT-
# 🔐 Vulnerability Assessment and Penetration Testing (VAPT)
### Target: Metasploitable 2 | Environment: Isolated Lab

![Security](https://img.shields.io/badge/Type-Penetration%20Testing-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Tools](https://img.shields.io/badge/Tools-Nmap%20%7C%20Metasploit%20%7C%20MySQL-blue)
![OS](https://img.shields.io/badge/Attacker%20OS-Kali%20Linux-purple)
![Legal](https://img.shields.io/badge/Environment-Lab%20Only-yellow)

---

## 📌 Project Overview

This project documents a full **Vulnerability Assessment and Penetration Test (VAPT)** conducted on **Metasploitable 2** — an intentionally vulnerable virtual machine designed for security training and research.

The goal was to simulate a real-world black-box penetration test by:
- Discovering all open ports and running services
- Identifying known vulnerabilities
- Exploiting critical vulnerabilities to demonstrate impact
- Documenting findings and providing remediation recommendations

> ⚠️ **Disclaimer:** All activities were performed in an **isolated VirtualBox lab environment** on software designed to be exploited. This project is purely for **educational purposes**. Never attempt these techniques on systems you do not own or have explicit permission to test.

---

## 🖥️ Lab Environment

| Component | Details |
|---|---|
| **Attacker Machine** | Kali Linux |
| **Attacker IP** | 10.73.92.87 |
| **Target Machine** | Metasploitable 2 |
| **Target IP** | 10.73.92.147 |
| **Target OS** | Linux 2.6.24 (Ubuntu 8.04) |
| **Network** | VirtualBox Bridged Adapter (Isolated) |
| **Virtualization** | Oracle VirtualBox |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Nmap** | Port scanning & service enumeration |
| **Metasploit Framework** | Exploitation |
| **MySQL Client** | Database access testing |
| **Kali Linux** | Attacker operating system |

---

## 🔍 Methodology

This assessment followed the **PTES (Penetration Testing Execution Standard)**:

```
Reconnaissance → Scanning → Enumeration → Vulnerability Analysis → Exploitation → Reporting
```

---

## 🚨 Critical Vulnerabilities Exploited

### 1️⃣ vsftpd 2.3.4 Backdoor — CVE-2011-2523
- **Port:** 21 (FTP)
- **Severity:** 🔴 Critical (CVSS 10.0)
- **Result:** Remote root shell obtained
- **Tool:** Metasploit — `exploit/unix/ftp/vsftpd_234_backdoor`

> vsftpd 2.3.4 contained a malicious backdoor in its source code. Sending a smiley face `:)` in the username triggers the backdoor, opening a root shell on port 6200.

---

### 2️⃣ Samba 3.0.20 Usermap Script — CVE-2007-2447
- **Port:** 445 (SMB)
- **Severity:** 🔴 Critical (CVSS 10.0)
- **Result:** Remote root shell via reverse TCP
- **Tool:** Metasploit — `exploit/multi/samba/usermap_script`

> Samba 3.0.20 allows command injection via shell metacharacters in the username field, resulting in unauthenticated remote code execution as root.

---

### 3️⃣ MySQL Unauthenticated Root Access
- **Port:** 3306 (MySQL)
- **Severity:** 🔴 Critical (CVSS 9.8)
- **Result:** Full database access — all user credentials dumped
- **Tool:** MySQL Client

> MySQL root account had no password set, allowing direct connection and complete database compromise including extraction of MD5 password hashes.

---

## 📊 Findings Summary

| # | Vulnerability | Severity | Status |
|---|---|---|---|
| F-01 | vsftpd 2.3.4 Backdoor | 🔴 Critical | Exploited |
| F-02 | Samba 3.0.20 Usermap Script | 🔴 Critical | Exploited |
| F-03 | MySQL No Root Password | 🔴 Critical | Exploited |
| F-04 | Anonymous FTP Login | 🟠 High | Identified |
| F-05 | Telnet Running (Plaintext) | 🟠 High | Identified |
| F-06 | Weak Passwords (MD5) | 🟠 High | Identified |
| F-07 | Outdated Linux Kernel (2008) | 🟡 Medium | Identified |
| F-08 | Apache 2.2.8 Outdated | 🟡 Medium | Identified |
| F-09 | SMB Message Signing Disabled | 🟡 Medium | Identified |
| F-10 | VNC Protocol 3.3 Weak Auth | 🟡 Medium | Identified |

---

## 📁 Repository Structure

```
VAPT-Metasploitable2/
│
├── README.md              ← You are here
├── VAPT_Report.md         ← Full detailed report
└── screenshots/           ← Evidence screenshots
    ├── nmap_scan.png
    ├── vsftpd_exploit.png
    ├── samba_exploit.png
    └── mysql_dump.png
```

---

## 📸 Key Evidence

> Add your screenshots here after uploading them to the screenshots/ folder

| Screenshot | Description |
|---|---|
| `nmap_scan.png` | Full Nmap scan showing 21 open ports |
| `vsftpd_exploit.png` | Root shell obtained via vsftpd backdoor |
| `samba_exploit.png` | Root shell obtained via Samba exploit |
| `mysql_dump.png` | Full user credential database dump |

---

## 🎓 Key Learnings

- How to perform **network reconnaissance** using Nmap
- Understanding **CVEs** and how known vulnerabilities are exploited
- Using the **Metasploit Framework** for real-world exploitation
- Importance of **patch management** and keeping software updated
- How **weak configurations** (no passwords, plaintext protocols) create critical risks
- How to write a **professional penetration test report**

---

## 🔗 References

- [CVE-2011-2523 — vsftpd Backdoor](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523)
- [CVE-2007-2447 — Samba Usermap](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-2447)
- [Metasploitable 2 Documentation](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [PTES Standard](http://www.pentest-standard.org/)

---

## 👤 Author

**[Your Name]**  
Computer Science Student | Aspiring Cybersecurity Professional  
📧 [your-email@example.com]  
🔗 [LinkedIn Profile]  

---

*This project was completed as part of a hands-on cybersecurity lab exercise to build practical penetration testing skills.*
