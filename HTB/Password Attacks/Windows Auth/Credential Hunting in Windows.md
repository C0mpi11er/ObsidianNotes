

> [!info] Credential Hunting on Windows  
> Credential hunting is the process of searching a compromised Windows system for stored credentials.

Goal:

- Discover reusable credentials
    
- Escalate privileges
    
- Pivot to other systems
    
- Expand attack path
    

Typical access methods:

- GUI access (RDP)
    
- CLI access (WinRM / SMB / shell)
    

---

# Why it matters

Once access is gained to a Windows machine (especially an **IT admin workstation**), credential hunting can reveal:

- Domain credentials
    
- Service account credentials
    
- Admin passwords
    
- SSH keys
    
- Saved browser credentials
    
- VPN credentials
    
- Database passwords
    

These often enable:

- Lateral movement
    
- Privilege escalation
    
- Domain compromise
    

---

> [!tip] Think Contextually  
> Search based on **how the machine is used**.

Ask:

**What does this user do daily that requires credentials?**

Example: IT Admin workstation likely contains:

- RDP credentials
    
- SSH sessions
    
- WinSCP saved passwords
    
- VPN configs
    
- Admin scripts
    
- Server management tools
    

This narrows your search and reduces blind guessing.

---

# Useful Search Keywords

Search for terms commonly associated with credentials:

```text
password
pass
passphrase
pwd
creds
credentials
username
user account
keys
passkeys
login
configuration
dbpassword
dbcredential
users
```

---

# Search Methods

## 1. Windows Search (GUI)

If RDP access is available:

Use Windows Search bar to search for:

```text
pass
password
config
cred
login
```

Useful because it searches:

- Files
    
- Settings
    
- Apps
    
- Indexed content
    

---

## 2. LaZagne

LaZagne extracts stored credentials from many applications.

### Common modules

|Module|Purpose|
|---|---|
|browsers|Browser passwords|
|chats|Chat app credentials|
|mails|Email client credentials|
|memory|Memory dumps (KeePass / LSASS)|
|sysadmin|WinSCP, OpenVPN, etc|
|windows|Windows credential stores|
|wifi|Saved WiFi passwords|

---

### Transfer to target

If using RDP (like FreeRDP):

Copy/paste executable into session.

---

### Run LaZagne

```cmd
start LaZagne.exe all
```

Verbose:

```cmd
start LaZagne.exe all -vv
```

---

### Example output

```text
URL: 10.129.x.x
Login: admin
Password: ********
```

This commonly reveals:

- WinSCP creds
    
- Browser saved passwords
    
- OpenVPN credentials
    
- Remote management creds
    

---

> [!warning] OPSEC Note  
> LaZagne is noisy.

It may trigger:

- AV
    
- EDR
    
- Logging
    

Use carefully on monitored systems.

---

# 3. findstr (CLI Search)

Windows built-in pattern search tool.

Search config/text/script files:

```cmd
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

### Flags

|Flag|Meaning|
|---|---|
|`/S`|Search subdirectories|
|`/I`|Case-insensitive|
|`/M`|Show filenames only|
|`/C:`|Exact search string|

---

# High-Value Credential Locations

## SYSVOL

Common finds:

- Group Policy passwords
    
- Deployment scripts
    
- Admin scripts
    

Look for:

```text
\\domain\SYSVOL\
```

---

## Scripts

Check for plaintext creds in:

- PowerShell scripts
    
- Batch files
    
- Login scripts
    
- Deployment automation
    

Extensions:

```text
.ps1
.bat
.cmd
.vbs
```

---

## Web Config Files

Particularly on dev/admin machines:

```text
web.config
app.config
```

Often contain:

- DB creds
    
- Service credentials
    
- API keys
    

---

## unattend.xml

Often stores local admin credentials.

Locations:

```text
C:\Windows\Panther\
C:\Windows\Panther\Unattend\
```

---

## Active Directory Descriptions

Admins sometimes store passwords in:

- User descriptions
    
- Computer descriptions
    

Useful enumeration tools:

- NetExec
    
- LDAP queries
    

---

## KeePass Databases

Look for:

```text
.kdbx
```

If found:

Need master password.

Can attempt:

- Password guessing
    
- Cracking
    

---

## User Files / Shares

Search for obvious names:

```text
pass.txt
passwords.xlsx
creds.docx
vpn.txt
notes.txt
```

Common locations:

- Desktop
    
- Documents
    
- Downloads
    
- Shared folders
    

---

# Browser Credential Storage

High-value browsers:

- Google Chrome
    
- Microsoft Edge
    
- Mozilla Firefox
    

Why useful:  
Users save passwords constantly.

Extraction tools:

- LaZagne
    
- browser-specific decryptors
    

---

# Practical Workflow

> [!example] Credential Hunting Workflow

### 1. Identify system role

Ask:

- Workstation?
    
- Server?
    
- Dev machine?
    
- Admin box?
    

---

### 2. Quick wins

Search:

```cmd
findstr /SIM /C:"password" *
```

Run LaZagne.

---

### 3. Manual file review

Check:

- Documents
    
- Desktop
    
- Downloads
    
- Scripts
    
- Config files
    

---

### 4. Network locations

Enumerate:

- SYSVOL
    
- SMB shares
    
- IT shares
    

---

### 5. Validate credentials

Test with:

- NetExec
    
- SMB
    
- WinRM
    
- RDP
    
- SSH
    

---

# Key Exam / HTB Takeaways

Remember:

> [!success] Core idea  
> Credential hunting is **not random searching**.

It is:

**Context-driven, targeted searching based on system purpose and user behavior.**

Focus on:

- Search terms
    
- Likely storage locations
    
- Built-in tools (`findstr`)
    
- Automated extraction tools (LaZagne)
    
- User workflow patterns
    



