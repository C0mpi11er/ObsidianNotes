---

# 🛡️ MSSQL (Microsoft SQL Server) Cheat Sheet

---

> [!info] 🧠 What is MSSQL?
>
> ```bash
> Microsoft's proprietary, SQL-based relational database management system
> ```
> 
> 💡 Key Characteristics:
>
> * **Closed-source** and deeply integrated with the Windows ecosystem
> * Primary choice for applications built on the **.NET framework**
> * Uses **T-SQL** (Transact-SQL) as its extension of the SQL language
>
> ✔ Common in:
>
> * Enterprise Windows environments
> * Active Directory-integrated applications

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Client (SSMS/mssql-cli) ↔ Server (SQL Database Engine)
> ```
>
> 💡 Network Defaults:
>
> * **TCP 1433** → Default MSSQL port
> * **UDP 1434** → SQL Browser Service (used for named instances)
>
> ✔ Think of it as **the database backbone for Windows Enterprises**

---

# 🌳 Structure: Default System Databases

---

| Database | Description |
| :--- | :--- |
| **master** | Tracks all system-level info (logins, config settings) |
| **model** | Template for every new database created |
| **msdb** | Used by SQL Server Agent for alerts, jobs, and history |
| **tempdb** | Workspace for temporary objects (re-created on restart) |

---

# 🔐 Authentication & Security

---

> [!info] 🆔 Authentication Modes
>
> 1. **Windows Authentication:** Relies on the OS/Active Directory. No separate SQL password is used.
> 2. **Mixed Mode:** Allows both Windows accounts and specific SQL logins (like the `sa` account).
>
> 💡 **The `sa` Account:** The "System Administrator" account. Misconfigured or weak `sa` passwords are a primary target for attackers.

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Key Enumeration Tools
>
> ```bash
> # 1. Detailed Nmap Script Scan
> nmap --script ms-sql-info,ms-sql-ntlm-info,ms-sql-empty-password -p 1433 <target>
> 
> # 2. Metasploit Ping (Useful for instance discovery)
> msf6 > use auxiliary/scanner/mssql/mssql_ping
> 
> # 3. Impacket Connection (Linux-to-Windows)
> mssqlclient.py Administrator@<target> -windows-auth
> ```
> 
> 💡 Tip: MSSQL often uses **Named Pipes**. Identifying the pipe path can be crucial for lateral movement.

---

# ⚠️ Common Misconfigurations

---

> [!danger] 🔓 Default/Weak `sa` Credentials
>
> ```bash
> # Trying common passwords for the sa account
> mssqlclient.py sa:Password123@<target>
> ```
>
> 💡 Impact:
>
> * Full control over the database engine and potentially the underlying OS.

---

> [!warning] ⚙️ Unencrypted Connections
>
> ```bash
> # Force connection without encryption
> mssqlclient.py <user>@<target> -no-encrypt
> ```
>
> 💡 Risk:
>
> * Credentials and sensitive data can be sniffed over the network via Man-in-the-Middle (MitM) attacks.

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Interaction Flow (T-SQL)
>
> ```sql
> -- 1. List all databases
> SELECT name FROM sys.databases;
> 
> -- 2. Check current user permissions
> SELECT IS_SRVROLEMEMBER('sysadmin');
> 
> -- 3. Execute system commands (if xp_cmdshell is enabled)
> EXEC xp_cmdshell 'whoami';
> ```
>
> 💡 **Privilege Escalation:** If you compromise a domain account that is a local `sysadmin` in SQL, you can often pivot to full SYSTEM control of the server.

---

# 🧩 Mental Model

---

> [!quote] 🎯 Windows Integration
>
> ```bash
> Active Directory → The Keycard
> Windows Auth → The Automatic Door
> sa Account → The Master Key
> master DB → The Security Log
> ```
>
> 💡 In MSSQL, the database security is often only as strong as the **Windows Domain security** surrounding it.
```