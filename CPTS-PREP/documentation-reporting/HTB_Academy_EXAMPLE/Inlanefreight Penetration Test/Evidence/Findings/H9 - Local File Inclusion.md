# 📂 Local File Inclusion

> [!ABSTRACT] Overview of Local File Inclusion (LFI)
>
> Local File Inclusion is a web security vulnerability that allows an attacker to include and read local files on the server, often leading to sensitive information disclosure.

---

## ⚙️ What is LFI?

Local File Inclusion (LFI) occurs when a web application takes user input and uses it as part of a file inclusion call without proper sanitization or validation. This can lead to the execution of arbitrary code from files on the server, such as configuration files containing database credentials.

> [!NOTE] Common Attack Vectors
>
> - Including PHP files (e.g., `file.php?include=some_file`)
> - Using file-based authentication mechanisms

---

## 🔍 Initial Enumeration

### Identify Vulnerable Input Fields

```text
Check for parameters that might be used in file inclusion:
- "page" parameter: http://example.com/?page=../../../../etc/passwd
```

### Verify LFI Vulnerability

Attempt to read a specific file:

```bash
http://vulnerable-site/page.php?file=/etc/passwd
```

---

## ⚠️ Dangers of LFI Exploitation

- **Sensitive Information Disclosure**: Reading configuration files, password hashes.
- **Server Code Execution**: Including PHP scripts that execute system commands.

### Preventing LFI Vulnerabilities

1. Validate and sanitize all user input.
2. Use absolute paths for file inclusion where possible.
3. Implement a whitelist of allowed filenames or directories.

---

## 📝 Techniques to Exploit LFI

### Using Common File Paths

Attempt including sensitive files:

```text
http://example.com/page.php?file=../../../../etc/passwd
```

### Bypassing Restrictions with Alternative Encoding

Use URL encoding or other techniques to bypass filters:

```text
http://example.com/page.php?file=%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
```

---

## 📂 Common LFI Targets

| File | Description |
|---|---|
| `/etc/passwd` | User accounts and permissions |
| `/var/log/auth.log` | Authentication logs |
| `config.php` | Database credentials and site configuration |

Also check for keywords like `config`, `database`, `db`, or `settings`.

---

## 🔑 Exploiting LFI to Gain Privileges

### Including PHP Shell

Place a malicious PHP file on the server if possible:

```text
http://example.com/page.php?file=/path/to/malicious_shell.php
```

Run commands through the included shell.

### Uploading Files via Upload Forms

Exploit file upload forms to place scripts:

```text
http://example.com/upload.php?file=../../../../etc/passwd
```

---

## ⚠️ Common Pitfalls in LFI Exploitation

- **Incorrect Path Traversal**: Incorrectly calculating directory traversal sequences.
- **File Existence Check Failures**: Failing to verify if a file exists before including it.

### Documenting LFI Attempts and Results

Use a structured format for documenting each attempt:

```text
Date: [YYYY-MM-DD]
Target: example.com
Method: HTTP GET with Parameter Injection
Payload: /page.php?file=../../../../etc/passwd
Result: Success (Read passwd file)
```

---

## 🧠 Exam Mental Model for LFI

Whenever you encounter a parameter that looks like it might be used in file inclusion:

```text
Suspect Input → Test Traversal Paths → Attempt to Read Config Files → Upload Shell Script → Gain Privileges
```

LFI is critical because it can provide direct access to sensitive server files and potentially lead to command execution.

---

> [!WARNING]
> Be cautious with LFI exploitation as it may have significant security implications. Ensure you are authorized before attempting any such actions.