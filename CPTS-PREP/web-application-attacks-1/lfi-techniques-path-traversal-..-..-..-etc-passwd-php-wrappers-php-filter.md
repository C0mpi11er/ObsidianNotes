```markdown
# 🛠️ LFI Techniques - Path Traversal (../../../etc/passwd), PHP Wrappers (php://filter)

---

## 🔍 Overview

> [!ABSTRACT] 
> This document outlines techniques for exploiting Local File Inclusion (LFI) vulnerabilities using path traversal and PHP wrappers to access sensitive files like `../../../../etc/passwd`. It covers steps to bypass input validation, read system files, and use various PHP filters.

### Path Traversal
Path traversal is a technique where an attacker manipulates file paths in URL requests or form submissions to access restricted directories. This can include accessing important system files such as `/etc/passwd`.

> [!NOTE] 
> Ensure the application does not properly sanitize input for file inclusion requests before attempting these techniques.

### PHP Wrappers
PHP wrappers like `php://filter` allow data filtering in PHP scripts, which can be leveraged to manipulate and read files even if direct access is blocked by standard filesystem permissions or URL rewriting rules.

---

## 📝 Path Traversal Example

To bypass basic input validation that only checks for slashes (`/`) to prevent directory traversal attacks, an attacker might use double dots `..`:

```text
http://example.com/vulnerable-file.php?page=../../../../etc/passwd
```

This request attempts to read the `/etc/passwd` file by moving up three levels in the directory structure.

---

## 📝 PHP Wrappers Example

PHP's `php://filter` wrapper can be used with read and convert filters. For instance, the `convert.base64-decode` filter can decode base64 encoded content:

```text
http://example.com/vulnerable-file.php?page=php://filter/convert.base64-decode/resource=../../../../etc/passwd
```

This technique bypasses typical input validation by encoding the path and using PHP's filtering mechanism to read it.

---

## 📝 Steps for Exploitation

### Step 1: Identify Vulnerable Input Fields

> [!CHECK] 
> Use web proxy tools or browser developer tools to inspect form submissions and URL parameters that could be used for file inclusion attacks.

### Step 2: Craft the Path Traversal Request
Construct a request that includes directory traversal sequences (`../` or `./`) to target files outside of the expected scope:

```text
http://example.com/vulnerable-file.php?page=../../../../etc/passwd
```

### Step 3: Test PHP Wrappers

Attempt to use `php://filter` with different filters and resources. For example, reading a file directly using a filter resource:

```text
http://example.com/vulnerable-file.php?page=php://filter/convert.base64-decode/resource=../../../../etc/passwd
```

### Step 4: Analyze Response

Check the server response for any files accessed or errors that indicate whether the input validation was bypassed.

---

## 📌 Important Files to Access

Target critical system and application configuration files:

```text
/etc/passwd
/etc/shadow
/config.php
/db.conf
/.htaccess
```

These files often contain sensitive information like usernames, passwords, database credentials, and server configurations.

---

## ⚠️ Security Considerations

> [!WARNING] 
> Ensure that all testing is done in a controlled environment (like a lab) to avoid unauthorized access or data breaches. Misuse of these techniques can lead to legal consequences and damage systems.

### Mitigation Strategies
Implement proper input validation, use file system permissions wisely, and employ security headers such as Content Security Policy (CSP).

---

## 📊 Common Findings

| Technique | Impact |
|---|---|
| Path Traversal | Access Restricted Files |
| PHP Wrappers | Bypass Input Validation |

> [!SUCCESS] 
> Successful exploitation indicates a lack of proper input validation and file system security measures in the application.
```