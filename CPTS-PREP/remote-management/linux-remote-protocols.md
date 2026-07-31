# 🛰️ RSH Command Execution & File Transfer

## 📝 Summary
RSH (Remote Shell) is an older and less secure protocol for executing commands remotely and transferring files between machines.

### 🚦 Commands
- `rsh target "command"`: Execute a command on the remote host.
- `rcp localfile target:remotefile`: Copy a file to the remote machine.
- `rcp target:remotefile localfile`: Copy a file from the remote machine.

### 📁 Example
```bash
# Execute whoami and uname -a remotely
rsh target "whoami; uname -a"

# Transfer files
rcp /home/user/config.txt target:/tmp/
rcp target:/etc/passwd /root/

# Remote login session
rlogin target
```

---

# 🔐 Security Assessment

## SSH Security Assessment 🛡️

### 🕵️ Port Scan and Version Detection
```bash
nmap -p22 -sV target
```

### 📄 Configuration Analysis
```bash
ssh -T -o StrictHostKeyChecking=no user@target 2>&1 | grep -E "debug|config"
```

### 🔑 Authentication Method Testing
- Test default accounts and passwords.
- Use `hydra` for brute force attacks.

### 🛡️ Vulnerability Scanning
```bash
nmap -p22 --script ssh-vuln* target
```

## Rsync Security Assessment 📂

### 🕵️ Port Scan
```bash
nmap -p873 target
```

### 🗃 Module Enumeration
```bash
rsync target::
```

### 📄 File Download Testing
```bash
rsync target::module/file.txt .
```

### 💾 Write Permission Testing
```bash
echo "test" | rsync -v target::module/testfile.txt
```

## R-Service Security Assessment 🌐

### 🕵️ Port Scan and Service Availability
```bash
nmap -p513,514 --script rlogin-info,target-name,rsh-auth target
```

### 💥 Authentication Bypass Attempts
```bash
rsh target "echo 'admin admin' >> /etc/hosts.equiv"
rsh target "id"
```

### 🌐 Command Execution Testing
```bash
rsh target "whoami; id"
```

---

# 🔍 Enumeration Checklist

## SSH Enumeration 🛡️

- [ ] Port scan for SSH (22/tcp)
- [ ] Version detection and banner grabbing
- [ ] Algorithm enumeration
- [ ] User enumeration
- [ ] Authentication method testing
- [ ] Configuration analysis
- [ ] Vulnerability scanning

### Examples
```bash
nmap -p22 -sV --script ssh-hostkey,ssh2-enum-algos target
hydra -l user -P passwords.txt ssh://target
```

## Rsync Enumeration 📂

- [ ] Port scan for rsync (873/tcp)
- [ ] Module enumeration
- [ ] Anonymous access testing
- [ ] Directory listing
- [ ] File download testing
- [ ] Write permission testing
- [ ] Sensitive file identification

### Examples
```bash
rsync target::
rsync -av target::module/ .
```

## R-Service Enumeration 🌐

- [ ] Port scan for R-Services (513, 7)
- [ ] Service availability testing
- [ ] Authentication bypass attempts
- [ ] Command execution testing
- [ ] File transfer testing

### Examples
```bash
rsh target "whoami"
rcp target:file .
```

---

# 💥 Common Vulnerabilities

## SSH Vulnerabilities 🛡️
- **CVE-2018-15473**: User enumeration in OpenSSH
- **CVE-2016-10009**: Privilege escalation via environment variables
- **CVE-2008-5161**: Client-side buffer overflow

## Rsync Vulnerabilities 📂
- **CVE-2014-9512**: Path traversal in rsync
- **CVE-2011-1097**: Security bypass via options

## R-Service Vulnerabilities 🌐
- **CVE-1999-0651**: Buffer overflow in rshd
- **CVE-1999-0025**: Authentication bypass in rlogin

---

# 💡 Tools and Techniques

### SSH Tools 🛡️
```bash
ssh                  # SSH client
scp                  # Secure copy
sftp                 # File transfer protocol over SSH
ssh-keygen           # Key generation
ssh-copy-id         # Deploy keys to remote machine
```

### Rsync Tools 📂
```bash
rsync                # Rsync client
nmap -p873 target    # Service detection
```

### Custom Rsync Enumerator Script
```bash
#!/bin/bash
# Rsync enumerator script
target=$1
modules=$(rsync $target:: 2>/dev/null | awk '{print $1}')
for module in $modules; do
    echo "=== Module: $module ==="
    rsync $target::$module/ 2>/dev/null
done
```

### R-Service Tools 🌐
```bash
rsh                  # Remote shell
rcp                  # Remote copy
rlogin               # Remote login
```

---

# 🔒 Defensive Measures

## SSH Hardening 🛡️
```bash
# /etc/ssh/sshd_config
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
AllowUsers normaluser
DenyUsers root

# Restart SSH service
systemctl restart sshd
```

## Rsync Security 📂
```bash
# /etc/rsyncd.conf
uid = nobody
gid = nobody
use chroot = yes
max connections = 10
timeout = 300
refuse options = delete

[secure_backup]
    path = /backup
    read only = true
    hosts allow = 192.168.1.0/24
    auth users = backup_user
    secrets file = /etc/rsyncd.secrets
```

## R-Service Mitigation 🌐
```bash
# Disable services
systemctl stop rsh
systemctl disable rsh

# Remove packages
apt remove rsh-client rsh-server
apt remove rlogin
```

---

# 📚 Best Practices

### SSH Best Practices 🛡️
1. **Use key-based authentication only**
2. **Disable root login via SSH**
3. **Change default port number**
4. **Configure firewall rules to restrict access**
5. **Enable fail2ban for brute force protection**

### Rsync Best Practices 📂
1. **Encrypt data in transit using rsyncrypto or ssh tunneling**
2. **Restrict network access to trusted IPs only**
3. **Use read-only shares when possible**
4. **Monitor log files for suspicious activity**
5. **Regularly update and patch software**

### R-Service Recommendations 🌐
1. **Do not use R-Services in production environments**
2. **Replace with secure alternatives like SSH or SFTP**
3. **Disable all R-Service related packages**
4. **Implement security policies to prevent their usage**
5. **Conduct regular audits and penetration testing**

---

# 📝 References
- [[CrackMapExec]]
- [[Nmap]]
- [[Hydra]]
- [[Patator]]

---