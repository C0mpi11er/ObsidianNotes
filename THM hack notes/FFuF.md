
---

# 🧨 FFUF Master Cheat Sheet

> **Tool**: Fuzz Faster U Fool  
> **Author**: [@ffuf.me](https://github.com/ffuf/ffuf)  
> **Category**: Web Fuzzing, Enumeration  
> **Language**: Go  
> **Goal**: Directory, vhost, parameter, and header fuzzing

---

## 📦 INSTALLATION

```bash
sudo apt install ffuf
# or via Go:
go install github.com/ffuf/ffuf/v2@latest
```

---

## 🔰 BASIC SYNTAX

```bash
ffuf -u https://target/FUZZ -w wordlist.txt
```

- `FUZZ` is the magic keyword placeholder
    
- Supports multiple wordlists and placeholders!
    

---

## 🧠 USAGE MODES

|Mode|Description|
|---|---|
|Directory Bruteforce|Find hidden folders/files|
|File Extension Guess|Append extensions (`.php`, `.bak`, etc.)|
|Parameter Discovery|Find vulnerable or hidden GET/POST parameters|
|VHost Fuzzing|Discover subdomains via `Host:` header|
|Header Manipulation|Test auth or bypass headers|
|Cookie Injection|Fuzz cookies|
|JSON Fuzzing|POST JSON body fuzz|
|Multiple Wordlists|Use more than one FUZZ in URL|
|Non-Bruteforce Recon|Filter known vs unknown responses|
|Recursive Fuzzing|Combine tools/scripts for deeper discovery|

---

## ⚙️ OPTIONS BREAKDOWN

|Flag|Description|
|---|---|
|`-u`|URL with one or more `FUZZ` keywords|
|`-w`|Wordlist|
|`-H`|Custom headers|
|`-X`|HTTP method (GET, POST, etc.)|
|`-d`|POST data payload|
|`-mc`|Match HTTP status codes|
|`-fc`|Filter HTTP status codes|
|`-mr`|Match regex in response body|
|`-fs`|Filter by response size|
|`-fl`|Filter by content length|
|`-o`|Output file|
|`-of`|Output format (`json`, `csv`, `html`, `md`)|
|`-t`|Number of threads (default: 40)|
|`-rate`|Max requests per second|
|`-timeout`|Timeout per request|
|`--replay-proxy`|Send matching requests to proxy (e.g. Burp)|

---

## 📁 DIRECTORY FUZZING

```bash
ffuf -u https://target.com/FUZZ -w /path/to/wordlist.txt -mc 200
```

---

## 📄 FILE EXTENSION ENUMERATION

```bash
ffuf -u https://target.com/index.FUZZ -w extensions.txt -mc 200
```

Common file extension list:

```
php
html
bak
old
txt
conf
```

---

## 🧬 GET PARAMETER ENUMERATION

```bash
ffuf -u https://target.com/page.php?FUZZ=value -w params.txt
```

## 💥 VALUE FUZZING

```bash
ffuf -u https://target.com/page.php?id=FUZZ -w payloads.txt
```

---

## 🔁 POST PARAMETER FUZZING

```bash
ffuf -u https://target.com/login -X POST -d "username=admin&password=FUZZ" \
-w rockyou.txt -H "Content-Type: application/x-www-form-urlencoded"
```

---

## 🧱 JSON FUZZING

```bash
ffuf -u https://target.com/api/login -X POST \
-d '{"username":"admin", "password":"FUZZ"}' \
-H "Content-Type: application/json" -w rockyou.txt
```

---

## 🔐 COOKIE FUZZING

```bash
ffuf -u https://target.com/secure -H "Cookie: session=FUZZ" -w tokens.txt
```

---

## 🔄 HEADER FUZZING

```bash
ffuf -u https://target.com -H "X-Forwarded-For: FUZZ" -w ips.txt
```

🧠 Great for bypassing WAF or IP-based restrictions.

---

## 🌍 VHOST DISCOVERY

```bash
ffuf -u http://target/ -H "Host: FUZZ.target.com" -w subdomains.txt -fs 4242
```

📌 Use `-fs` to filter out known false positive size responses.

---

## 🎯 MULTIPLE FUZZ POINTS

```bash
ffuf -u https://target.com/FUZZ/FUZZ2 -w list1.txt:FUZZ -w list2.txt:FUZZ2
```

---

## 🔍 FILTERING TECHNIQUES

|Flag|Use|
|---|---|
|`-mc 200`|Match only 200 OK|
|`-fc 404`|Filter out 404s|
|`-fs 1024`|Filter responses with size 1024|
|`-ml 50`|Match responses of length 50|
|`-mr admin`|Match responses with word "admin"|

---

## 📊 OUTPUT FORMATS

```bash
-of json   # JSON
-of csv    # CSV
-of html   # Web report
-of md     # Markdown
```

Example:

```bash
ffuf -u ... -o results.json -of json
```

---

## ⚡ RATE LIMITING & PERFORMANCE

```bash
ffuf -u https://target/FUZZ -w biglist.txt -t 100 -rate 200 -timeout 10
```

|Flag|Meaning|
|---|---|
|`-t 100`|Threads|
|`-rate`|Max requests/sec|
|`-timeout`|Seconds per request|

---

## 🧪 LIVE HOST CHECK (pipe to httpx)

```bash
ffuf -u https://FUZZ.target.com -w subs.txt -mc 200 -o subs.txt
cat subs.txt | httpx
```

---

## 🔁 RECURSIVE FUZZING STRATEGY

FFUF does **not** recurse automatically. Use tools like:

- [ffuf-scripts](https://github.com/d3mondev/ffuf-scripts)
    
- Custom Bash loop:
    

```bash
for dir in $(ffuf ...); do
  ffuf -u https://target.com/$dir/FUZZ -w ...;
done
```

---

## 🧪 DEBUG / REPLAY WITH BURP

```bash
--replay-proxy http://127.0.0.1:8080
```

Send matched requests to **Burp/ZAP** for analysis.

---

## 🧠 BYPASSING 403/401

Try these:

```bash
-H "X-Original-URL: /admin"
-H "X-Custom-IP-Authorization: 127.0.0.1"
-H "X-Forwarded-For: 127.0.0.1"
```

---

## 📁 COMMON WORDLISTS

### 🔗 SecLists:

- `Discovery/Web-Content/`
    
- `Fuzzing/`
    
- `DNS/`
    
- `Miscellaneous/wordlists`
    

```bash
git clone https://github.com/danielmiessler/SecLists.git
```

---

## 📊 FFUF vs GOBUSTER vs DIRB

|Feature|FFUF|Gobuster|Dirb|
|---|---|---|---|
|Fast (Multi-threaded)|✅|✅|❌|
|Regex filtering|✅|❌|❌|
|POST/JSON|✅|❌|❌|
|Header fuzzing|✅|❌|❌|
|Multiple placeholders|✅|❌|❌|
|Output formats|✅|Limited|❌|

---

## 🧷 Recommended Obsidian Save Path

```
/Notes/Hacking/Web/FFUF-CheatSheet.md
```

---

Let me know if you'd like:

- A `.md` file + preview image bundle
    
- A matching FFUF GUI wrapper reference
    
- Or a **combo cheat sheet**: FFUF + Gobuster + Dirb
    

I can also generate printable PDFs or visual mind maps if needed.