

---

# 🛠️ cURL Cheat Sheet

> **cURL = Client URL** – command-line tool for transferring data with URLs.  
> Supports HTTP(S), FTP, SFTP, SMTP, LDAP, MQTT, and more.

---

## 🔹 Basic Usage

```bash
curl [options] [URL]
```

### Examples

```bash
curl https://example.com             # Simple GET request
curl -O https://example.com/file.zip # Download file (keep name)
curl -o myfile.zip https://example.com/file.zip  # Download and rename
```

---

## 🔹 Request Methods

```bash
-X <METHOD>   # Specify HTTP method
```

### Examples

```bash
curl -X GET https://api.site.com/users
curl -X POST https://api.site.com/users -d "name=John&age=30"
curl -X PUT https://api.site.com/users/1 -d "name=Alice"
curl -X DELETE https://api.site.com/users/1
```

---

## 🔹 Data & Payloads

```bash
-d <data>     # Send data (POST/PUT)
--data-urlencode <data> # URL-encode data
```

### Examples

```bash
curl -d "name=John&age=30" https://api.site.com/users
curl -H "Content-Type: application/json" \
     -d '{"name":"Alice","age":25}' \
     https://api.site.com/users
```

---

## 🔹 Headers

```bash
-H "<Header: value>"   # Add custom header
-I                     # Fetch headers only
```

### Examples

```bash
curl -I https://example.com
curl -H "Authorization: Bearer TOKEN" https://api.site.com/data
curl -H "User-Agent: MyApp/1.0" https://example.com
```

---

## 🔹 Authentication

```bash
-u user:pass           # Basic auth
--oauth2-bearer TOKEN  # OAuth 2.0 Bearer token
```

### Examples

```bash
curl -u admin:1234 https://example.com/secure
curl -H "Authorization: Bearer my_token" https://api.site.com/data
```

---

## 🔹 File Uploads

```bash
-F "field=@file"   # Upload file (multipart/form-data)
```

### Examples

```bash
curl -F "file=@report.pdf" https://api.site.com/upload
```

---

## 🔹 Cookies & Sessions

```bash
-b "name=value"       # Send cookies
-c cookies.txt        # Save cookies
-b cookies.txt        # Load cookies
```

### Examples

```bash
curl -b "sessionid=12345" https://example.com
curl -c cookies.txt https://example.com
curl -b cookies.txt https://example.com/dashboard
```

---

## 🔹 Output & Formatting

```bash
-o <file>     # Write to file
-O            # Save with remote filename
-s            # Silent (no progress)
-S            # Show errors (with -s)
-v            # Verbose (debug)
```

### Examples

```bash
curl -o out.html https://example.com
curl -s https://example.com -o /dev/null
curl -v https://example.com
```

---

## 🔹 Redirects

```bash
-L            # Follow redirects
```

### Example

```bash
curl -L http://bit.ly/xyz
```

---

## 🔹 Proxy

```bash
-x <[protocol://]host:port>  # Use proxy
```

### Example

```bash
curl -x socks5://127.0.0.1:9050 https://example.com
```

---

## 🔹 SSL/TLS

```bash
-k            # Ignore SSL certificate errors
--cert cert.pem --key key.pem  # Client cert
```

### Example

```bash
curl -k https://self-signed.badssl.com/
```

---

## 🔹 FTP / SFTP

```bash
curl -u user:pass ftp://ftp.example.com/file.txt
curl -u user:pass -T local.txt sftp://example.com/upload/
```

---

## 🔹 Debugging & Timing

```bash
-v                 # Verbose
--trace trace.txt  # Full trace
--trace-ascii log.txt
-w "%{time_total}\n" # Measure total time
```

### Example

```bash
curl -w "Total time: %{time_total}s\n" -o /dev/null -s https://example.com
```

---

## 🔹 Common Shortcuts

- `-X` → request method
    
- `-d` → send data
    
- `-H` → set header
    
- `-u` → authentication
    
- `-o` / `-O` → save output
    
- `-L` → follow redirects
    
- `-v` → verbose/debug
    

---

