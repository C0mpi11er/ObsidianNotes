# 🛰️ Native Windows Enumeration Guide

## 🔍 Introduction to AD Enumeration Techniques

### ⚙️ Overview of Living off the Land (LotL) Tactics
Living off the land involves using native tools and system features to achieve enumeration, reconnaissance, and exploitation goals without deploying external utilities. This approach is critical in Active Directory environments for avoiding detection by modern security solutions.

## 📜 Enumerating Local Accounts

### 💻 Basic User Enumeration
```cmd
net user
```

### 🔍 Detailed Account Information
```cmd
net user [USERNAME]
```
Example:
```cmd
C:\htb> net user damundsen

User name                    DAMUNDSEN
Full Name                   
Comment                      Damond "Dan" Mundson
User's comment               HTB{...}
Country/region code          000 (Computer default)
Account active               Yes
Account expires              Never

...
```

### 🌐 Local Groups with Privileges
```cmd
net localgroup administrators
```
Example:
```plaintext
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
INLANEFREIGHT\damundsen
INLANEFREIGHT\Domain Admins
The command completed successfully.
```

**Expected Answer:** `damundsen`

## 🔍 Enumerating Domain Accounts and Groups

### 🌐 Detailed Domain User Information
```cmd
net user [USERNAME] /domain
```
Example:
```plaintext
User name                    damondmundson
Full Name                    Damond "Dan" Mundson
Comment                      Damond "Dan" Mundson
User's comment               HTB{...}
Country/region code          000 (Computer default)
Account active               Yes
Account expires              Never

...
```

### 🔍 Domain Group Membership
```cmd
net group [GROUPNAME] /domain
```
Example:
```plaintext
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
INLANEFREIGHT\damundsen
INLANEFREIGHT\Domain Admins
The command completed successfully.
```

**Expected Answer:** `HTB{...}`

### 🔍 Disabled Accounts with Administrative Privileges
```cmd
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2)(adminCount=1))" -attr sAMAccountName description
```
Example:
```plaintext
  sAMAccountName description
  backup_svc     HTB{...}
```

**Expected Answer:** `HTB{...}`

## 🔧 Advanced Native Techniques

### 📄 PowerShell One-Liners for Enumeration
```powershell
# Domain user enumeration with details
Get-ADUser -Filter * -Properties * | Select-Object Name, SamAccountName, Enabled, LastLogonDate, AdminCount | Format-Table

# Group membership analysis
Get-ADGroupMember -Identity "Domain Admins" | ForEach-Object {Get-ADUser $_ -Properties LastLogonDate | Select-Object Name, LastLogonDate}

# Computer enumeration
Get-ADComputer -Filter * -Properties OperatingSystem, LastLogonDate | Sort-Object LastLogonDate

# Service Principal Name discovery
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName | Select-Object Name, ServicePrincipalName

# Find user accounts with interesting flags
Get-ADUser -Filter * -Properties UserAccountControl | Where-Object {$_.UserAccountControl -band 0x10000} | Select-Object Name, UserAccountControl
```

### 🌐 WMI Remote Enumeration Techniques
```cmd
# Remote system information
wmic /node:"TARGET_HOST" /user:"DOMAIN\USER" /password:"PASSWORD" computersystem get Name,Domain

# Remote process enumeration
wmic /node:"TARGET_HOST" process list brief

# Remote service enumeration
wmic /node:"TARGET_HOST" service get name,state,startmode

# Remote group enumeration
wmic /node:"TARGET_HOST" group get name,description
```

### 🔍 Registry-Based Discovery
```cmd
# Domain information from registry
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\History" /s

# Cached logons
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v CachedLogonsCount

# Auto-logon credentials
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword

# Installed software
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall" /s | findstr "DisplayName"
```

## ⚡ Quick Reference Commands

### 🔧 Essential Command Matrix

| **Category** | **Command** | **Purpose** |
|--------------|-------------|-------------|
| **System Info** | `systeminfo` | Complete system overview |
| **Network** | `ipconfig /all` | Network configuration |
| **Network** | `arp -a` | Known hosts discovery |
| **Network** | `route print` | Network topology |
| **Security** | `netsh advfirewall show allprofiles` | Firewall status |
| **Security** | `Get-MpComputerStatus` | Defender configuration |
| **Sessions** | `qwinsta` | Active sessions |
| **Domain** | `net group /domain` | Domain groups |
| **Domain** | `net user /domain` | Domain users |
| **Domain** | `dsquery user` | LDAP user query |
| **Domain** | `dsquery computer` | LDAP computer query |
| **WMI** | `wmic ntdomain list /format:list` | Domain information |

### 🚀 Rapid Enumeration Script
```cmd
@echo off
echo === Basic Host Information ===
hostname
echo %USERDOMAIN%
echo %LOGONSERVER%

echo === Network Configuration ===
ipconfig /all | findstr /i "IP Address\|Subnet\|Gateway\|DNS"

echo === Domain Groups ===
net group /domain

echo === Local Administrators ===
net localgroup administrators

echo === Security Configuration ===
sc query windefend

echo === Active Sessions ===
qwinsta

echo === ARP Table ===
arp -a

echo === Domain Controllers ===
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -attr sAMAccountName
```

## 🔑 Key Takeaways

### ✅ Native Tool Advantages
- **No File Transfer**: Built-in tools eliminate upload requirements.
- **Reduced Detection**: Lower probability of triggering security controls.
- **Legitimate Activity**: Commands blend with normal administrative tasks.
- **Universal Availability**: Tools exist on all Windows domain systems.

### 🎯 Strategic Enumeration Priorities
1. **System Context**: Understand host role and privilege level.
2. **Security Posture**: Assess defensive capabilities and monitoring.
3. **Network Topology**: Map accessible systems and network segments.
4. **Domain Structure**: Identify users, groups, and trust relationships.
5. **Attack Vectors**: Locate privilege escalation and lateral movement opportunities.

### ⚠️ Operational Security Considerations
- **PowerShell Logging**: Script Block Logging captures command history.
- **Event Generation**: Net commands and WMI queries create Event Log entries.
- **Behavioral Analysis**: Unusual command patterns may trigger EDR alerts.
- **Version Downgrade**: PowerShell v2.0 bypasses modern logging capabilities.
- **Alternative Syntax**: Use `net1` instead of `net` to avoid string detection.

### 🚀 Escalation Pathways
After native enumeration, typical next steps include:
- **Credential Harvesting**: Memory dumps, registry extraction, file hunting.
- **Privilege Escalation**: Service misconfigurations, scheduled tasks, permissions.
- **Lateral Movement**: PSRemoting, WMI execution, service account abuse.
- **Persistence**: Registry modifications, service creation, scheduled tasks.

---

*Living off the land demonstrates that comprehensive Active Directory enumeration is possible using only native Windows tools - proving that security through obscurity is insufficient and that proper access controls and monitoring are essential for domain protection.*

---