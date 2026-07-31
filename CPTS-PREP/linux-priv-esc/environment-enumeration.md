# 🛰️ Introduction

[[Linux Privilege Escalation]]

## 🔍 Purpose of this Guide
[!INFO] This guide provides a comprehensive methodology for Linux environment enumeration, a critical step in privilege escalation efforts. The objective is to systematically uncover system details and vulnerabilities that can lead to elevated privileges.

### Key Steps:
1. **Initial System Information**: Gather basic facts about the target.
2. **User and Permission Analysis**: Identify user accounts, permissions, and group memberships.
3. **File System Exploration**: Search for sensitive files and directories.
4. **Temporary Files Investigation**: Analyze temporary directories for potential credentials or data.
5. **Comprehensive Enumeration Checklist**: A structured approach to ensure thoroughness.

---

## 📐 Initial System Information

### Basic Facts
[!CHECK] Gather essential system details:

```bash
whoami
id
hostname
```

**OS Information:**
[!CHECK]
- `/etc/os-release`
- `cat /proc/version`
- `lsb_release -a` (if available)

**Kernel Details:**
[!CHECK]
```bash
uname -a
```

### Network Configuration
[!INFO] Identify network interfaces and configurations:

```bash
ifconfig
ip a
route -n
netstat -rn
```

---

## 🚀 User and Permission Analysis

### User Accounts
[!CHECK] List all user accounts:

```bash
cat /etc/passwd | cut -d: -f1
```

**Find Recent Users:**
[!INFO]
```bash
grep '/home/' /etc/passwd | awk -F':' '$3 >= 1000 {print $1}' 
```

### Group Memberships
[!CHECK] Identify users in critical groups:

```bash
getent group sudo
getent group admin
```

**Current User Groups:**
[!INFO]
```bash
groups
id
```

---

## 📂 File System Exploration

### Home Directory Investigation
[!SUCCESS] Examine user home directories for sensitive files and configurations.

#### List Home Directories:
[!CHECK]
```bash
ls -la /home
```

**Search Files:**
[!INFO]
- `.bashrc`, `.bash_profile`
- SSH keys (`~/.ssh/`)
- History files (`~/.bash_history`)

**Interesting Configurations:**
[!SUCCESS]
```bash
find /home -name "*history*" -type f 2>/dev/null
```

### Hidden Files and Directories
[!CHECK] Look for hidden files and directories that may contain sensitive information:

#### All Hidden Files:
[!INFO]
```bash
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | head -20
```

**User-Specific Hidden Files:**
[!CHECK]
```bash
find /home -type f -name ".*" -exec ls -l {} \; 2>/dev/null
```

---

## 📁 Temporary Files and Directories

### Standard Temporary Directories
[!SUCCESS] Examine common temporary directories for sensitive files:

#### List `/tmp`:
```bash
ls -la /tmp
```
#### Recently Created Files:
[!INFO]
```bash
find /tmp -type f -mtime -1 2>/dev/null
```

**Search with Keywords:**
[!SUCCESS]
```bash
grep -r "password" /tmp/
```

---

## 📋 Systematic Enumeration Checklist

### Phase 1: Basic Orientation
- [ ] Run `whoami`, `id`, `hostname`
- [ ] Check `sudo -l` for immediate privilege escalation

### Phase 2: System Information
- [ ] OS version and distribution (`/etc/os-release`)
- [ ] Kernel version (`uname -a`)

### Phase 3: Environment Analysis
- [ ] PATH variable enumeration (`echo $PATH`)
- [ ] Network configuration (`ifconfig`, `route`)

### Phase 4: User and Permission Analysis
- [ ] User enumeration (`/etc/passwd`)
- [ ] Group analysis (`/etc/group`)
- [ ] Home directory investigation

### Phase 5: File System Analysis
- [ ] Hidden files and directories
- [ ] Temporary file analysis

---

## 💡 Key Findings to Look For

### High-Impact Discoveries

**Immediate Privilege Escalation:**
- `sudo -l` showing passwordless commands
- SUID binaries with known exploits
- Writable files in PATH

**Credential Discovery:**
- Passwords in configuration files
- SSH private keys
[[Linux Enumeration]]

---

## ⚠️ Common Pitfalls and Considerations

### Stealth Considerations
[!WARNING] Be mindful of system logs:
- `sudo -l` may generate audit trails.
- Avoid running destructive commands.

**System Stability:**
- Kernel exploits can crash systems.
- Test in controlled environments first.

**Thoroughness vs. Speed:**
- Balance thorough enumeration with time constraints.
- Focus on high-impact areas initially.

---

## 🛠️ Automation and Tools

### Manual vs. Automated Enumeration
[!INFO] 
**Manual:**
- Learning system internals.
- Customized searches based on findings.

**Automated:**
- **LinPEAS**: Comprehensive enumeration script.
- **LinEnum**: Classic Linux enumeration tool.

**Integration Strategy:**
1. Perform initial manual enumeration.
2. Run automated tools for comprehensive coverage.
3. Cross-reference findings.
4. Focus manual investigation on promising vectors.

---

## 📚 Next Steps

After environment enumeration, proceed to:

1. **Permissions-based Privilege Escalation**: File permissions, SUID/SGID
2. **Service-based Privilege Escalation**: Running services and processes
3. **Configuration-based Attacks**: Misconfigurations and weak settings
4. **Kernel Exploitation**: Operating system vulnerabilities

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations.
2. Use emojis in ALL H1 and H2 headers.
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context.
4. Separate major logical sections with horizontal rules (`---`).
5. Use clean Markdown tables where appropriate.
6. ALWAYS use language tags for code blocks.
7. Convert tool names and techniques into Obsidian wiki-links.

---