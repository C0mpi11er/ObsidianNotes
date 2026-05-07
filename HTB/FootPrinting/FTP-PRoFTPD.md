# 🌐 FTP / ProFTPD Command Cheat Sheet

---

> [!info] 🧠 What is FTP?
>
> ```bash
> File Transfer Protocol (FTP)
> ```
>
> 💡 Used for:
>
> * File uploads/downloads
> * Remote file management
> * Anonymous file sharing
>
> ✔ Common port:
>
> ```bash
> TCP 21
> ```

---

> [!tip] 🔧 Target Identified
>
> ```bash
> 220 ProFTPD Server
> ```
>
> 💡 This means:
>
> * FTP service is running
> * Server software = ProFTPD
> * Ready for authentication attempts

---

# 🔍 Initial Enumeration

---

> [!success] 📡 Banner Grab
>
> ```bash
> nc <target> 21
> ```
>
> 💡 Example:
>
> ```bash
> nc 10.129.61.28 21
> ```
>
> ✔ Reveals:
>
> * FTP software
> * Server version
> * Welcome banner

---

> [!tip] 🧠 Check FTP Scripts with Nmap
>
> ```bash
> nmap -sV -sC -p21 <target>
> ```
>
> 💡 Useful for:
>
> * Anonymous login checks
> * Version detection
> * Misconfiguration discovery

---

# 🔑 Authentication Commands

---

> [!info] 👤 Specify Username
>
> ```bash
> USER admin
> ```
>
> 💡 Tells server which account to authenticate

---

> [!info] 🔐 Enter Password
>
> ```bash
> PASS password123
> ```
>
> 💡 Used after USER command

---

> [!success] 🕵️ Anonymous Login
>
> ```bash
> USER anonymous
> PASS anonymous
> ```
>
> 💡 Many FTP servers allow:
>
> * guest access
> * public file downloads

---

# 📂 Navigation Commands

---

> [!tip] 📍 Print Current Directory
>
> ```bash
> PWD
> ```
>
> 💡 Shows current working directory

---

> [!info] 📁 Change Directory
>
> ```bash
> CWD uploads
> ```
>
> 💡 Moves into another folder

---

> [!tip] ⬆️ Go Back One Directory
>
> ```bash
> CDUP
> ```
>
> 💡 Similar to:
>
> ```bash
> cd ..
> ```

---

# 📋 Listing Files

---

> [!success] 📄 List Files
>
> ```bash
> LIST
> ```
>
> 💡 Shows:
>
> * files
> * permissions
> * owners
> * timestamps

---

> [!tip] 📜 Name List Only
>
> ```bash
> NLST
> ```
>
> 💡 Cleaner output:
>
> * filenames only

---

# 📥 Downloading Files

---

> [!success] ⬇️ Download File
>
> ```bash
> RETR secret.txt
> ```
>
> 💡 Retrieves file from server

---

> [!tip] 🧠 Better Way (FTP Client)
>
> ```bash
> ftp <target>
> get secret.txt
> ```
>
> 💡 Easier for interactive downloads

---

# 📤 Uploading Files

---

> [!warning] ⬆️ Upload File
>
> ```bash
> STOR shell.php
> ```
>
> 💡 Attempts file upload
>
> ✔ Very important during pentests

---

> [!danger] 🎯 Why Upload Matters
>
> ```bash
> Upload writable web shell
> ```
>
> 💡 Can lead to:
>
> * remote code execution
> * website compromise

---

# 🧪 Useful FTP Commands

---

> [!info] 🖥️ Identify System Type
>
> ```bash
> SYST
> ```
>
> 💡 Returns:
>
> * OS information

---

> [!tip] ❓ Help Menu
>
> ```bash
> HELP
> ```
>
> 💡 Lists supported commands

---

> [!info] 📏 File Size
>
> ```bash
> SIZE backup.zip
> ```
>
> 💡 Displays file size

---

> [!tip] ⏰ File Modification Time
>
> ```bash
> MDTM file.txt
> ```
>
> 💡 Shows last modified timestamp

---

# ⚡ Interactive FTP Session

---

> [!success] 🧠 Full Workflow
>
> ```bash
> ftp 10.129.61.28
> ```
>
> Then:
>
> ```bash
> USER anonymous
> PASS anonymous
> LIST
> CWD uploads
> RETR secrets.txt
> STOR shell.php
> ```

---

# 🔍 Enumeration Strategy

---

> [!success] 🧠 FTP Pentest Flow
>
> ```bash
> 1. Scan port 21
> 2. Grab banner
> 3. Test anonymous login
> 4. Enumerate directories
> 5. Download interesting files
> 6. Check write permissions
> 7. Attempt upload
> ```
>
> 💡 Standard FTP attack workflow

---

# ⚠️ Common FTP Misconfigurations

---

> [!danger] 🔓 Anonymous Access
>
> ```bash
> USER anonymous
> ```
>
> 💡 Can expose:
>
> * sensitive files
> * backups
> * credentials

---

> [!warning] 📤 Writable Directories
>
> ```bash
> Upload allowed
> ```
>
> 💡 May lead to:
>
> * web shell upload
> * RCE

---

> [!warning] 🔐 Plaintext Credentials
>
> ```bash
> FTP transmits credentials in cleartext
> ```
>
> 💡 Vulnerable to:
>
> * sniffing
> * MITM attacks

---

# 🧩 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> FTP = Remote File System
> ```
>
> 💡 Main goals:
>
> * enumerate files
> * steal data
> * upload payloads
> * find credentials

---
