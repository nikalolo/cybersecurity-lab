# Cybersecurity Lab

A collection of hands-on penetration testing and web application security labs built during my cybersecurity coursework. All testing was performed in isolated virtualized environments for educational purposes.

## Lab Environment

| Component | Details |
|---|---|
| **Attacking Machine** | Kali Linux (VirtualBox VM) |
| **Target Machine** | Metasploitable 2 (VirtualBox VM) |
| **Network** | Host-only / Internal network (isolated) |
| **Tools Used** | Nmap, Nmap Scripting Engine (NSE), Metasploit Framework (msfconsole), Burp Suite, Firefox |

## Labs

### 1. Network Reconnaissance & Exploitation

- Performed host discovery and service enumeration against Metasploitable 2
- Identified open ports and vulnerable services using Nmap and NSE scripts
- Exploited the **vsftpd 2.3.4 backdoor** (CVE-2011-2523) via Metasploit to gain root shell access
- Documented the attack chain and proposed remediation steps

**Key Commands:**

```bash
nmap -sV -sC [target_ip]
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS [target_ip]
vncviewer 192.168.100.152 # Password: password
run
```

### 2. Web Application Security Testing (DVWA)

- Deployed and tested **Damn Vulnerable Web Application (DVWA)** on the internal network
- Identified and manually exploited OWASP Top 10 vulnerabilities:
  - **SQL Injection** — extracted database information via UNION-based injection
  - **Reflected & Stored XSS** — injected malicious scripts to demonstrate session hijacking vectors
  - **Command Injection** — executed arbitrary system commands through unsanitized input fields
- Analyzed input validation flaws and proposed mitigations:
  - Parameterized queries / prepared statements
  - Output encoding and context-aware sanitization
  - Input validation and allowlisting

## Repository Structure

```
cybersecurity-lab/
├── README.md
├── report.md
└── screenshots/
    ├── meta2_ip.png
    ├── nmap-scan.png
    ├── vsftpd-exploit.png
    ├── vncviewer.png
    ├── dvwa-access.png
    ├── dvwa-login.png
    ├── dvwa-sql-injection.png
    └── dvwa-xss.png
```

## Disclaimer

All activities documented here were performed in a controlled, isolated lab environment on intentionally vulnerable systems. Never attempt these techniques on systems you do not own or have explicit written permission to test.

**Author:** Nika Loliashvili
**LinkedIn:** [linkedin.com/in/nika-loliashvili-9b02aa392](https://linkedin.com/in/nika-loliashvili-9b02aa392)
