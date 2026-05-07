---

# 🌐 IMAP & POP3 Cheat Sheet

---

> [!info] 🧠 What are IMAP & POP3?
>
> ```bash
> Network protocols for accessing and managing emails on a remote server
> ```
>
> 💡 Key Differences:
>
> * **IMAP** → Online management, synchronization across devices, supports folders.
> * **POP3** → Basic listing, retrieving, and deleting; limited server-side functionality.
>
> ✔ Common in:
>
> * Enterprise mail environments
> * Personal email services (Gmail, Outlook, etc.)

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Client ↔ Mail Server (Dovecot/Exchange)
> ```
>
> 💡 Main protocols:
>
> * IMAP (143/993) → "Network File System" for emails
> * POP3 (110/995) → Download and (usually) delete
> * SMTP → Used for *sending* the emails
>
> ✔ Think of it as **Remote Storage vs. Local Download**

---

# 📥 IMAP (Internet Message Access Protocol)

---

> [!info] 📡 Default Ports
>
> ```bash
> TCP 143 (Plain/STARTTLS) / TCP 993 (SSL/TLS)
> ```
>
> 💡 Used for:
>
> * Real-time synchronization
> * Server-side folder management

---

> [!success] 🔗 Essential IMAP Commands
>
> ```bash
> 1 LOGIN username password        # Authenticate
> 1 LIST "" *                      # List all directories
> 1 SELECT INBOX                   # Access a mailbox
> 1 FETCH <ID> all                 # Retrieve message data
> 1 LOGOUT                         # Close connection
> ```
>
> 💡 Note: IMAP uses identifiers (like `1`) to track command/response pairs.

---

# 📬 POP3 (Post Office Protocol v3)

---

> [!info] 📡 Default Ports
>
> ```bash
> TCP 110 (Plain/STARTTLS) / TCP 995 (SSL/TLS)
> ```
>
> 💡 Used for:
>
> * Simple retrieval
> * Minimalistic command set

---

> [!success] 🔗 Essential POP3 Commands
>
> ```bash
> USER username                    # Identify user
> PASS password                    # Authenticate
> STAT                             # Get number of emails
> LIST                             # List email IDs and sizes
> RETR <ID>                        # Retrieve specific email
> QUIT                             # Close connection
> ```

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Nmap Scanning
>
> ```bash
> sudo nmap <target> -sV -sC -p110,143,993,995
> ```
>
> 💡 Reveals:
>
> * Software versions (e.g., Dovecot)
> * SSL Certificate details (Org name, Hostname, Email)
> * Supported capabilities (AUTH=PLAIN, STARTTLS)

---

> [!tip] 🛠️ Interaction Tools
>
> ```bash
> # Connect via cURL (IMAPS)
> curl -k 'imaps://<target>' --user user:pass -v
> 
> # Connect via OpenSSL (Encrypted)
> openssl s_client -connect <target>:993
> openssl s_client -connect <target>:995
> ```
>
> 💡 Gives raw access to the protocol banners and command line.

---

# ⚠️ Common Misconfigurations

---

> [!danger] 🔓 Insecure Transmission
>
> ```bash
> Cleartext authentication over port 143/110
> ```
>
> 💡 Impact:
>
> * Credentials and emails can be sniffed via MITM

---

> [!warning] ⚙️ Verbose Logging (Dovecot)
>
> ```bash
> auth_debug_passwords = yes
> auth_verbose = yes
> ```
>
> 💡 Risk:
>
> * Passwords and unsuccessful attempts are logged to disk, increasing exposure if the server is breached.

---

> [!warning] 👤 Anonymous Access
>
> ```bash
> auth_anonymous_username
> ```
>
> 💡 Risk:
>
> * Potential unauthorized access to public or shared mailboxes.

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Attack Flow
>
> ```bash
> 1. Scan 110, 143, 993, 995
> 2. Inspect SSL cert for hostnames/usernames
> 3. Use found credentials (e.g., from SMTP/reused)
> 4. Authenticate via OpenSSL or Evil-WinRM
> 5. Search for sensitive info (Passwds, VPN configs) in folders
> ```
>
> 💡 Email is a goldmine for lateral movement credentials

---

# 🧩 Mental Model

---

> [!quote] 🎯 Protocol Comparison
>
> ```bash
> IMAP → Syncs (Cloud-like)
> POP3 → Downloads (Physical-like)
> SMTP → Delivers (Postman)
> ```
>
> 💡 IMAP is preferred for modern workflows; POP3 is legacy but still common.
```