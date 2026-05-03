
---

# 🧠 FTP Command Cheat Sheet

---

> [!info] 🌐 What is FTP?
>
> 💡 **FTP (File Transfer Protocol)** is used to:
>
> * Upload files 📤
> * Download files 📥
> * Manage remote systems
>
> 🎯 Common in **pentesting, file transfers, misconfig exploits**

---

# 🔌 Connection & Session Control

---

> [!success] 🔗 Connect / Disconnect
>
> ```bash
> open <ip/domain>
> ```
>
> ```bash
> user <username>
> ```
>
> ```bash
> bye / quit
> ```
>
> ```bash
> close / disconnect
> ```
>
> 💡 Start, authenticate, and end sessions

---

> [!tip] 📡 Check Status
>
> ```bash
> status
> ```
>
> 💡 Shows current FTP connection info

---

# 📂 Navigation (Remote & Local)

---

> [!info] 📁 Directory Navigation
>
> ```bash
> cd <dir>        # Remote directory
> lcd <dir>       # Local directory
> pwd             # Show remote directory
> ```
>
> 💡 Always know where you are

---

> [!tip] 📜 List Files
>
> ```bash
> ls
> dir
> mls
> mdir
> ```
>
> 💡 View files on remote system

---

# 📥 Downloading Files

---

> [!success] ⬇️ Download Files
>
> ```bash
> get file.txt
> ```
>
> ```bash
> mget *.txt
> ```
>
> ```bash
> recv file.txt
> ```
>
> 💡 Single or multiple downloads

---

# 📤 Uploading Files

---

> [!success] ⬆️ Upload Files
>
> ```bash
> put file.txt
> ```
>
> ```bash
> mput *.txt
> ```
>
> ```bash
> send file.txt
> ```
>
> 💡 Upload payloads or tools during pentest

---

# 🗂️ File Management

---

> [!info] 🛠️ Manage Files
>
> ```bash
> delete file.txt
> mdelete *.txt
> ```
>
> ```bash
> rename old.txt new.txt
> ```
>
> ```bash
> append local.txt remote.txt
> ```
>
> 💡 Modify or remove remote files

---

> [!tip] 📁 Directory Management
>
> ```bash
> mkdir new_folder
> rmdir old_folder
> ```
>
> 💡 Create/delete directories

---

# ⚙️ Transfer Modes

---

> [!info] 🔄 File Transfer Types
>
> ```bash
> ascii     # Text files (default)
> binary    # Images, executables, etc.
> type      # Show/set mode
> ```
>
> 💡 Always use **binary for exploits/files**

---

# 🧪 Debugging & Output

---

> [!tip] 🧰 Debug & Output Control
>
> ```bash
> debug
> trace
> verbose
> ```
>
> ```bash
> hash
> bell
> ```
>
> 💡 Monitor transfers & troubleshoot issues

---

# 🔁 Automation & Behavior

---

> [!info] ⚡ Automation Controls
>
> ```bash
> prompt      # Toggle confirmation prompts
> glob        # Wildcard support
> ```
>
> 💡 Useful for bulk transfers

---

> [!tip] 🧠 Run Local Commands
>
> ```bash
> !ls
> !whoami
> ```
>
> 💡 Execute commands on YOUR machine (not remote)

---

# 📡 Advanced Commands

---

> [!warning] ⚠️ Raw Server Interaction
>
> ```bash
> literal <command>
> quote <command>
> ```
>
> 💡 Send raw FTP commands directly to server

---

> [!info] 🆘 Help Commands
>
> ```bash
> help
> ?
> remotehelp
> ```
>
> 💡 Get command usage info

---

# 💻 FTP Command-Line Options

---

> [!success] 🚀 Launch Options
>
> ```bash
> ftp -v      # Disable verbose output
> ftp -n      # Disable auto login
> ftp -i      # Disable prompts
> ftp -d      # Enable debugging
> ```
>
> ```bash
> ftp -g      # Disable wildcard expansion
> ftp -s:file # Run commands from file
> ```
>
> ```bash
> ftp -w:8192 # Set buffer size
> ftp <ip>    # Connect directly
> ```
>
> 💡 Useful for scripting & automation

---

# ⚡ Pentesting Use Cases

---

> [!danger] 💥 Common Attacks
>
> * Anonymous login:
>
> ```bash
> user anonymous
> ```
>
> * Upload shell:
>
> ```bash
> put shell.php
> ```
>
> * Download sensitive files:
>
> ```bash
> get config.php
> ```
>
> 💡 FTP misconfigs = easy entry point

---

# 🧠 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> CONNECT → ENUMERATE → DOWNLOAD → UPLOAD → EXPLOIT
> ```

---

> [!tip] 🚀 Pro Tips
>
> * Always try **anonymous login**
> * Use **binary mode** for payloads
> * Check writable directories
> * Combine with web access (FTP + HTTP = 🔥)
> * Automate with `-s:file` scripts

---

