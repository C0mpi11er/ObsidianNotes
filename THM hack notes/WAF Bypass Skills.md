Bypassing Web Application Firewalls (WAFs) like Cloudflare, AWS WAF, or others is a **red team or penetration testing** task that should only be performed **with explicit written permission**. Here's a breakdown of **legitimate techniques** pentesters use to test for WAF bypasses—structured by layers:

---

### 🔐 **1. WAF Fingerprinting**

First, identify the WAF and its behavior:

- Use tools like:
    
    - `wafw00f` → `wafw00f https://target.com`
        
    - `nmap --script http-waf-detect`
        
- Passive analysis via HTTP headers (e.g., `Server`, `cf-ray`, `X-Amzn-Waf-Id`).
    

---

### 🧠 **2. Payload Obfuscation / Evasion Techniques**

Used to trick WAF parsing mechanisms.

#### 🔸 SQLi

- **Case manipulation**: `UNION SELECT` → `UnIoN sElEcT`
    
- **Inline comments**: `UNI/**/ON SEL/**/ECT`
    
- **URL encoding**: `' OR '1'='1` → `%27%20OR%20%271%27%3D%271`
    
- **Double encoding**: `%2527` = `%27`
    
- **Whitespace tricks**: `/**/`, `--+`, `\t`, `\n`, etc.
    

#### 🔸 XSS

- Obfuscate with:
    
    - `"><svg/onload=alert(1)>`
        
    - Use unicode: `\u003cscript\u003ealert(1)\u003c/script\u003e`
        
    - Base64-encoded payloads injected into parameters
        

---

### 🌐 **3. Alternative HTTP Methods & Headers**

WAFs sometimes only monitor GET/POST.

- Use methods like:
    
    - `OPTIONS`, `PUT`, `HEAD`, `TRACE`
        
- Add headers:
    
    - `X-Original-URL`
        
    - `X-Forwarded-For: 127.0.0.1`
        
    - `X-HTTP-Method-Override: DELETE`
        
    - `X-Custom-IP-Authorization: 127.0.0.1`
        

Use tools:

```bash
curl -X POST https://target.com/resource -H "X-HTTP-Method-Override: GET"
```

---

### 🌍 **4. IP Whitelisting Bypass**

Some WAFs trust internal IPs or CDNs.

- Use **SSRF** to trick the app into making internal calls
    
- Use **open redirect + CDN/IP spoofing** to reroute malicious payloads
    
- Attempt bypass with **Cloudflare origin IP leak** via:
    
    - `securitytrails.com` / `crt.sh` / `dnsdumpster`
        
    - Subdomain brute-force + direct IP access
        

---

### 🔃 **5. Rate Limiting & CAPTCHA Bypass**

- Use **rotating IP proxies**
    
- Implement **delay timers / jitter**
    
- Bypass CAPTCHAs using:
    
    - Known **replayable tokens**
        
    - OCR / AI-based solvers (for advanced red teams)
        

---

### ⚒️ **6. Tools**

|Tool|Purpose|
|---|---|
|`sqlmap`|With tamper scripts for evasion|
|`Burp Suite`|Manual payload crafting, match/replacement, encoder|
|`wafw00f`|WAF detection|
|`Nuclei`|WAF detection templates|
|`Ffuf` / `Dirsearch`|WAF-safe directory fuzzing|
|`TLSCan`, `JA3`, `curl`|TLS/WAF fingerprint variation|

---

### 🧪 **7. Use Tamper Scripts**

`sqlmap -u https://target.com/page.php?id=1 --tamper=between,randomcase,space2comment`

Customize your own **tamper scripts** based on:

- Encodings
    
- Header changes
    
- HTTP method fuzzing
    

---

### 🚧 **8. HTTP2 / WebSocket Exploits**

Some WAFs ignore or poorly implement:

- HTTP/2 (use Burp Suite Pro with HTTP/2 plugin)
    
- WebSocket payloads
    
- gRPC fuzzing
    

---

### 👾 **9. Use DNS-Based Exfiltration**

Some WAFs let outbound DNS:

- Inject DNS payloads to exfil data via tools like `dnschef`, `iodine`, or `dnscat2`.
    

---

### 🛑 LEGAL NOTE

These techniques are **strictly for authorized security assessments**. Unauthorized testing on live websites without permission **violates laws** (e.g., CFAA, GDPR, Nigerian Cybercrimes Act).

---

Would you like a curated **Obsidian cheat sheet** version of this?