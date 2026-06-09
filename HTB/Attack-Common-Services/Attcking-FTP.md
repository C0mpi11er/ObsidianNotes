# 🛰️ FTP (File Transfer Protocol) Attack Cheat Sheet

> [!info] 🧠 What is FTP?
> 
> Bash
> 
> ```
> Standard network protocol used to transfer files between computers over a network
> ```
> 
> 💡 Allows administrators to:
> 
> - Perform file/directory operations (changing directories, listing files, renaming, deleting)
>     
> - Manage a file storage space structured in a hierarchical directory system
>     
> - Trigger raw file uploads and downloads between servers and clients
>     
> 
> ✔ Common in:
> 
> - Corporate environments for software or website development processes
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Client (User Session) ↔ Server (File Management System)
> ```
> 
> 💡 Key Ports:
> 
> - **TCP 21** → FTP Control Channel (Listens for incoming connections/commands by default)
>     
> 
> ✔ Think of it as **a remote file filing cabinet controlled directly through network commands**

# 🌳 Structure: Navigation & Data Transfer

> [!info] 📚 Directory Navigation Commands
> 
> Bash
> 
> ```
> ls   # List files and folders in the current remote working directory
> cd   # Change the remote working directory
> help # View all available commands within an active FTP client session
> ```
> 
> 💡 Function:
> 
> - Interacts with the target's operating system file layout similarly to a standard Linux terminal.
>     

> [!tip] 📥 Data Movement: Downloads & Uploads
> 
> Bash
> 
> ```
> get  <file>  # Download a single file from the remote server
> mget <files> # Download multiple files from the remote server
> put  <file>  # Upload a single local file to the remote server
> mput <files> # Upload multiple local files to the remote server
> ```
> 
> 💡 Logic:
> 
> - Attacks center on gaining unauthorized access to these actions to pull sensitive data out or push malicious payloads in.
>     

# 🔐 Access Methods

|**Authentication Type**|**Credentials Required**|**Risk Level**|**Notes**|
|---|---|---|---|
|**Anonymous Login**|Username: `anonymous` / No Password|**CRITICAL**|Dangerous misconfiguration; drops users directly into shared folders|
|**Authenticated Access**|Unique User / Unique Password|**MEDIUM**|Standard practice; susceptible to brute-forcing if weak passwords are used|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration Tools
> 
> Bash
> 
> ```
> # 1. Automated Script & Version Scan
> sudo nmap -sC -sV -p 21 <target>
> 
> # 2. Interacting manually with the service
> ftp <target>
> nc -nv <target> 21
> ```
> 
> 💡 Flag breakdown:
> 
> - `-sC` includes the `ftp-anon` script to verify if unauthenticated logins are enabled.
>     
> - `-sV` grabs the FTP banner to extract software versions (e.g., `vsFTPd 2.3.4`).
>     

> [!info] 🔍 Information Revealed
> 
> - **FTP Banner Info:** Discloses specific backend software names and running versions.
>     
> - **Directory Maps:** Lists exposed root folder structures, hidden files (e.g., `.banner`), and accessible directories (e.g., `incoming/`).
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Exposed Anonymous Authentication
> 
> Bash
> 
> ```
> Name: anonymous
> Password: <Press Enter / Blank>
> ```
> 
> 💡 Impact:
> 
> - **Read Permissions:** Allows attackers to search directories and download sensitive or critical corporate data.
>     
> - **Write Permissions:** Allows attackers to upload malicious scripts (like a PHP backdoor). Combined with other exploits like Web Application Path Traversal, this can lead to Remote Code Execution (RCE).
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Brute-Forcing Credentials
> 
> Bash
> 
> ```
> medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h <target> -M ftp
> ```
> 
> 💡 Impact:
> 
> - Uses rapid automated guessing lists to locate valid active accounts (e.g., finding `User: fiona Password: family`).
>     
> - _Note:_ Modern applications often block traditional brute forcing; **Password Spraying** is widely used as a more effective alternative.
>     

> [!danger] 🎯 FTP Bounce Attack
> 
> Bash
> 
> ```
> nmap -Pn -v -n -p80 -b anonymous:password@<Proxy_FTP_IP> <Internal_Target_IP>
> ```
> 
> 💡 Risk:
> 
> - Uses the FTP `PORT` command to trick an internet-facing FTP server into relaying outbound traffic to hidden internal systems (`Internal_DMZ`).
>     
> - Allows attackers to map open ports on private internal systems by using the vulnerable FTP host as a network middleman.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Target TCP Port 21 via Nmap to check for active FTP configurations.
> 2. Analyze Nmap script output to verify if "Anonymous FTP login allowed" triggers.
> 3. Connect via the native 'ftp' client to map out available folders using 'ls'.
> 4. Check folder rules: use 'get' to exfiltrate files or 'put' to test write capabilities.
> 5. If access is closed, launch Medusa to brute-force credential lists for valid user accounts.
> ```
> 
> 💡 Always check for application interdependencies where an FTP folder serves as a web root.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Directory Tree → The Floor Plan
> Anonymous Login → An unlocked service entrance
> Medusa → An automated key-turner testing combinations
> Bounce Attack → Using a mirror to bounce a laser around a corner
> ```
> 
> 💡 If the "Service Entrance" (Anonymous Access) is left wide open, you don't need a lockpick to walk away with the assets.

