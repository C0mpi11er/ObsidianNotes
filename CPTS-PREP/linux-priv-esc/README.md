# 🛰️ Linux Privilege Escalation Module

This module provides a comprehensive guide to gaining elevated privileges on Linux systems through various attack vectors, techniques, and tools.

## 📚 Table of Contents

- [Enum](enum.md)
  - **Manual Enumeration**
    - System Commands: `uname`, `id`, `whoami`
    - File System Tools: `find`, `ls`, `cat`, `grep`
    - Network Utilities: `ifconfig`, `netstat`, `route`, `arp`
    - Process Management: `ps`, `top`, `systemctl`, `service`
  - **Automated Enumeration**
    - [[LinPEAS]]
    - [[LinEnum]]
    - [[linux-smart-enumeration]]
    - [[PEASS-ng]]

- [Exploitation Techniques](exploitation-techniques.md)
  - **Manual Exploitation Methods** 
    - SUID/SGID Binaries
    - Writable Files in PATH
    - Misconfigured Services
    - Kernel Exploits
  - **Automated Exploitation Tools**
    - [[Metasploit]]
    - [[GTFOBins]]
    - [[ExploitDB]]

- [Persistence](persistence.md)
  - Crontab Entries
  - Cron Job Abuse
  - .bashrc Hooks

### 🛠️ Tools and Techniques

#### Manual Enumeration
- **System Commands**:
  ```bash
  uname -a 
  id
  whoami
  ```
- **File System**:
  ```bash
  find / -perm -4000 -type f 2>/dev/null
  ls -l /etc/cron*
  cat /etc/passwd
  grep -r "root" /etc/
  ```
- **Network**: 
  ```bash
  ifconfig
  netstat -antup
  route -n
  arp -a
  ```
- **Process**:
  ```bash
  ps aux | grep root
  top
  systemctl list-units --type service
  service --status-all
  ```

#### Automated Tools
- [[LinPEAS]]
- [[LinEnum]]
- [[linux-smart-enumeration]]
- [[PEASS-ng]]

### Exploitation Frameworks

- **Metasploit** - Post-exploitation modules
- **GTFOBins** - Living off the land binaries
- **ExploitDB** - Public exploit database
- **Custom Scripts** - Tailored enumeration and exploitation
- **Kernel Exploits** - CVE-specific exploits (⚠️ **High risk - use with caution**)

## 🎯 Learning Objectives

By completing this module, you will be able to:

1. **Perform systematic environment enumeration** on Linux systems.
2. **Identify privilege escalation vectors** through various attack surfaces.
3. **Exploit common misconfigurations** to gain elevated privileges.
4. **Utilize automated tools effectively** while understanding manual techniques.
5. **Maintain persistence** after successful privilege escalation.
6. **Document findings professionally** for penetration test reports.

## 🛡️ Defensive Considerations

### Common Misconfigurations
- Excessive sudo permissions.
- Writable files in PATH.
- SUID binaries on sensitive executables.
- Unpatched kernel vulnerabilities.
- Service running as root unnecessarily.

### Hardening Recommendations
- Regular system updates and patching (especially kernel updates).
- Principle of least privilege enforcement.
- File permission auditing.
- Service account isolation.
- Monitoring and logging implementation.
- **Special attention to kernel exploits** - Advanced techniques require careful testing.

## 📖 Prerequisites Knowledge

### Linux Fundamentals
- Command line navigation.
- File system structure.
- User and group concepts.
- Process management.
- Network configuration basics.

### Security Concepts
- Unix permissions model.
- SUID/SGID concepts.
- Service architecture.
- Kernel space vs user space.
- Authentication and authorization.

## 🏆 Success Metrics

### Skill Development Goals
- **Manual Enumeration Proficiency**: Perform thorough recon without tools.
- **Attack Vector Recognition**: Identify privilege escalation opportunities.
- **Tool Integration**: Combine manual and automated techniques effectively.
- **Stealth Operations**: Conduct enumeration without detection.
- **Documentation Skills**: Create comprehensive findings reports.

### Practical Milestones
- Successfully escalate privileges on various Linux distributions.
- Identify and exploit SUID/SGID vulnerabilities.
- Abuse service misconfigurations for privilege escalation.
- Utilize kernel exploits safely and effectively (with caution for advanced techniques).
- Establish persistent elevated access.
- Master 24 different privilege escalation techniques including advanced kernel exploits and defensive hardening.

---

*This Linux Privilege Escalation module provides comprehensive coverage of techniques, tools, and methodologies for gaining elevated privileges on Linux systems, essential for penetration testers and security professionals.* 

---