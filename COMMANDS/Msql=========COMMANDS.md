---

# 🗄️ MySQL Database Navigation Cheat Sheet

---

> [!info] 🧠 What is Database Navigation?
>
> ```bash
> Moving through MySQL structures to find data
> ```
>
> 💡 You use it to:
>
> * explore databases
> * locate tables
> * inspect columns
> * extract data
>
> ✔ Think of it as **mapping the database from top to bottom**

---

# 📂 Show Available Databases

---

> [!info] 🔍 List all databases
>
> ```sql
> SHOW DATABASES;
> ```
>
> 💡 What it does:
>
> * shows all databases on the server
>
> ✔ First step after login

---

# 📌 Select a Database

---

> [!tip] 🎯 Choose a database
>
> ```sql
> USE <database_name>;
> ```
>
> 💡 Example:
>
> ```sql
> USE mysql;
> ```
>
> ✔ This “enters” the database you want to work with

---

# 📋 Show Tables in a Database

---

> [!success] 🧾 List tables
>
> ```sql
> SHOW TABLES;
> ```
>
> 💡 What it does:
>
> * displays all tables inside selected database
>
> ✔ This reveals data structure (users, logs, configs)

---

# 🧱 View Table Structure

---

> [!info] 🧬 Show columns
>
> ```sql
> SHOW COLUMNS FROM <table>;
> ```
>
> 💡 Example:
>
> ```sql
> SHOW COLUMNS FROM users;
> ```
>
> ✔ Shows:
>
> * column names
> * data types
> * structure design

---

# 🔍 Inspect Data in Tables

---

> [!success] 📊 View everything
>
> ```sql
> SELECT * FROM <table>;
> ```
>
> 💡 Example:
>
> ```sql
> SELECT * FROM users;
> ```
>
> ✔ Dumps full table contents

---

> [!tip] 🎯 Filter specific data
>
> ```sql
> SELECT * FROM <table> WHERE <column> = "<value>";
> ```
>
> 💡 Example:
>
> ```sql
> SELECT * FROM users WHERE username = "admin";
> ```
>
> ✔ Used to locate specific records

---

# 🧠 Database Context Info

---

> [!info] 🧭 Current database
>
> ```sql
> SELECT DATABASE();
> ```
>
> 💡 Shows:
>
> * which database is currently active

---

> [!tip] 👤 Current user
>
> ```sql
> SELECT USER();
> ```
>
> 💡 Shows:
>
> * logged-in MySQL account

---

# 🧾 System Database Navigation

---

> [!info] 🧠 Important system DBs
>
> ```bash
> information_schema
> mysql
> performance_schema
> sys
> ```
>
> 💡 Purpose:
>
> * metadata storage
> * user accounts
> * system configuration
>
> ✔ Always present by default

---

# 🧠 Key System Table Navigation

---

> [!tip] 👤 User table
>
> ```sql
> SELECT * FROM mysql.user;
> ```
>
> 💡 Shows:
>
> * usernames
> * hosts
> * privileges
> * authentication data

---

> [!info] 🧾 Database privileges
>
> ```sql
> SELECT * FROM mysql.db;
> ```
>
> 💡 Shows:
>
> * access permissions per database

---

# ⚡ Navigation Workflow

---

> [!success] 🧠 Typical flow
>
> ```bash
> 1. SHOW DATABASES;
> 2. USE target_db;
> 3. SHOW TABLES;
> 4. SHOW COLUMNS FROM table;
> 5. SELECT * FROM table;
> ```
>
> 💡 This is how you fully explore a MySQL server

---

# 🧩 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> Server → Databases → Tables → Columns → Rows → Data
> ```
>
> 💡 Navigation is just:
>
> > “Drilling deeper until you reach useful data”

---

