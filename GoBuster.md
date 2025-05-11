Here is your **updated and extremely comprehensive GoBuster Cheat Sheet**, optimized for **real-world use**, **fast reference**, and **CTF/pentesting readiness**. It includes **all modes**, **flags**, and **practical examples**.

---

# 🛠️ GoBuster Full Cheat Sheet (v3.x+)

---

## 📦 GENERAL SYNTAX

```bash
gobuster [mode] -u <URL> -w <wordlist> [options]
```

---

## ⚙️ GLOBAL FLAGS (ALL MODES)

|Flag|Description|
|---|---|
|`-u, --url`|Target URL or IP|
|`-w, --wordlist`|Path to wordlist|
|`-t, --threads`|Number of threads (default: 10)|
|`-o, --output`|Save results to file|
|`-k, --no-tls-validation`|Skip TLS cert check|
|`--timeout`|Timeout (default: 10s)|
|`--user-agent`|Custom User-Agent|
|`--random-agent`|Use random User-Agent|
|`--proxy`|Use proxy (e.g., `http://127.0.0.1:8080`)|
|`--headers`|Add custom headers (`"H1: v1\nH2: v2"`)|
|`--username`, `--password`|Basic auth|
|`--method`|HTTP method (GET, POST, HEAD, etc.)|
|`--delay`|Delay between requests|
|`--no-error`|Hide non-critical errors|

---

## 🌐 MODE: `dir` (Directory & File Brute-Forcing)

```bash
gobuster dir -u http://target -w /path/to/list.txt [options]
```

### Useful Flags

|Flag|Description|
|---|---|
|`-x, --extensions`|File extensions to append (e.g., `php,txt`)|
|`-e, --expanded`|Show full URLs|
|`--wildcard`|Force wildcard detection|
|`-s, --status-codes`|Only show these status codes|
|`-b, --status-codes-blacklist`|Hide these codes|
|`--exclude-length`|Filter by length (`--exclude-length 77`, `250-300`)|
|`--follow-redirect`|Follow 3xx redirects|
|`--canonicalize`|Clean path display|

### Example

```bash
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -s 200,204,301,302 -t 50
```

---

## 📁 MODE: `dns` (Subdomain Brute-Forcing)

```bash
gobuster dns -d domain.com -w /path/to/list.txt [options]
```

### Useful Flags

|Flag|Description|
|---|---|
|`-d, --domain`|Domain name|
|`-r, --resolver`|Use custom DNS server|
|`-i, --show-ips`|Show IPs of subdomains|
|`-z, --show-cname`|Show CNAMEs|
|`--wildcard`|Detect wildcard DNS|
|`--timeout`|DNS timeout|

### Example

```bash
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -i -t 100
```

---

## 🔌 MODE: `vhost` (Virtual Host Brute-Forcing)

```bash
gobuster vhost -u http://IP -w /path/to/list.txt [options]
```

### Useful Flags

|Flag|Description|
|---|---|
|`--domain`|Append domain to entries|
|`--append-domain`|Use `entry.domain` format|
|`--exclude-length`|Filter by body length (range or exact)|
|`--status-codes`|Show only certain codes|
|`--follow-redirect`|Follow 3xx redirects|

### Example (Realistic TryHackMe/HTB)

```bash
gobuster vhost -u http://10.10.50.129 --domain example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320
```

---

## 🪣 MODE: `s3` (AWS S3 Bucket Discovery)

```bash
gobuster s3 -w /path/to/list.txt [options]
```

### Flags

|Flag|Description|
|---|---|
|`-w`|Bucket name list|
|`-t`|Threads|
|`-o`|Output to file|
|`--no-error`|Suppress warnings/errors|

### Example

```bash
gobuster s3 -w /usr/share/seclists/Discovery/S3/buckets.txt -t 50
```

---

## 🔧 MODE: `fuzz` (Generic Fuzzing)

```bash
gobuster fuzz -u http://site.com/FUZZ -w /path/to/list.txt [options]
```

### Flags

|Flag|Description|
|---|---|
|`-u`|URL must contain `FUZZ`|
|`-w`|Fuzzing wordlist|
|`-s`|Only show specific status codes|
|`--exclude-length`|Filter by response size|
|`--method`|Use GET/POST/HEAD/etc.|
|`--headers`|Set custom headers|

### Example

```bash
gobuster fuzz -u http://target/FUZZ -w fuzzlist.txt -s 200,204,403 -t 40
```

---

## 🗂️ COMMON WORDLISTS (SecLists)

|Purpose|Path|
|---|---|
|Directories|`/usr/share/seclists/Discovery/Web-Content/`|
|Subdomains|`/usr/share/seclists/Discovery/DNS/`|
|S3 Buckets|`/usr/share/seclists/Discovery/S3/`|
|Fuzzing|`/usr/share/seclists/Fuzzing/`|

---

## 🚀 QUICK RECIPES

### 🔍 Web Enum (Common + Extensions)

```bash
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -t 40 -s 200,204,301,302
```

### 🕵️ Subdomain Scan (Show IPs)

```bash
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -i
```

### 🛠️ Virtual Host Fuzz

```bash
gobuster vhost -u http://10.10.50.129 --domain site.thm -w /usr/share/wordlists/subdomains.txt --append-domain --exclude-length 250-320
```

### 🧪 Fuzz GET Params

```bash
gobuster fuzz -u "http://10.10.10.10/index.php?FUZZ=test" -w fuzz.txt -s 200,500
```

---

Would you like me to export this as a **Markdown file** or generate a **PDF version** for printing or offline use?