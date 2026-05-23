
---

# 🌐 Network Services & Authentication Overview

> [!info] 🧠 What are Network Services?
> 
> ```bash
> Network services are applications running on hosts that provide functionality like file sharing, remote access, authentication, and database access over a network.
> ```
> 
> 💡 Key Idea:
> 
> - Each service runs under **specific users/permissions**
>     
> - Each service exposes a **network interface (port)**
>     
> - Attackers target these services for **credential access + remote execution**
>     

---

# 🔐 Common Remote Access & Management Services

---

|Service|Port|Purpose|
|:--|:--|:--|
|**SSH**|22|Secure remote CLI access (Linux/Windows)|
|**RDP**|3389|Windows GUI remote desktop|
|**WinRM**|5985/5986|Windows remote management (PowerShell-based)|
|**SMB**|445|File sharing + remote admin in Windows networks|
|**FTP**|21|File transfer protocol|
|**MSSQL/MySQL**|1433/3306|Database access|
|**VNC**|5900|Remote GUI control|
|**LDAP**|389|Directory services authentication|

---

# ⚙️ WinRM (Windows Remote Management)

> [!info] 🧠 What is WinRM?
> 
> ```bash
> A SOAP-based protocol for remote management of Windows systems via WMI and PowerShell.
> ```

💡 Key Points:

- Uses **HTTP (5985)** or **HTTPS (5986)**
    
- Requires **enabled configuration**
    
- Often used in enterprise environments
    

---

> [!success] 📡 NetExec (WinRM brute-force / access)

```bash
netexec winrm <target-IP> -u user.list -p password.list
```

✔ If successful:

```bash
(Pwn3d!)
```

💡 Meaning: likely **remote command execution possible**

---

> [!success] 🧪 Evil-WinRM (post-access tool)

```bash
evil-winrm -i <target-IP> -u <username> -p <password>
```

✔ Gives interactive PowerShell session:

```powershell
*Evil-WinRM* PS C:\Users\user>
```

---

# 🔐 SSH (Secure Shell)

> [!info] 🧠 What is SSH?
> 
> ```bash
> A cryptographically secured protocol for remote login and command execution over TCP port 22.
> ```

💡 Security model:

- Symmetric encryption (session)
    
- Asymmetric encryption (key exchange)
    
- Hashing (integrity verification via MAC)
    

---

> [!success] 🔓 Hydra SSH brute force

```bash
hydra -L user.list -P password.list ssh://<target-IP>
```

---

> [!success] 🔑 SSH login

```bash
ssh user@<target-IP>
```

---

# 🖥️ RDP (Remote Desktop Protocol)

> [!info] 🧠 What is RDP?
> 
> ```bash
> A Microsoft protocol for full GUI remote control of Windows systems over TCP/3389.
> ```

💡 Features:

- GUI-based access
    
- Clipboard, file, and printer sharing
    
- Uses TCP + optional UDP
    

---

> [!success] 🔓 Hydra RDP brute force

```bash
hydra -L user.list -P password.list rdp://<target-IP>
```

---

> [!success] 🖥️ Connect with xfreerdp

```bash
xfreerdp /v:<target-IP> /u:user /p:password
```

---

# 📁 SMB (Server Message Block)

> [!info] 🧠 What is SMB?
> 
> ```bash
> A Windows file-sharing protocol used for remote file access, shares, and administrative access.
> ```

💡 Key ports:

- TCP 445
    

💡 Also known as:

- CIFS (legacy name)
    

---

> [!success] 🔓 Hydra SMB brute force

```bash
hydra -L user.list -P password.list smb://<target-IP>
```

---

> [!warning] ⚠️ SMBv3 Issues in Hydra
> 
> Older Hydra versions may fail due to SMBv3 incompatibility.
> 
> ✔ Fix: use Metasploit or NetExec instead

---

> [!success] 🧪 Metasploit SMB login scan

```bash
msfconsole -q
use auxiliary/scanner/smb/smb_login
set rhosts <target-IP>
set user_file user.list
set pass_file password.list
run
```

---

> [!success] 📡 NetExec SMB enumeration

```bash
netexec smb <target-IP> -u user -p password --shares
```

✔ Output includes:

- Shares
    
- Permissions (READ/WRITE/ADMIN)
    
- System info
    

---

> [!success] 📂 SMB file access

```bash
smbclient -U user \\\\<target-IP>\\SHARENAME
```

✔ Inside session:

```bash
ls
get file.txt
put file.txt
```

---

# 🧠 Authentication Concept (Cross-Service)

> [!info] 🧠 Core Idea
> 
> ```bash
> All network services rely on username + password OR key-based authentication.
> ```
> 
> 💡 Attack surfaces:

- Weak passwords → brute force
    
- Misconfigured services → unauthenticated access
    
- Reused credentials → lateral movement
    

---

# ⚡ Tooling Summary

---

|Tool|Purpose|
|:--|:--|
|**NetExec**|Multi-protocol authentication + enumeration|
|**Hydra**|Password brute forcing|
|**Evil-WinRM**|Interactive Windows shell|
|**xfreerdp**|RDP client|
|**smbclient**|SMB file interaction|
|**Metasploit**|Exploitation framework|

---

# 🧩 Mental Model

> [!quote] 🎯 How to think about network services
> 
> ```bash
> Service = Door
> Credentials = Key
> Protocol = Lock type
> Exploit tools = Lockpicks / master keys
> ```
> 
> 💡 If you understand:
> 
> - Port → service
>     
> - Service → auth method
>     
> - Auth method → tool
>     
> 
> → You can systematically attack or secure any network.

---
