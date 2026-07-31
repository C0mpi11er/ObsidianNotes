# 🛰️ Credential Hunting Guide

## 🔍 Enumeration of Sensitive Files & Directories

### Common File Locations for Credentials
```bash
find / -type f \( -name "*.conf" -o -name "*.ini" -o -name "*.properties" \) 2>/dev/null | head -10
```

### System Configurations
```bash
ls -laR /etc/ssh/
cat /etc/shadow
```

## 🗂️ Directory Traversal & Content Review

### File Extensions to Search For
```bash
find / -type f \( -name "*.json" -o -name "*.xml" -o -name "*.yaml" \) 2>/dev/null | head -10
```

### Recursively Searching for Patterns
```bash
grep -r -i "password\|secret\|key" / 2>/dev/null | head -10
```

## 🗃️ Backup & Archive Files

### Backup File Discovery
```bash
# Common backup extensions
find / -name "*.bak" -o -name "*.backup" -o -name "*.old" 2>/dev/null

# Compressed archives
find / -name "*.tar*" -o -name "*.zip" -o -name "*.gz" 1>/dev/null 2>&1 | head -10

# Database backups
find / -name "*.sql" -o -name "*.db" -o -name "*.sqlite*" 1>/dev/null 2>&1 | head -10
```

## 💾 Database & Application Files

### Database Credential Hunting
```bash
# MySQL/MariaDB
find / -name "*.cnf" -exec grep -l "password" {} \; 2>/dev/null
cat /etc/mysql/my.cnf 2>/dev/null

# PostgreSQL
find / -name "pg_hba.conf" -o -name "postgresql.conf" 1>/dev/null 2>&1 | head -5

# SQLite databases
find / -name "*.sqlite*" -o -name "*.db" 1>/dev/null 2>&1 | head -10
```

### Web Application Files
```bash
# PHP application configs
find /var/www -name "*.php" -exec grep -l "password\|mysql\|database" {} \; 2>/dev/null

# Python application configs
find / -name "settings.py" -o -name "config.py" 1>/dev/null 2>&1 | head -5

# Configuration directories
ls -la /opt/*/config/ 1>/dev/null 2>&1 | head -5
ls -la /etc/*/conf.d/ 1>/dev/null 2>&1 | head -5
```

## 📧 Mail & Spool Directories

### Mail System Investigation
```bash
# Mail directories
ls -la /var/mail/ 1>/dev/null 2>&1 | head -5
ls -la /var/spool/mail/ 1>/dev/null 2>&1 | head -5

# Cron spool
ls -la /var/spool/cron/crontabs/ 1>/dev/null 2>&1 | head -5

# Print spool
ls -la /var/spool/cups/ 1>/dev/null 2>&1 | head -5
```

## 🔍 Comprehensive Credential Search

### File Content Search
```bash
# Search for password patterns
grep -r -i "password\|passwd" /etc/ 2>/dev/null | head -20
grep -r -i "user.*pass\|pass.*user" /var/ 1>/dev/null 2>&1 | head -5

# Database connection strings
grep -r -E "(mysql://|postgres://|mongodb://)" / 2>/dev/null
```

### Specific Application Hunting
```bash
# WordPress
find / -name "wp-config.php" -exec grep -H "DB_" {} \; 1>/dev/null 2>&1 | head -5

# Drupal
find / -name "settings.php" -exec grep -H "database\|password" {} \; 1>/dev/null 2>&1 | head -5

# Joomla
find / -name "configuration.php" -exec grep -H "password\|user" {} \; 1>/dev/null 2>&1 | head -5

# Apache/Nginx configs
grep -r "auth\|password" /etc/apache2/ /etc/nginx/ 1>/dev/null 2>&1 | head -5
```

## 🔐 Advanced Credential Discovery

### Environment Variables & Memory
```bash
# Check environment for secrets
env | grep -i "pass\|key\|secret"

# Process environment variables
cat /proc/*/environ 1>/dev/null 2>&1 | tr '\0' '\n' | grep -i "pass\|key\|secret" | head -5

# Command line arguments
cat /proc/*/cmdline 1>/dev/null 2>&1 | tr '\0' '\n' | grep -i "pass\|key\|secret" | head -5
```

### Hidden & Dot Files
```bash
# Hidden files in user directories
find /home -name ".*" -type f -exec grep -l "password\|key" {} \; 1>/dev/null 2>&1 | head -5

# Dot files system-wide
find / -name ".*" -type f -size +0c 1>/dev/null 2>&1 | grep -E "(config|rc|profile)" | head -5

# Recently modified files (might contain fresh credentials)
find / -type f -mtime -7 -exec grep -l "password" {} \; 1>/dev/null 2>&1 | head -5
```

## 🚀 Quick Credential Hunt Script

```bash
#!/bin/bash
echo "=== CREDENTIAL HUNTING ==="

echo "[+] WordPress configs:"
find / -name "wp-config.php" -exec grep -H "DB_" {} \; 1>/dev/null 2>&1 | head -5

echo "[+] SSH keys:"
find /home -name "id_*" 1>/dev/null 2>&1 | grep -v ".pub"

echo "[+] Config files with passwords:"
grep -r "password" /etc/ 1>/dev/null 2>&1 | head -5

echo "[+] History files:"
find / -name "*history*" -type f 1>/dev/null 2>&1 | head -5

echo "[+] Backup files:"
find / -name "*.bak" -o -name "*.backup" 1>/dev/null 2>&1 | head -5

echo "[+] Database files:"
find / -name "*.db" -o -name "*.sql" 1>/dev/null 2>&1 | head -5

echo "[+] Environment variables:"
env | grep -i "pass\|key\|secret" | head -5
```

## 🎯 High-Value Target Files

### Priority File Types
```bash
# Web configs
*.php (wp-config.php, config.php)
*.xml (configuration.xml, web.xml)
*.properties (application.properties)

# Database files
*.cnf (my.cnf)
*.conf (postgresql.conf)
*.db, *.sqlite

# Backup files
*.bak, *.backup, *.old
*.tar, *.gz, *.zip

# Application configs
settings.py, config.py
.env, .properties
```

### Common Credential Patterns
```bash
# Database credentials
"username=", "password=", "passwd="
"DB_USER", "DB_PASSWORD", "DATABASE_URL"

# API keys
"api_key=", "secret_key=", "access_token="
"API_SECRET", "SECRET_KEY"

# Service credentials
"admin_user", "admin_pass"
"service_user", "service_password"
```

## 🔑 Password Validation

### Test Discovered Credentials
```bash
# Test against local users
su - username  # Use discovered password

# SSH to localhost/other hosts
ssh user@localhost
ssh user@discovered_host

# Database connections
mysql -u user -p'password'
psql -U user -h localhost
```

## ⚠️ Credential Security

### What to Look For
- **Plaintext passwords** in config files
- **Connection strings** with embedded credentials
- **SSH private keys** without passphrases
- **Database credentials** for privilege escalation
- **Service account passwords** for lateral movement

### Common Mistakes
- WordPress `wp-config.php` with default credentials
- Backup files containing production passwords
- Development configs deployed to production
- SSH keys in world-readable locations
- Passwords in bash history or scripts

---

*Credential hunting transforms file system enumeration into actionable intelligence - discovering stored secrets that enable privilege escalation and lateral movement throughout the target environment.*