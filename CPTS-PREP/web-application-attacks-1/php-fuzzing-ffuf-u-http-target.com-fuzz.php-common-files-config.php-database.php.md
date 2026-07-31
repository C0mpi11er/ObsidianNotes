# 🛠️ PHP Fuzzing with FFUF

> [!ABSTRACT] Overview of PHP Fuzzing using FFUF to discover common files like `config.php` and `database.php`.

---

## 🎯 Objective: Discover Common Files Using FFUF

### Step-by-Step Guide

1. **Target URL Setup**
   
   Use the following command format with FFUF to fuzz for `.php` files on a target:

   ```bash
   ffuf -u http://target.com/FUZZ.php -w /path/to/wordlist.txt
   ```

2. **Fuzzing Specific File Names**

   Fuzz specific file names like `config.php`, `database.php`, etc., to identify potential configuration files or database access points:

   ```bash
   ffuf -u http://target.com/FUZZ.php -w /path/to/wordlist.txt -ac -mc 200,301,302,403
   ```

   > [!NOTE] 
   > Use `-ac` to follow redirects and `-mc` to specify which HTTP status codes to consider as successful matches.

---

## 📝 Common Files Identified

### 1. `config.php`
This file is often used for storing configuration settings in PHP applications, including database credentials and other sensitive information.
   
### 2. `database.php`
Similar to `config.php`, this file can contain direct database connection details or functions that interact with the application's database.

---

## 📝 Example Command Usage

```bash
ffuf -u http://target.com/FUZZ.php -w /home/user/wordlists/php_files.txt
```

> [!SUCCESS] Successful discovery of files like `config.php` and `database.php`.
> 
> Example output:
> ```text
> Target: http://target.com/FUZZ.php
> Payload Type: FUZZ (Wordlist)
> Wordlist File: /home/user/wordlists/php_files.txt
>
> [Status] (Reason)      [Word]
> [200] OK                config
> [301] Moved Permanently database
>
> Total requests: 2679
> ```

---

## 📌 Potential Vulnerabilities

- **Sensitive Information Exposure**: The discovery of these files may lead to the exposure of sensitive information such as database credentials.
  
- **Misconfiguration**: Incorrect permissions or server configurations might allow unauthorized access.

### Recommendations

- **Security Audits**: Regularly audit PHP applications for misconfigurations and ensure proper file permission settings are enforced.
- **Input Validation**: Enforce strict input validation in web applications to prevent unexpected file access.

---

## 📚 Additional Resources
For more detailed information on FFUF usage, refer to the official documentation or online tutorials:
> [!QUOTE] 
> Official Documentation: https://github.com/ffuf/ffuf/wiki

> [!QUOTE]
> Tutorial Guide: https://pentest-tools.com/blog/php-fuzzing-ffuf-examples-tutorial/

---

## 🧠 Exam Mental Model

```text
PHP Fuzzing Workflow:
1. Set up FFUF command for target URL and wordlist.
2. Identify common file names like `config.php` or `database.php`.
3. Analyze the results to find sensitive information or misconfigurations.
4. Implement security measures to mitigate vulnerabilities.
```

> [!SUCCESS] When you see an unsecured PHP application, think: 
>
> ```text
> Fuzz for common files → Identify potential exposures → Secure configurations.
> ```

---

## 🧰 Tools Used

- **[[FFUF]]**: A fast web fuzzer to discover and enumerate hidden directories, files, and parameters on a target server.

This standardized format ensures all details are preserved while maintaining clarity and structure.