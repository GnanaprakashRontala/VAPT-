# 🔐 Vulnerability Assessment and Penetration Testing (VAPT)
### Target: Metasploitable 2 | 12 Vulnerabilities Exploited | Full Lab Report

![Security](https://img.shields.io/badge/Type-Penetration%20Testing-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Exploits](https://img.shields.io/badge/Exploits-12%20Critical-red)
![Tools](https://img.shields.io/badge/Tools-Nmap%20%7C%20Metasploit%20%7C%20Wireshark%20%7C%20Burp-blue)
![OS](https://img.shields.io/badge/Attacker%20OS-Kali%20Linux-purple)
![Legal](https://img.shields.io/badge/Environment-Isolated%20Lab-yellow)
![Category](https://img.shields.io/badge/Category-Cybersecurity-orange)

---

## 📌 Project Overview

This project documents a **comprehensive Vulnerability Assessment and Penetration Test (VAPT)** conducted on **Metasploitable 2** — an intentionally vulnerable virtual machine designed for security training and research.

**12 critical vulnerabilities** were identified and exploited across 4 major attack categories:
- 🌐 Network Exploitation
- 🗄️ Database Attacks  
- 🕸️ Web Application Attacks
- ⬆️ Privilege Escalation

> ⚠️ **Disclaimer:** All activities were performed in an **isolated VirtualBox lab environment** on software specifically designed to be exploited. This project is purely for **educational purposes**. Never attempt these techniques on systems you do not own or have explicit written permission to test.

---

## 🖥️ Lab Environment

| Component | Details |
|---|---|
| **Attacker Machine** | Kali Linux |
| **Target Machine** | Metasploitable 2 |
| **Target OS** | Linux 2.6.24 (Ubuntu 8.04) |
| **Network** | VirtualBox Bridged Adapter (Isolated) |
| **Virtualization** | Oracle VirtualBox |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Nmap** | Port scanning & service enumeration |
| **Metasploit Framework** | Exploitation |
| **Wireshark** | Network packet capture & analysis |
| **MySQL Client** | Database access testing |
| **Netcat (nc)** | Raw TCP connections |
| **VNC Viewer** | Remote desktop access |
| **Web Browser** | Manual web application testing |

---

## 🔍 Methodology

```
Reconnaissance → Scanning → Enumeration → Vulnerability Analysis → Exploitation → Reporting
```

Following the **PTES (Penetration Testing Execution Standard)** methodology.

---

## 🚨 Vulnerabilities Exploited

### 🌐 Network Exploitation

#### 1️⃣ vsftpd 2.3.4 Backdoor — CVE-2011-2523
- **Port:** 21 (FTP) | **Severity:** 🔴 Critical
- **Result:** Instant root shell via FTP backdoor
- vsftpd 2.3.4 had a malicious backdoor planted in its source code. Sending `:)` in the username triggers a root shell on port 6200 — a classic supply chain attack.

#### 2️⃣ Samba 3.0.20 Usermap Script — CVE-2007-2447
- **Port:** 445 (SMB) | **Severity:** 🔴 Critical
- **Result:** Root shell via reverse TCP
- Command injection via shell metacharacters in Samba's username field, resulting in unauthenticated remote code execution as root.

#### 3️⃣ Bindshell — Port 1524
- **Port:** 1524 | **Severity:** 🔴 Critical
- **Result:** Instant root shell — zero effort
- A root shell was literally sitting open on port 1524 with no authentication. Single `nc` command = instant root.

#### 4️⃣ UnrealIRCd 3.2.8.1 Backdoor — CVE-2010-2075
- **Port:** 6667 (IRC) | **Severity:** 🔴 Critical
- **Result:** Root shell via IRC backdoor
- Second supply chain attack — malicious code planted in UnrealIRCd source. Triggers root shell via reverse TCP.

#### 5️⃣ VNC Unauthenticated Access
- **Port:** 5900 (VNC) | **Severity:** 🔴 Critical
- **Result:** Full graphical desktop control
- VNC running with weak default password — gained complete visual and interactive control of the target desktop in real time.

#### 6️⃣ Telnet Plaintext Credential Sniffing
- **Port:** 23 (Telnet) | **Severity:** 🔴 Critical
- **Result:** Credentials captured in plaintext via Wireshark
- Demonstrated how Telnet sends everything including passwords in plain text. Captured full session including credentials using Wireshark TCP Stream analysis.

---

### 🗄️ Database Attacks

#### 7️⃣ MySQL Unauthenticated Root Access
- **Port:** 3306 (MySQL) | **Severity:** 🔴 Critical
- **Result:** Full database dump — all credentials extracted
- MySQL root account had no password. Direct connection dumped entire DVWA database including MD5 password hashes.

#### 8️⃣ PostgreSQL Default Credentials + SUID Privilege Escalation
- **Port:** 5432 (PostgreSQL) | **Severity:** 🔴 Critical
- **Result:** Root via two-stage attack chain
- Stage 1: Gained access via default `postgres:postgres` credentials. Stage 2: Escalated to root using SUID bit set on old Nmap binary (`nmap --interactive → !sh`).

---

### 🕸️ Web Application Attacks

#### 9️⃣ Apache Tomcat Manager — Default Credentials
- **Port:** 8180 (HTTP) | **Severity:** 🔴 Critical
- **Result:** Meterpreter shell via malicious WAR upload
- Accessed Tomcat Manager with default `tomcat:tomcat` credentials. Uploaded malicious WAR file via manager interface to get Meterpreter shell.

#### 🔟 SQL Injection (DVWA)
- **Port:** 80 (HTTP) | **Severity:** 🔴 Critical
- **Result:** Full database dump via browser input field
- Manual SQL injection without any tools. Used both authentication bypass (`1' OR '1'='1`) and UNION-based injection to extract all credentials.

#### 1️⃣1️⃣ Stored XSS (DVWA)
- **Port:** 80 (HTTP) | **Severity:** 🔴 Critical
- **Result:** Persistent JavaScript execution in all visitors' browsers
- Injected malicious JavaScript into the guestbook that persists in the database and executes for every user who visits the page.

#### 1️⃣2️⃣ Command Injection (DVWA)
- **Port:** 80 (HTTP) | **Severity:** 🔴 Critical
- **Result:** Full OS command execution via web form
- Injected OS commands through a ping input field. Dumped entire `/etc/passwd` file through the browser.

---

## 📊 Findings Summary

| # | Vulnerability | Category | Severity |
|---|---|---|---|
| 1 | vsftpd 2.3.4 Backdoor | Network | 🔴 Critical |
| 2 | Samba 3.0.20 Usermap | Network | 🔴 Critical |
| 3 | MySQL No Authentication | Database | 🔴 Critical |
| 4 | Bindshell Port 1524 | Network | 🔴 Critical |
| 5 | PostgreSQL Default Creds | Database | 🔴 Critical |
| 6 | SUID Nmap Privesc | Privilege Escalation | 🔴 Critical |
| 7 | VNC Weak Authentication | Network | 🔴 Critical |
| 8 | UnrealIRCd Backdoor | Network | 🔴 Critical |
| 9 | Tomcat Default Credentials | Web App | 🔴 Critical |
| 10 | Telnet Plaintext Protocol | Network | 🔴 Critical |
| 11 | SQL Injection | Web App | 🔴 Critical |
| 12 | Stored XSS | Web App | 🔴 Critical |
| 13 | Command Injection | Web App | 🔴 Critical |

**Overall Risk: 🔴 CRITICAL — 13 Critical Findings**

---

## 📁 Repository Structure

```
VAPT-Metasploitable2/
│
├── README.md                  ← You are here
├── VAPT_Report_Final.md       ← Full detailed report with all 12 exploits
└── screenshots/               ← Evidence screenshots
    ├── nmap_scan.png
    ├── vsftpd_exploit.png
    ├── samba_exploit.png
    ├── mysql_dump.png
    ├── bindshell.png
    ├── vnc_desktop.png
    ├── tomcat_manager.png
    ├── wireshark_telnet.png
    ├── sql_injection.png
    ├── xss_stored.png
    └── command_injection.png
```

---

## 🎓 Key Learnings

- Performing **network reconnaissance** using Nmap
- Understanding and exploiting **CVEs** using Metasploit
- **Manual web exploitation** — SQL Injection, XSS, Command Injection without tools
- **Privilege escalation** via SUID binary abuse
- **Packet analysis** using Wireshark to capture plaintext credentials
- Why **patch management** and keeping software updated is critical
- How **default credentials** and **misconfigurations** create critical risks
- Writing a professional **penetration test report**
- Understanding **OWASP Top 10** vulnerabilities hands-on

---

## 🔗 References

- [CVE-2011-2523 — vsftpd Backdoor](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523)
- [CVE-2007-2447 — Samba Usermap](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-2447)
- [CVE-2010-2075 — UnrealIRCd](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2010-2075)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Metasploitable 2 Documentation](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [PTES Standard](http://www.pentest-standard.org/)

---

## 👤 Author

**Gnana prakash Rontala**  
Computer Science Student | Aspiring Cybersecurity Professional  
📧 gnanaprakash.rontala.27@gmail.com 
🔗 linkedin.com/in/gnana-prakash-rontala-585408406  
🐦 https://github.com/GnanaprakashRontala/VAPT-.git

---

*This project was completed as part of a hands-on cybersecurity lab exercise to build practical penetration testing skills.*  
*All testing was performed legally in an isolated environment.*
