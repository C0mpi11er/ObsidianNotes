🧪 SQL*Plus Cheat Sheet (Oracle Database CLI)

[!info] 🧠 What is SQL*Plus?

Oracle command-line client used to interact with databases

💡 Used to:

execute SQL queries
enumerate database structure
extract sensitive data

✔ Think: “Oracle database terminal shell”

[!tip] 🔧 Core Idea

User → SQL*Plus → Oracle DB → Tables → Data

💡 You can:

query tables
list users
extract hashes (if privileged)

✔ Direct interaction with database engine

🔌 Connecting to Oracle

[!success] 🔗 Basic Login

sqlplus <user>/<password>@<IP>/<SID>

💡 Example:

sqlplus scott/tiger@10.10.10.10/XE

✔ Connects to Oracle instance

[!tip] 👑 SYSDBA Login (High Privilege)

sqlplus <user>/<password>@<IP>/<SID> as sysdba

💡 Gives:

full database control
admin-level access

✔ Highest privilege mode

🔍 Database Enumeration

[!info] 📂 List Tables

SELECT table_name FROM all_tables;

💡 Shows:

all accessible tables

✔ Core enumeration step

[!tip] 📄 List Columns

SELECT column_name FROM all_tab_columns WHERE table_name='USERS';

💡 Shows:

structure of a table

✔ Helps identify sensitive fields

[!success] 👤 List Users

SELECT username FROM all_users;

💡 Shows:

database accounts

✔ Useful for privilege mapping

[!info] 🔐 User Privileges

SELECT * FROM user_role_privs;

💡 Shows:

assigned roles
access level

✔ Determines attack capability

🔥 Sensitive Data Extraction

[!danger] 🔑 Password Hashes

SELECT name, password FROM sys.user$;

💡 Used to:

extract password hashes

✔ Requires high privileges

[!warning] 📊 Current User

SELECT user FROM dual;

💡 Shows:

current session user

✔ Basic recon check

[!info] 🧾 Database Version

SELECT banner FROM v$version;

💡 Shows:

Oracle version

✔ Helps identify exploits

⚙️ Navigation Commands

[!tip] 📌 Switch Context

USE <database>;

💡 (Oracle equivalent via schema change)

[!info] 🔎 Show Privileges

SELECT * FROM session_privs;

💡 Shows:

active privileges in session

✔ Important for privilege escalation checks

🧠 SQL*Plus Attack Mindset

[!quote] 🎯 Think Like This

Connect → Enumerate → Pivot → Extract → Escalate

💡 SQL*Plus gives:

full database visibility
sensitive data exposure

✔ The database = application brain

[!warning] ⚠️ Common Mistake

Only checking if login works

❌ Real value is:

table enumeration
user extraction
privilege abuse
⚡ Real-World Workflow

[!success] 🧠 SQL*Plus Flow

1. Connect (sqlplus)
2. Identify user privileges
3. Enumerate tables
4. Extract sensitive data
5. Escalate to SYSDBA (if possible)

💡 Full Oracle database takeover path

🧩 Mental Model

[!quote] 🎯 Think Like This

Oracle DB → Users → Tables → Columns → Secrets

💡 SQL*Plus = direct window into database core