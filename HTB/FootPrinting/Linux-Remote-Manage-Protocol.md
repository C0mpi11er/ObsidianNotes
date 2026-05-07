
---

# 🐧 Linux Remote Management Protocols Cheat Sheet

---

> [!info] 🧠 What are Remote Management Protocols?
>
> ```bash
> Methods to access and control systems remotely
> ```
>
> 💡 Used to:
>
> * troubleshoot systems
> * manage servers from anywhere
> * transfer files & execute commands
>
> ✔ Think: **“control another machine without being there”**

---

> [!tip] 🎯 Why Attackers Care
>
> ```bash
> Remote access = direct system entry point
> ```
>
> 💡 If misconfigured:
>
> * easy unauthorized access
> * credential abuse
> * full system compromise
>
> ✔ High-value targets in pentesting

---

# 🔐 SSH (Secure Shell)

---

> [!info] 🧠 What is SSH?
>
> ```bash
> Encrypted remote access protocol (TCP 22)
> ```
>
> 💡 Features:
>
> * secure login
> * command execution
> * file transfer (SCP/SFTP)
>
> ✔ Replaced insecure protocols like Telnet

---

> [!tip] 🔑 Authentication Methods
>
> ```bash
> Password
> Public Key (MOST SECURE)
> Host-based
> Keyboard-interactive
> GSSAPI
> ```
>
> 💡 Most common:
>
> * password → weak if brute-forced
> * key-based → strong authentication

---

> [!success] 🔐 Public Key Auth Flow
>
> ```bash
> Client (private key) ↔ Server (public key)
> ```
>
> 💡 Flow:
>
> * server sends challenge
> * client signs with private key
> * server verifies → grants access
>
> ✔ No password needed after setup

---

> [!warning] ⚠️ Dangerous SSH Settings
>
> ```bash
> PasswordAuthentication yes
> PermitRootLogin yes
> PermitEmptyPasswords yes
> X11Forwarding yes
> ```
>
> 💡 Risks:
>
> * brute-force attacks
> * root compromise
> * command injection
>
> ✔ Misconfig = easy entry

---

> [!tip] 🔍 SSH Enumeration
>
> ```bash
> ssh-audit <IP>
> ```
>
> 💡 Reveals:
>
> * version
> * weak algorithms
> * supported auth methods

---

# 📦 Rsync (File Transfer)

---

> [!info] 🧠 What is Rsync?
>
> ```bash
> Fast file sync & transfer tool (TCP 873)
> ```
>
> 💡 Key feature:
>
> * transfers only file differences (delta transfer)
>
> ✔ Used for backups & mirroring

---

> [!tip] 🔍 Enumeration
>
> ```bash
> nc -nv <IP> 873
> ```
>
> 💡 Then:
>
> ```bash
> #list
> ```
>
> ✔ Lists available shares

---

> [!success] 📂 List Files
>
> ```bash
> rsync -av --list-only rsync://<IP>/<share>
> ```
>
> 💡 Can reveal:
>
> * configs
> * secrets
> * SSH keys
>
> ✔ Often accessible without auth

---

> [!warning] ⚠️ Risk
>
> ```bash
> Open rsync share = data exposure
> ```
>
> 💡 Leads to:
>
> * credential leaks
> * lateral movement

---

# ⚠️ R-Services (LEGACY & INSECURE)

---

> [!danger] 🧠 What are R-Services?
>
> ```bash
> Old Unix remote access protocols (UNENCRYPTED)
> ```
>
> 💡 Includes:
>
> * rlogin
> * rsh
> * rexec
> * rcp
>
> ✔ Runs on:
>
> ```bash
> 512 / 513 / 514 TCP
> ```

---

> [!warning] 🚨 Major Weakness
>
> ```bash
> No encryption + trust-based authentication
> ```
>
> 💡 Vulnerable to:
>
> * MITM attacks
> * credential sniffing
> * unauthorized access

---

> [!info] 🔓 Trust Files
>
> ```bash
> /etc/hosts.equiv
> ~/.rhosts
> ```
>
> 💡 Define:
>
> * trusted users
> * trusted hosts
>
> ✔ If misconfigured → login WITHOUT password

---

> [!danger] 🚨 Dangerous Example
>
> ```bash
> + +
> ```
>
> 💡 Means:
>
> * ANY user from ANY host can login
>
> ✔ Full compromise

---

> [!success] 🔑 Exploitation Example
>
> ```bash
> rlogin <IP> -l user
> ```
>
> 💡 If trusted → instant shell

---

> [!tip] 👤 User Enumeration
>
> ```bash
> rwho
> rusers -al <IP>
> ```
>
> 💡 Shows:
>
> * logged-in users
> * active sessions
>
> ✔ Helps target valid accounts

---

# 🔍 Footprinting

---

> [!info] 📡 Scan Remote Services
>
> ```bash
> nmap -p 22,873,512,513,514 -sV <IP>
> ```
>
> 💡 Detects:
>
> * SSH
> * Rsync
> * R-services

---

# 🧠 Attacker Mindset

---

> [!quote] 🎯 Think Like This
>
> ```bash
> Can I login?
> Can I brute-force?
> Can I reuse credentials?
> Can I access files?
> ```
>
> 💡 Remote services = **entry points**

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Remote Access Recon Flow
>
> ```bash
> 1. Scan ports (22, 873, 512-514)
> 2. Identify service (SSH/Rsync/R-services)
> 3. Enumerate configs & versions
> 4. Test credentials / brute force
> 5. Check misconfigurations
> 6. Gain access / extract data
> ```
>
> 💡 Often leads to:
>
> * shell access
> * credential reuse
> * full compromise

---

# 🧩 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> Remote Service → Auth → Misconfig → Access → Control
> ```
>
> 💡 If authentication or trust is weak → system is yours

---
