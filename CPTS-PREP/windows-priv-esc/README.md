```markdown
# 🛰️ Windows Privilege Escalation

> [!ABSTRACT] Overview
> Windows privilege escalation techniques for penetration testing and CPTS preparation. This section covers systematic approaches to elevating privileges from a low-privileged user account to local administrator or system-level access.

---

## 📚 Module Structure

### 🔍 Initial Assessment
- **[[Situational Awareness]]** - Network enumeration, security protections, system context
- **[[Initial Enumeration]]** - System info, processes, users, groups, and services enumeration
- **[[Communication with Processes]]** - Network services and named pipes analysis

---

### 🏛️ User and Group Privileges  
- **[[SeImpersonate & SeAssignPrimaryToken]]** - Token impersonation attacks (Potato techniques)
- **[[SeDebugPrivilege]]** - LSASS memory dumping and SYSTEM privilege escalation
- **[[SeTakeOwnershipPrivilege]]** - File ownership takeover and ACL manipulation
- **[[Windows Built-in Groups]]** - Backup Operators, SeBackupPrivilege, and NTDS.dit extraction
- **[[Event Log Readers]]** - Event log analysis and credential extraction from command lines
- **[[DnsAdmins]]** - DNS service DLL injection and Domain Controller privilege escalation
- **[[Hyper-V Administrators]]** - VM cloning attacks and hard link exploitation
- **[[Print Operators]]** - SeLoadDriverPrivilege exploitation and Capcom.sys driver attacks
- **[[Server Operators]]** - Service control, binary path modification, and local administrator access
- **[[UAC Bypass]]** - User Account Control bypass via DLL hijacking and auto-elevating binaries
- **[[Weak Permissions]]** - File system ACLs, service permissions, unquoted paths, and registry exploitation
- **[[Kernel Exploits]]** - Historical and modern Windows kernel vulnerabilities for privilege escalation
- **[[Vulnerable Services]]** - Third-party application exploitation and service-based privilege escalation
- **[[Credential Hunting]]** - File system credential discovery, PowerShell history, and DPAPI decryption
- **[[Other Files]]** - Advanced credential hunting in StickyNotes, system files, and network shares
- **Windows User Privileges** - Token privileges and abuse techniques
- **Windows Group Privileges** - Dangerous group memberships and exploitation

---

### 🎯 Attack Vectors
- **Attacking the OS** - Kernel exploits, service misconfigurations
- **Credential Theft** - LSASS, registry, memory-based attacks
- **Service Exploitation** - Unquoted service paths, weak permissions
- **Scheduled Task Abuse** - Task scheduler misconfigurations

---

### 🔒 Restricted Environments
- **AppLocker Bypass** - Application whitelisting evasion
- **AMSI Bypass** - Antimalware Scan Interface evasion
- **UAC Bypass** - User Access Control circumvention

---

### 🛠️ Additional Techniques
- **DLL Hijacking** - DLL search order exploitation
- **Registry Exploitation** - Registry-based privilege escalation
- **File System** - NTFS permissions and symbolic links
- **Windows Subsystem** - WSL and containerization issues

---

### 🏚️ Legacy Systems
- **End of Life Systems** - Windows 7, Server 2008 specific techniques
- **Legacy Service Exploitation** - Deprecated service vulnerabilities

---

## 🎯 Learning Objectives

1. **Systematic enumeration** - Comprehensive information gathering
2. **Attack vector identification** - Spotting escalation opportunities  
3. **Tool proficiency** - PowerShell, WinPEAS, PrivescCheck
4. **Evasion techniques** - Bypassing security controls
5. **Persistence methods** - Maintaining elevated access

---

## 🛠️ Common Tools

```powershell
# Automated enumeration
WinPEAS.exe
PrivescCheck.ps1
PowerUp.ps1
Seatbelt.exe

# Manual techniques
whoami /all
Get-Process
Get-Service
Get-ScheduledTask
```

---

## 📋 Quick Assessment Checklist

- [ ] Current user privileges (`whoami /priv`)
- [ ] Group memberships (`whoami /groups`)
- [ ] Running services (`Get-Service`)
- [ ] Network configuration (`ipconfig /all`)
- [ ] Installed software (`Get-WmiObject Win32_Product`)
- [ ] Security protections (`Get-MpComputerStatus`)
- [ ] Scheduled tasks (`Get-ScheduledTask`)
- [ ] File/folder permissions (`icacls`)

---

> [!INFO] This section provides comprehensive coverage of Windows privilege escalation techniques aligned with the CPTS certification requirements.
```