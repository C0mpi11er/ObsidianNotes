

---

# 🧠 HTB Fingerprinting Speed Sheet (Real Exam Use)

---

# 📡 RPCBIND / PORTMAPPER (111)

---

> [!info] 🧠 What it means
>
> ```bash
> Service registry for RPC services (NFS, NIS, etc.)
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> rpcinfo -p <IP>
> nmap -sV -p111 --script rpcinfo <IP>
> ```

> [!danger] 💥 Why it matters
>
> * Reveals **ALL RPC services + ports**
> * Usually leads to NFS / mountd / nlockmgr
> * Entry point to full filesystem exposure chain

> [!success] ⚡ Follow-up move
>
> ```bash
> showmount -e <IP>
> nmap --script nfs* -p2049 <IP>
> ```

---

# 📦 NFS (2049)

---

> [!info] 🧠 What it means
>
> ```bash
> Remote filesystem sharing (Linux/Unix)
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> showmount -e <IP>
> mount -t nfs <IP>:/share /mnt
> ls -la /mnt
> ```

> [!danger] 💥 What actually breaks it
>
> * `no_root_squash` → ROOT escalation
> * writable exports → shell drop
> * UID mismatch → impersonation

> [!success] ⚡ Exploit path
>
> ```bash
> upload ssh key → mount → root access pivot
> ```

---

# 🗂 SMB (445)

---

> [!info] 🧠 What it means
>
> ```bash
> Windows file sharing + auth system
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> smbclient -L //<IP> -N
> enum4linux-ng <IP>
> crackmapexec smb <IP>
> ```

> [!danger] 💥 Real vulns
>
> * NULL session → full enum
> * Guest share access
> * NTLM relay potential
> * MS17-010 (EternalBlue)

> [!success] ⚡ Quick win check
>
> ```bash
> look for writable shares → upload payload
> ```

---

# 📡 FTP (21)

---

> [!info] 🧠 What it means
>
> ```bash
> File transfer service (often misconfigured)
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> ftp <IP>
> anonymous:anonymous
> nmap --script ftp-anon,ftp-syst -p21 <IP>
> ```

> [!danger] 💥 Real vulns
>
> * anonymous login
> * writable upload directory
> * weak vsFTPd backdoor (2.3.4)

---

# 🌐 DNS (53)

---

> [!info] 🧠 What it means
>
> ```bash
> Name resolution + internal mapping leaks
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> dig axfr @<IP> domain.com
> dnsrecon -d domain.com -t std
> ```

> [!danger] 💥 Real vulns
>
> * zone transfer (AXFR)
> * internal subdomain leak
> * misconfigured recursion

---

# 📧 SMTP (25)

---

> [!info] 🧠 What it means
>
> ```bash
> Email routing system
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> smtp-user-enum -M VRFY -U users.txt -t <IP>
> nmap --script smtp-enum-users -p25 <IP>
> ```

> [!danger] 💥 Real vulns
>
> * user enumeration (VRFY/EXPN)
> * open relay
> * spoofing emails

---

# 📬 IMAP / POP3 (143 / 110)

---

> [!info] 🧠 What it means
>
> ```bash
> Mail access protocols
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> telnet <IP> 110
> telnet <IP> 143
> nmap --script imap-capabilities,pop3-capabilities <IP>
> ```

> [!danger] 💥 Real vulns
>
> * plaintext login
> * weak creds
> * mailbox dumping

---

# 📡 SNMP (161)

---

> [!info] 🧠 What it means
>
> ```bash
> Device + system info leak protocol
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> snmpwalk -v2c -c public <IP>
> onesixtyone -c community.txt <IP>
> ```

> [!danger] 💥 Real vulns
>
> * public/private strings
> * full system enumeration
> * process + network leaks

---

# 🗄 MySQL (3306)

---

> [!info] 🧠 What it means
>
> ```bash
> Database server
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> mysql -h <IP> -u root
> nmap --script mysql-empty-password <IP>
> ```

> [!danger] 💥 Real vulns
>
> * root no password
> * remote DB access
> * data dump

---

# 🏢 MSSQL (1433)

---

> [!info] 🧠 What it means
>
> ```bash
> Microsoft database server
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> crackmapexec mssql <IP>
> impacket-mssqlclient
> ```

> [!danger] 💥 Real vulns
>
> * weak SA creds
> * xp_cmdshell → RCE
> * linked DB pivoting

---

# 🧬 Oracle TNS (1521)

---

> [!info] 🧠 What it means
>
> ```bash
> Oracle DB listener service
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> tnscmd10g version -h <IP>
> nmap --script oracle-sid-brute <IP>
> ```

> [!danger] 💥 Real vulns
>
> * SID brute force
> * default creds
> * DB enumeration

---

# ⚙️ IPMI (623)

---

> [!info] 🧠 What it means
>
> ```bash
> Server hardware management (BMC)
> ```

> [!tip] 🔍 Fast checks
>
> ```bash
> ipmitool -I lanplus -H <IP> -U admin -P admin chassis status
> nmap --script ipmi-version <IP>
> ```

> [!danger] 💥 Real vulns
>
> * default creds (ADMIN/ADMIN)
> * hash dump attack
> * remote power control

---

# 🧠 FINAL LAB RULE

---

> [!quote] 🎯 Speed Mindset
>
> ```bash
> Service → Default creds → Misconfig → Data exposure → RCE
> ```

💡 Don’t enumerate blindly —
👉 every service has **2–3 known breakpoints**

---


