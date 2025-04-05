### **📜 Feroxbuster Cheat Sheet 🚀**

#### **🔹 Basic Usage**

```bash
feroxbuster --url <target_url>
```

- Scans for **hidden directories and files** on a web server.
    

#### **🔹 Using a Custom Wordlist**

```bash
feroxbuster --url <target_url> -w /path/to/wordlist.txt
```

- Uses a specific **wordlist** for brute-forcing directories/files.
    

#### **🔹 Multithreading for Faster Scans**

```bash
feroxbuster --url <target_url> -t 50
```

- Uses **50 threads** for parallel scanning (default is **10**).
    

#### **🔹 Recursively Scan Found Directories**

```bash
feroxbuster --url <target_url> -r
```

- **Automatically scans subdirectories** when found.
    

#### **🔹 Filtering Out Unwanted Status Codes**

```bash
feroxbuster --url <target_url> -C 404,403
```

- Hides **404 (Not Found)** and **403 (Forbidden)** responses.
    

#### **🔹 Saving Output to a File**

```bash
feroxbuster --url <target_url> -o results.txt
```

- Saves scan results to **results.txt**.
    

#### **🔹 Quiet Mode (No Banner, Minimal Output)**

```bash
feroxbuster --url <target_url> -q
```

- Runs **silently**, showing only valid results.
    

#### **🔹 Customizing Request Headers**

```bash
feroxbuster --url <target_url> -H "User-Agent: CustomUA"
```

- Sets a **custom User-Agent** to bypass basic detection.
    

#### **🔹 Using a Proxy for Anonymity**

```bash
feroxbuster --url <target_url> --proxy http://127.0.0.1:8080
```

- Sends traffic through a **proxy (e.g., Burp Suite, SOCKS5, or VPN)**.
    

#### **🔹 Scanning Only for Specific Extensions**

```bash
feroxbuster --url <target_url> -x php,html,txt
```

- Searches only for **.php, .html, .txt** files.
    

#### **🔹 Rate Limiting to Avoid Detection**

```bash
feroxbuster --url <target_url> --rate-limit 5
```

- Limits requests to **5 per second** (useful for stealth scans).
    

#### **🔹 Skipping SSL Certificate Verification**

```bash
feroxbuster --url https://target.com --insecure
```

- Ignores **SSL/TLS certificate errors** (useful for self-signed certs).
    

#### **🔹 Output in JSON Format**

```bash
feroxbuster --url <target_url> -o results.json --json
```

- Saves output as **JSON** for easier parsing.
    

#### **🔹 Running with Default Wordlist**

```bash
feroxbuster --url <target_url> -w /usr/share/wordlists/dirb/common.txt
```

- Uses **common.txt** from **Dirb** for standard enumeration.
    

#### **🔹 Checking Response Length to Detect WAF/Evasion**

```bash
feroxbuster --url <target_url> -n
```

- **Disables automatic filtering** based on response length.
    

#### **🔹 Avoiding Redirects (Preventing Scanning External Domains)**

```bash
feroxbuster --url <target_url> --no-recursion
```

- Stops recursion from following redirects.
    

---

### **💡 Common Use Cases**

✅ **Fast directory brute-force with recursion:**

```bash
feroxbuster --url http://target.com -t 60 -r
```

✅ **Only scan for PHP and TXT files:**

```bash
feroxbuster --url http://target.com -x php,txt
```

✅ **Bypass WAF with custom headers & User-Agent:**

```bash
feroxbuster --url http://target.com -H "X-Forwarded-For: 127.0.0.1" -H "User-Agent: Googlebot"
```

✅ **Stealth scan with proxy, low threads & rate limit:**

```bash
feroxbuster --url http://target.com --proxy http://127.0.0.1:8080 -t 10 --rate-limit 2
```

✅ **Export results in JSON for automation:**

```bash
feroxbuster --url http://target.com -o output.json --json
```

---

### **🔥 Why Use Feroxbuster Over Gobuster?**

|Feature|**Feroxbuster** 🚀|**Gobuster** ⚡|
|---|---|---|
|**Speed**|Faster (Rust-based)|Fast (Go-based)|
|**Recursion**|✅ Built-in|❌ Manual input required|
|**Wildcard Handling**|✅ Yes|❌ No|
|**Response Filtering**|✅ Yes (`-C 404,403`)|✅ Yes (`-b 404`)|
|**Output Formats**|✅ JSON, CSV, TXT|✅ Plain text only|
|**Proxy Support**|✅ Yes|✅ Yes|
|**TLS Certificate Handling**|✅ Yes|❌ No|

💡 **TL;DR:** Use **Feroxbuster** for **speed, recursion, and automation**, but **Gobuster** is still good for lightweight tasks.

---

### **🛠️ Final Tips**

- **Increase threads (`-t`)** for **faster scans**, but beware of **rate limits.**
    
- **Use proxies (`--proxy`)** to **anonymize** and evade detection.
    
- **Filter responses (`-C 403,404`)** to reduce clutter.
    
- **Use recursion (`-r`)** to find **deeper directories automatically.**
    

Would you like me to add any advanced techniques? 🚀