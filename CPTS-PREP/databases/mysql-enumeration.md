## 🛰️ MySQL Server Enumeration Guide

### 🔍 System Information Gathering

**Step 1: Service Detection**

```bash
# Use nmap to detect the service and version on port 3306
nmap -p3306 -sV target_ip_address
```

Example Output:
```
PORT    STATE SERVICE  VERSION
3306/tcp open  mysql    MySQL 8.0.27-0ubuntu0.20.04.1
```

**Step 2: Version Extraction**

```bash
# Look for the version string from nmap output
MySQL X.X.XX (e.g., MySQL 8.0.27)
```

### 🔍 Database Enumeration

**Connecting to MySQL Server**
```bash
mysql -u robin -probin -h target_ip_address --ssl=0
```

**List All Databases**
```sql
show databases;
```

Example Output:
```
+--------------------+
| Database           |
+--------------------+
| customers          |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.085 sec)
```

**Select the Customers Database**
```sql
use customers;
```

**List Tables in Selected Database**
```sql
show tables;
```

Example Output:
```
+---------------------+
| Tables_in_customers |
+---------------------+
| myTable             |
+---------------------+
1 row in set (0.078 sec)
```

**Describe Table Structure**
```sql
describe myTable;
```

Example Output:
```
+-----------+--------------------+------+-----+---------+----------------+
| Field     | Type               | Null | Key | Default | Extra          |
+-----------+--------------------+------+-----+---------+----------------+
| id        | mediumint unsigned | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255)       | YES  |     | NULL    |                |
| email     | varchar(255)       | YES  |     | NULL    |                |
| country   | varchar(100)       | YES  |     | NULL    |                |
| postalZip | varchar(20)        | YES  |     | NULL    |                |
| city      | varchar(255)       | YES  |     | NULL    |                |
| address   | varchar(255)       | YES  |     | NULL    |                |
| pan       | varchar(255)       | YES  |     | NULL    |                |
| cvv       | varchar(255)       | YES  |     | NULL    |                |
+-----------+--------------------+------+-----+---------+----------------+
9 rows in set (0.079 sec)
```

**Extract Specific Data**
```sql
SELECT email FROM myTable WHERE name = "Otto Lang";
```

Example Output:
```
+---------------------+
| email               |
+---------------------+
| ultrices@google.htb |
+---------------------+
1 row in set (0.078 sec)
```

**Result:**
```plaintext
ultrices@google.htb
```

### 🔍 System Schema Exploration

**Connect to MySQL System Database**
```sql
use mysql;
show tables;
```

Example Output:
```
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
...SNIP...
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.002 sec)
```

**Connect to Sys Database for Metadata**
```sql
use sys;
show tables;
select host, unique_users from host_summary;
```

Example Output:
```
mysql> show tables;
+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
...SNIP...
+-----------------------------------------------+
2 rows in set (0,01 sec)
```

### 🛠️ Essential MySQL Commands

#### Connection and Basic Operations
| Command | Description |
|---------|-------------|
| `mysql -u <user> -p<password> -h <IP address>` | Connect to MySQL server (no space between `-p` and password) |
| `show databases;` | Show all databases |
| `use <database>;` | Select one of the existing databases |
| `show tables;` | Show all available tables in the selected database |
| `show columns from <table>;` | Show all columns in the selected table |
| `select * from <table>;` | Show everything in the desired table |
| `select * from <table> where <column> = "<string>";` | Search for needed string in the desired table |

#### Advanced Query Examples
```sql
# Database exploration
SHOW DATABASES;
USE customers;
SHOW TABLES;
DESCRIBE customers;

# Data extraction
SELECT * FROM customers;
SELECT * FROM customers WHERE name = 'Otto Lang';
SELECT email FROM customers WHERE name = 'Otto Lang';

# User enumeration
SELECT User, Host FROM mysql.user;
SELECT * FROM mysql.user WHERE User='root';
```

### 🛠️ Database Schema Information

#### Important System Databases
- **information_schema**: Contains metadata about all databases (ANSI/ISO standard)
- **mysql**: Contains MySQL server system data and configurations
- **performance_schema**: Contains performance monitoring information
- **sys**: Contains system schema with interpreted performance data

**Schema Differences:**
- **System Schema**: Microsoft system catalog (more comprehensive)
- **Information Schema**: ANSI/ISO standard metadata (standardized)

---

### 📜 HTB Academy Lab Questions

#### Question 1: Version Detection
**Task**: Enumerate the MySQL server and determine the version in use  
**Format**: MySQL X.X.XX

**Solution**:
```bash
# Step 1: Service detection
nmap -p3306 -sV target_ip_address

# Step 2: Version extraction from nmap output
# Look for: mysql   MySQL 8.0.27-0ubuntu0.20.04.1

# Step 3: Format the answer
# Answer: MySQL 8.0.27
```

#### Question 2: Data Extraction
**Task**: Using credentials "robin:robin", find email address of customer "Otto Lang"

**Solution**:
```bash
# Step 1: Connect with provided credentials (with SSL disabled)
mysql -u robin -probin -h target_ip_address --ssl=0

# Step 2: List all databases
MySQL [(none)]> show databases;

# Step 3: Select the customers database
MySQL [(none)]> use customers;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

# Step 4: List tables in the customers database
MySQL [customers]> show tables;

# Step 5: Examine table structure
MySQL [customers]> describe myTable;
  
# Step 6: Extract Otto Lang's email address
MySQL [customers]> SELECT email FROM myTable WHERE name = "Otto Lang";
+---------------------+
| email               |
+---------------------+
| ultrices@google.htb |
+---------------------+
1 row in set (0.078 sec)

# Result: ultrices@google.htb
```

### 🛡️ Security Assessment

#### Common Vulnerabilities
1. **Default Credentials**: Testing root with empty password
2. **Weak Passwords**: Common password patterns
3. **Information Disclosure**: Version information, database names
4. **Excessive Privileges**: Users with unnecessary permissions
5. **Configuration Issues**: Dangerous settings enabled
6. **Network Exposure**: MySQL accessible from external networks

#### Enumeration Checklist
- [ ] Port scan for 3306
- [ ] Service version detection
- [ ] Default credential testing
- [ ] Anonymous access testing
- [ ] Database enumeration
- [ ] User account discovery
- [ ] Privilege assessment
- [ ] Configuration analysis
- [ ] Data extraction testing

### 📚 MariaDB Relationship
**MariaDB** is a fork of MySQL created when Oracle acquired MySQL AB. Key points:
- Created by original MySQL chief developer
- Based on MySQL source code
- Often used interchangeably with MySQL
- Compatible with MySQL protocols and commands
- Common in Linux distributions

## 📘 Reference Documentation
- **MySQL Reference Manual**: Comprehensive configuration options
- **Security Issues Section**: Best practices for securing MySQL servers
- **HTB Academy**: Practical enumeration techniques
- **Penetration Testing**: Real-world attack scenarios