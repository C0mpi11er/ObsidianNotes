# 🛠️ MySQL - Port 3306, Default Credentials, SQL Injection

---

## 🔍 Initial Enumeration

### Nmap Scan

```bash
nmap -p 3306 <IP>
```

Check if MySQL service is running on port 3306.

---

## ⚙️ Login Attempts with Default Credentials

Try default credentials to log in:

```text
mysql -u root -p
```
Default password for testing environments might be `root`.

> [!WARNING]
> Always ensure you are not targeting a production environment before using default credentials or attempting SQL injection.

---

## 📄 SQL Injection Vulnerabilities

### Testing with `UNION SELECT`

Perform basic SQL injection test:

```sql
SELECT * FROM information_schema.tables WHERE table_schema = 'database_name';
```
Substitute `'database_name'` with the actual database name to check for tables within that schema.

> [!EXAMPLE]
> Example command:
>
> ```sql
> mysql -u root -p --execute="SELECT * FROM information_schema.tables WHERE table_schema='mysql';"
> ```

---

## 🔍 Database Enumeration

### Schema Details

List all databases:

```sql
SHOW DATABASES;
```

Select a specific database for further enumeration:

```sql
USE database_name;
```

---

## 📄 Table and Column Information

Retrieve list of tables within the selected database:

```sql
SHOW TABLES;
```

Get column names from a table:

```sql
DESCRIBE table_name;
```
or
```sql
SELECT * FROM information_schema.columns WHERE table_name = 'table_name';
```

---

## 🔍 Data Retrieval

### Query Sensitive Information

Extract sensitive data such as usernames and passwords from the database. Example queries to get user credentials:

```sql
SELECT username, password FROM users;
```
or
```sql
SELECT * FROM mysql.user;
```

> [!NOTE]
> Ensure you have appropriate privileges or permissions before extracting sensitive information.

---

## ⚠️ Mitigation Strategies

- Always change default passwords.
- Enable strong authentication mechanisms.
- Regularly audit and update database configurations.
- Implement Web Application Firewall (WAF) rules to prevent SQL injection attacks.

---

## 🧠 Exam Mental Model

```text
MySQL Open?
   ↓
Default Credentials?
      → Login
   ↓
Tables, Columns Info?
      → Query Sensitive Data
```

> [!SUCCESS]
> **Rule of Thumb for MySQL Exploitation:**
>
> - Always check default credentials.
> - Use SQL injection techniques to enumerate and extract data if necessary.

---