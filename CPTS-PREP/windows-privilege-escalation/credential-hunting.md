```markdown
# 🛡️ Credential Hunting

---

## 🔍 Overview & Objectives

> [!ABSTRACT] 
> This section provides an overview of the process and objectives for credential hunting in cybersecurity scenarios.

Credential hunting is a critical phase in penetration testing aimed at identifying sensitive information such as usernames, passwords, and other authentication credentials that can be used to gain unauthorized access. The primary objective is to gather enough information to perform effective password spraying or pass-the-hash attacks.

---

## 🛠️ Tools & Techniques

> [!INFO] 
> Common tools and techniques for credential hunting include [[CrackMapExec]], [[SMBClient]], and manual enumeration methods.

### Tools
- **[[CrackMapExec]]**
  - `crackmapexec smb <IP>`
- **[[SMBClient]]**
  - `smbclient -N -L //<IP>` (Anonymous)
  - `smbclient -L //<IP> -U user` (Authenticated)

---

## 🚦 Enumeration Workflow

> [!SUCCESS] 
> The enumeration process involves several steps, each aimed at gathering more information about the target system.

1. **Identify Open SMB Ports**
   ```bash
   sudo nmap -p445 <IP>
   ```
2. **Enumerate Shares and Access Levels**
   ```bash
   smbclient -N -L //<IP>
   ```
3. **Check for Guest or Null Sessions**
   ```bash
   rpcclient -U'%' <IP>
   ```
4. **List Users, Groups, and Permissions**
   ```bash
   enumdomusers
   enumdomgroups
   ```

---

## 📄 File Hunting

> [!EXAMPLE] 
> Searching for files containing sensitive information is a crucial part of credential hunting.

```text
find / -type f \( -name "*.txt" -o -name "*.xml" \) 2>/dev/null | grep -i 'password|creds|secret'
```

### High-Value Files
- `passwords.txt`
- `creds.txt`
- `config.php`
- `.kdbx` (KeePass Database)
- `web.config`
- `database.yml`
- `.ssh/id_rsa`

---

## ⚠️ Common Findings

> [!WARNING] 
> Certain findings during credential hunting can indicate significant security weaknesses.

| Finding | Impact |
|---|---|
| **Null Session Access** | User Enumeration and Data Exposure |
| **Writable Shares** | Malware Drop Zones for Lateral Movement |
| **Local Admin Credentials** | Potential SYSTEM Shell Access |

---

## 🔐 Defensive Measures

> [!NOTE] 
> Implementing strong security measures can mitigate the risks associated with credential hunting.

- Enable Strong Password Policies
- Disable Guest Accounts and Null Sessions
- Ensure SMB Signing is Enabled
- Regularly Audit User Permissions and File Shares
```