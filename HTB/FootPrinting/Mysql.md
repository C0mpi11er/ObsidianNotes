---

# 🐬 MySQL Cheat Sheet

---

> [!info] 🧠 What is MySQL?
>
> ```bash
> Open-source Relational Database Management System (RDBMS)
> ```
>
> 💡 Key Characteristics:
>
> * Organizes data into **Tables** with defined columns/rows
> * Uses **SQL** (Structured Query Language) for interaction
> * Optimized for high performance and minimal storage footprint
>
> ✔ Found in:
>
> * Web Applications (WordPress, CMS)
> * Tech Stacks: **LAMP** (Linux, Apache, MySQL, PHP) or **LEMP** (Nginx)

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Client (App/CLI) ↔ Server (Database Engine)
> ```
>
> 💡 Network Defaults:
>
> * **TCP 3306** → Standard MySQL port
> * **localhost** → Standard default access (127.0.0.1)
>
> ✔ Think of it as **a high-speed, structured filing cabinet**

---

# 🌳 Structure: Databases & Tables

---

> [!info] 📚 Schema & Tables
>
> ```bash
> database → table → column/row
> ```
> 
>
> 💡 Vital System Schemas:
>
> * `information_schema` → Metadata about all other DBs (Tables, Privileges)
> * `sys` → Internal management and performance metrics
> * `mysql` → User accounts and global permission records

---

# 🔐 Security & Settings

---

| Feature | Importance | Risk |
| :--- | :--- | :--- |
| **user** | Service ownership | Running as `root` allows OS-level compromise |
| **password** | Access control | Plain-text storage in `.cnf` or script headers |
| **admin_address** | Management isolation | Exposure to public IP allows remote brute-force |
| **secure_file_priv** | File Operations | Can lead to LFI-to-RCE if improperly set |

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Key Enumeration Tools
>
> ```bash
> # 1. Service & Script Scan
> nmap <target> -sV -sC -p3306 --script mysql*
> 
> # 2. Connect Manually (Local/Remote)
> mysql -u <user> -p<password> -h <target_IP>
> 
> # 3. Check Version (Post-Auth)
> select version();
> ```
>
> 💡 Tip: Nmap scripts often report false-positive "empty passwords." Always verify manually.

---

> [!info] 🔍 Information Revealed
>
> * Database names & User hashes
> * Operating System details
> * Sensitive data: Usernames, Emails, Hashed Passwords
> * Internal file paths via error messages

---

# ⚠️ Common Misconfigurations

---

> [!danger] 🔓 Remote Access Enabled
>
> ```bash
> bind-address = 0.0.0.0
> ```
>
> 💡 Impact:
>
> * Allows any external IP to attempt authentication or exploit vulnerabilities.

---

> [!warning] ⚙️ Verbose Error Reporting
>
> ```bash
> sql_warnings = 1 / debug = 1
> ```
>
> 💡 Risk:
>
> * Leaks table structures or SQL logic to the front-end, aiding SQL Injection attacks.

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Navigation Flow
>
> ```sql
> 1. show databases;               -- List all available DBs
> 2. use <database_name>;          -- Switch to target DB
> 3. show tables;                  -- List all tables in DB
> 4. show columns from <table_name>; -- View table structure
> 5. select * from <table_name>;   -- Dump all data from table
> ```
>
> 💡 MySQL interaction is foundational for lateral movement and data exfiltration.

---

# 🧩 Mental Model

---

> [!quote] 🎯 SQL Logic
>
> ```bash
> Database → The Library
> Table → The Book
> Column → The Page Header
> Query (SELECT) → Reading the content
> ```
>
> 💡 You need to know which **Book** (Table) and **Page** (Column) to read to get the **Information** (Data).
```