# 🚀 Real-World API Example

```bash
curl -X POST https://api.example.com/login \
     -H "Content-Type: application/json" \
     -d '{"username":"admin","password":"1234"}' \
     -c cookies.txt

curl -b cookies.txt https://api.example.com/dashboard
```

---

⚡ **Pro Tip:** Combine flags for power usage:

```bash
curl -s -o /dev/null -w "Status: %{http_code}\nTime: %{time_total}s\n" https://example.com
```

---


---

# 🔒 cURL — Pentest / Security Testing Cheat Sheet (Ethical use only)

## ⚖️ Legal & Safety Reminder

Always have explicit permission before testing systems. Use a controlled lab for experiments. Log your tests and limit impact (rate limits, small payloads).

---

## 🔍 1) Recon & Fingerprinting

Quickly gather headers, server, and tech-stack hints.

Show full response headers + body:

```bash
curl -i https://target.example.com
```

Show only response headers:

```bash
curl -I https://target.example.com
```

Verbose networking info (TLS handshake, request/response):

```bash
curl -v https://target.example.com
```

Show HTTP status code and timing summary (useful to detect slow endpoints):

```bash
curl -s -o /dev/null -w "HTTP: %{http_code}  Time: %{time_total}s\n" https://target.example.com
```

Detect allowed HTTP methods (`Allow` header or `OPTIONS`):

```bash
curl -X OPTIONS -i https://target.example.com/resource
```

Check for common security headers (CSP, HSTS, X-Frame-Options, etc.):

```bash
curl -I https://target.example.com | egrep -i "strict-transport-security|content-security-policy|x-frame-options|x-content-type-options|referrer-policy|permissions-policy"
```

---

## 🔐 2) Authentication & Session Testing

Test basic auth:

```bash
curl -u username:password -I https://target.example.com/protected
```

Test token-based auth header behavior:

```bash
curl -H "Authorization: Bearer BADTOKEN" -i https://api.target.example.com
```

Save and reuse cookies (session sim):

```bash
curl -c cookies.txt -d "username=me&password=pass" https://target.example.com/login
curl -b cookies.txt https://target.example.com/dashboard
```

Check for weak redirects after login (open redirect risk):

```bash
curl -v "https://target.example.com/login?next=http://evil.example.com/"
```

(Inspect `Location:` header in response for suspicious redirect targets.)

---

## 🌐 3) CORS (Cross-Origin Resource Sharing) Testing

Test how server responds to different `Origin` headers:

```bash
curl -H "Origin: https://attacker.example" -I https://api.target.example/resource
```

Look for `Access-Control-Allow-Origin` header in response — improper `*` or echoing attacker origin can be risky.

Test preflight (`OPTIONS`) behavior:

```bash
curl -X OPTIONS -H "Origin: https://attacker.example" -H "Access-Control-Request-Method: POST" -i https://api.target.example/resource
```

---

## ↪️ 4) Open Redirect & SSRF Detection (safe checks)

**Open redirect (check `Location` header):**

```bash
curl -I "https://target.example/redirect?url=https://evil.example"
```

If `Location:` points directly to external input, it may be vulnerable.

**SSRF detection (lab/internal host attempt):**  
Only run in an authorized environment. Some servers fetch URLs you supply — test if server will fetch internal resources:

```bash
curl -G --data-urlencode "url=http://127.0.0.1:80/admin" "https://vulnerable.target/fetch"
```

(Inspect response for content from the internal URL or any errors indicating internal fetch.)

---

## 🧪 5) Input Validation & Basic Fuzzing (non-destructive)

Send unusual headers and content types to see how server reacts:

Change `Content-Type` to unexpected value:

```bash
curl -X POST https://target.example/api -H "Content-Type: application/xml" -d '<test>1</test>'
```

URL encode suspicious input (seeing how server decodes):

```bash
curl -G --data-urlencode "q=../../etc/passwd" https://target.example.com/search
```

Use `--max-time` to avoid hanging requests during fuzzing:

```bash
curl --max-time 10 -d "input=abc" https://target.example/test
```

