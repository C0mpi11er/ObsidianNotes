# 🌀 cURL Cheat Sheet – The Ultimate Guide 🧠
> **cURL** = *Client for URLs*. Used to transfer data to/from servers using URL syntax. Supports HTTP, HTTPS, FTP, SMTP, LDAP, etc.

---
![[Curl_image.png]]
## 📦 Basic Usage

```bash
curl [options] [URL]
curl https://example.com            # GET request
curl -L https://example.com         # Follow redirects
```

---

## 🌐 HTTP Request Methods

```bash
curl -X GET https://example.com
curl -X POST https://example.com
curl -X PUT https://example.com
curl -X DELETE https://example.com/resource
curl -X PATCH https://example.com
```

---

## 📬 Sending Data

### 📝 Form Data (application/x-www-form-urlencoded)
```bash
curl -d "user=admin&pass=123" https://example.com/login
```

### 📂 File Upload (multipart/form-data)
```bash
curl -F "file=@file.txt" https://example.com/upload
```

### 📄 Raw JSON or Other Body
```bash
curl -X POST https://api.site.com/data \
-H "Content-Type: application/json" \
-d '{"name": "Obsidian"}'
```

---

## 🔐 Headers

```bash
curl -H "Authorization: Bearer <token>" https://api.site.com
curl -H "User-Agent: Mozilla/5.0" https://example.com
curl -I https://example.com        # Only show headers
```

---

## 🍪 Cookies

```bash
curl --cookie "name=value" https://example.com
curl -b cookies.txt https://example.com
curl -c cookies.txt https://example.com
```

---

## 🧪 Debugging / Verbosity

```bash
curl -v https://example.com
curl -s https://example.com
curl -S -s https://example.com
curl -i https://example.com
curl -w "%{http_code}" -o /dev/null https://example.com
```

---

## 🔁 Redirection & Timing

```bash
curl -L https://example.com
curl -m 10 https://example.com
curl --connect-timeout 5 https://example.com
```

---

## 💾 File Downloads

```bash
curl -O https://example.com/file.zip
curl -o customname.zip https://example.com/file.zip
```

---

## 🛃 Authentication

### 🔐 Basic Auth
```bash
curl -u username:password https://example.com
```

### 🪪 Bearer Token / API Key
```bash
curl -H "Authorization: Bearer <token>" https://api.site.com
```

---

## 💥 Security

```bash
curl -k https://self-signed.badssl.com
curl --cacert cert.pem https://secure.com
curl --cert my.pem --key my.key https://ssl.com
```

---

## 📁 FTP / SFTP / SCP

```bash
curl -u user:pass ftp://ftp.example.com/file.txt
curl -T upload.txt ftp://ftp.example.com/
```

---

## 🧙 Advanced Usage

### 🕵️‍♂️ Host Header Injection / VHost Enumeration
```bash
curl -H "Host: target.com" http://1.2.3.4
```

### 🚪 Port and IP
```bash
curl http://10.10.10.10:8080
```

### 📧 SMTP/IMAP/POP3
```bash
curl --url smtp://mail.example.com --mail-from me@example.com --mail-rcpt you@example.com --upload-file mail.txt
```

---

## 🔄 Rate Limiting / Throttling

```bash
curl --limit-rate 100k https://example.com/file.iso
```

---

## 🔍 Proxy Usage

```bash
curl -x http://127.0.0.1:8080 https://example.com
curl -U user:pass -x socks5://127.0.0.1:9050 https://example.com
```

---

## 🔐 Bruteforce / Security Testing

```bash
curl -X POST http://site.com/login -d "user=admin&pass=$pass" >> results.txt
```

---

## 🧾 Save Response

```bash
curl https://example.com -o output.html
curl -s https://api.com/data -o data.json
```

---

## 🖼️ Optional Icons for Obsidian View

✅ = successful  
❌ = failed  
🔐 = secure  
📡 = networking  
📁 = files  
🧪 = testing  
🌀 = general