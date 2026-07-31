# 🛠️ Upload Paths Discovery

> [!ABSTRACT] This document outlines how to discover upload paths such as `/uploads/`, `/profile_images/`, and `/assets/images/` through source inspection.

---

## 🔍 Source Inspection Overview

### Path Discovery Techniques

Source code review is one of the primary methods for identifying potential file upload paths. Reviewing backend files, such as controllers or models in frameworks like Laravel, Django, or Ruby on Rails can reveal where files are being uploaded.

> [!INFO] Common Upload Paths
>
> - `/uploads/`
> - `/profile_images/`
> - `/assets/images/`

---

## 🚀 Initial Discovery Steps

### Manual Inspection

Manually browse through the project's source code to locate directories and filenames related to file uploads. Look for patterns such as:

- Directories named `upload`, `images`, or `files`.
- File names with keywords like `upload.php`, `file_upload.py`, etc.

> [!SUCCESS] When you find a relevant script, inspect it closely to understand how files are processed and stored.

---

## 📝 Source Code Examples

### Example 1: PHP Upload Script
```php
<?php
if (isset($_FILES['uploaded_file'])) {
    $file_name = basename($_FILES['uploaded_file']['name']);
    $upload_dir = "/uploads/";
    move_uploaded_file($_FILES['uploaded_file']['tmp_name'], "$upload_dir/$file_name");
}
?>
```

### Example 2: Python Upload Handler
```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/upload', methods=['POST'])
def upload_file():
    file = request.files['file']
    if file:
        filename = secure_filename(file.filename)
        file.save(os.path.join("/uploads/", filename))
```

---

## 📜 Verification Steps

### Using Web Crawlers
Use automated tools like [[Wappalyzer]], [[Buster]], or custom scripts to search for upload paths.

> [!CHECK] Verify if the discovered path is actually writable by uploading a test file and checking its presence on the server.

---

## 🔑 Path Enumeration Techniques

### Automated Scanning Tools
Automated tools can also help in identifying potential upload paths:

```bash
# Using Nikto to search for common upload directories
nikto -h <target> -C all | grep "upload"
```

---

## ⚠️ Common Findings & Risks

| Finding                          | Impact                           |
|----------------------------------|---------------------------------|
| Misconfigured Upload Directories | File Inclusion, Remote Code Execution |
| Unrestricted File Uploads        | Arbitrary File Write             |

> [!WARNING] Be cautious of unrestricted file uploads as they can lead to serious security issues such as remote code execution.

---

## 🧩 Exam Mental Model

```text
Inspect Source Code → Locate Upload Paths → Verify Writability → Enumerate Further
```

Whenever you find an upload path:
1. Ensure it is writable.
2. Look for other potential vulnerabilities.
3. Document findings and proceed with caution to avoid triggering alerts.

---

## 📌 High-Value Files to Hunt

Search for files like `config.php`, `.env`, or any file containing sensitive information that may be uploaded via the discovered paths:

```text
.config
.env
secret.txt
passwords.conf
```

Also, look out for keywords such as `credentials`, `secrets`, and `tokens`.

---

## 🧠 Summary & Next Steps

### Final Thoughts

Understanding how to discover upload paths through source inspection is crucial in identifying potential security vulnerabilities.

> [!NOTE]
> Always verify findings with manual checks before proceeding with exploitation.