For many fuzzing requests use dedicated tools (wfuzz, ffuf) — curl is for single-shot tests or small batches.

---

## 📁 6) File Upload & Content-Type Checks

Upload as multipart/form-data (test server validation):

```bash
curl -F "file=@./test.txt;type=text/plain" https://target.example/upload
```

Try changing filename or MIME to test validation:

```bash
curl -F "file=@./malicious.php;filename=shell.jpg;type=image/jpeg" https://target.example/upload
```

(Do this only in labs — checks whether server relies on filename/extension only.)

---

## 🔄 7) Host Header & Virtual Host Testing

Simulate requests with different `Host:` to test virtual host routing / host-header poisoning:

```bash
curl -H "Host: admin.target.example" https://203.0.113.10/
```

Or map host via `--resolve` without DNS changes:

```bash
curl --resolve target.example:443:203.0.113.10 https://target.example/
```

---

## 🕵️ 8) TLS / Certificate & Client Cert Tests

Ignore invalid certs in lab:

```bash
curl -k https://self-signed.badssl.com/
```

Use a client certificate for mTLS endpoints:

```bash
curl --cert client.crt --key client.key https://mtls.target.example/
```

Dump TLS handshake info:

```bash
curl -vI https://target.example 2>&1 | sed -n '1,60p'
```

---

## 🕰️ 9) Timing-Based Tests

Measure response times (detect time-based SQLi or slow endpoints—do gentle tests only):

```bash
curl -s -o /dev/null -w "Time_total: %{time_total}\n" "https://target.example/resource?q=test"
```

Use `--max-time` to cap request duration:

```bash
curl --max-time 5 https://target.example/slow
```

---

## 🚪 10) Rate-Limit & Brute-Force (Guidance & Caution)

- **Do not** brute-force production systems without permission.
    
- Test rate limiting by sending a small burst and inspecting headers (e.g., `X-RateLimit-*`):
    

```bash
curl -I https://api.target.example/resource
```

- For responsible load testing, use controlled tooling and coordinate with owners.
    

---

## 🔁 11) Proxy / Intercepting (Burp/ZAP) Integration

Route requests via a local proxy (Burp/ZAP) for inspection:

```bash
curl -x http://127.0.0.1:8080 -k https://target.example/
```

Combine with `-v` to see proxy negotiation.

---

## 🧾 12) Export/Import Requests (Reproducibility)

Save request/response trace for analysis:

```bash
curl --trace-ascii trace.txt -i https://target.example
```

Recreate requests with `--data` and headers captured from tools like Burp for controlled retests.

---

## ✅ 13) Defensive Checks (Security Best-Practices to Look For)

When auditing, check for:

- Missing `Strict-Transport-Security` (HSTS).
    
- Missing `X-Frame-Options` / `frame-ancestors` (clickjacking).
    
- Missing or permissive `Content-Security-Policy`.
    
- `Access-Control-Allow-Origin` echoing request origin.
    
- Server permitting unsafe HTTP methods (PUT/DELETE) where not required.
    
- Insecure file upload handling (no content inspection).
    
- Absence of rate limiting headers on auth endpoints.
    

Use the earlier header-checking commands to spot these.

---

## 🧰 14) Tools to Use Alongside cURL

For deeper or automated testing, use specialized tools in an authorized environment:

- Interception & manipulation: **Burp Suite**, **OWASP ZAP**
    
- Web fuzzing: **ffuf**, **wfuzz**
    
- SQLi automation: **sqlmap** (authorized targets only)
    
- API testing: **HTTPie**, **Postman**
    

---

## 📝 Quick Reference — Handy Flags

- `-I` → headers only
    
- `-v` → verbose
    
- `-s -o /dev/null -w` → scripted status/timing output
    
- `-H` → header
    
- `-d` / `--data` → body data
    
- `-F` → multipart/form-data (upload)
    
- `-u` → basic auth
    
- `-x` → proxy
    
- `--resolve` → override DNS for host
    
- `--max-time` → timeout
    
- `-k` → ignore TLS cert errors (lab only)
    
- `-c` / `-b` → cookie jar
    

---

