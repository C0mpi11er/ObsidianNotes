Awesome! Here's the **combined “Best of Both Worlds” Feroxbuster Cheat Sheet** — blending clarity, depth, and practical use.

---

# 🦊 **Feroxbuster Ultimate Cheat Sheet** 🚀

> For CTFs, Red Team Ops, Web Enumeration, and Daily Pentesting.

---

## 🔰 **What is Feroxbuster?**

**Feroxbuster** is a **fast, recursive content discovery tool** written in Rust. It bruteforces **hidden directories and files** on web servers.

---

## 📌 **Basic Syntax**

```bash
feroxbuster -u <target_url>
```

- `-u`: Target URL (e.g., `http://example.com`)
    

---

## 🔧 **Common Options**

|Option|Description|
|---|---|
|`-w`|Path to wordlist|
|`-t`|Number of threads (default: 10)|
|`-r`|Enable recursion|
|`-x`|Extensions to append (e.g., `php,html,txt`)|
|`-o`|Output file|
|`-C`|Filter status codes (e.g., `404,403`)|
|`-q`|Quiet mode (no banner or progress)|
|`--proxy`|Use HTTP/SOCKS proxy (e.g., `http://127.0.0.1:8080`)|
|`-H`|Custom request headers|
|`--insecure`|Skip TLS cert validation|
|`--rate-limit`|Throttle request rate (e.g., `5`)|
|`--json`|JSON output format|
|`-n`|Disable response size filtering|
|`--depth`|Limit recursion depth|

---

## 🚀 **Most Used Commands**

### 🔹 Basic Scan

```bash
feroxbuster -u http://target.com
```

### 🔹 Use a Custom Wordlist

```bash
feroxbuster -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

### 🔹 Multithreaded Scan (Speed Boost)

```bash
feroxbuster -u http://target.com -t 50
```

### 🔹 Recursively Find Subdirs

```bash
feroxbuster -u http://target.com -r
```

### 🔹 Filter Out 404s and 403s

```bash
feroxbuster -u http://target.com -C 404,403
```

### 🔹 Save Results to File

```bash
feroxbuster -u http://target.com -o scan.txt
```

### 🔹 Quiet Mode

```bash
feroxbuster -u http://target.com -q
```

---

## 🕵️ **Advanced Techniques**

### 🔸 Scan for Specific Extensions

```bash
feroxbuster -u http://target.com -x php,txt,html
```

### 🔸 Add Custom Headers (WAF Bypass / Spoof)

```bash
feroxbuster -u http://target.com -H "User-Agent: Googlebot"
```

### 🔸 Send Through a Proxy (e.g., Burp Suite)

```bash
feroxbuster -u http://target.com --proxy http://127.0.0.1:8080
```

### 🔸 Rate Limiting for Stealth

```bash
feroxbuster -u http://target.com --rate-limit 5
```

### 🔸 Skip SSL Verification (For Self-Signed Certs)

```bash
feroxbuster -u https://target.com --insecure
```

### 🔸 Disable Length Filtering (WAF/Evasion)

```bash
feroxbuster -u http://target.com -n
```

### 🔸 Output JSON for Automation

```bash
feroxbuster -u http://target.com -o result.json --json
```

### 🔸 Limit Recursion Depth

```bash
feroxbuster -u http://target.com -r --depth 2
```

---

## 💡 **Top Use Cases**

✅ **Fast Recursive Scan**

```bash
feroxbuster -u http://target.com -t 60 -r
```

✅ **Stealthy + Proxy + Rate-Limited**

```bash
feroxbuster -u http://target.com --proxy http://127.0.0.1:8080 -t 10 --rate-limit 2
```

✅ **Scan Only for .php Files**

```bash
feroxbuster -u http://target.com -x php
```

✅ **Filter Noise (Show Only 200s)**

```bash
feroxbuster -u http://target.com -C 403,404,301,302
```

✅ **Bypass Filters**

```bash
feroxbuster -u http://target.com -H "X-Forwarded-For: 127.0.0.1"
```

---

## ⚔️ **Feroxbuster vs Gobuster**

|Feature|**Feroxbuster** 🦊|**Gobuster** ⚡|
|---|---|---|
|Speed|🚀 Faster (Rust)|Fast (Go)|
|Recursion|✅ Yes|❌ No|
|Wildcard Filtering|✅ Yes|❌ No|
|Output Formats|✅ JSON, CSV, TXT|❌ Plain only|
|Proxy Support|✅|✅|
|WAF Bypass|✅ Headers, Rate Limit|⚠️ Manual|

---

## 🛠️ Pro Tips

- Start small (`-t 10 -w common.txt`) to avoid rate limits.
    
- Use large wordlists only during off-peak hours.
    
- Always check for wildcard responses or WAF behavior.
    
- Pair with tools like **Burp**, **FFUF**, or **dirsearch**.
    

---

Would you like this exported as a **PDF**, **Markdown**, or **Notion-compatible format**?