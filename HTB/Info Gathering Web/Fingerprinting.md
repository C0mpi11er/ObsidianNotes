# 🧠 Web Fingerprinting

---

> [!info] 📌 Overview  
> **Web Fingerprinting** is the process of identifying the technologies powering a target web application.

It helps uncover:

- Web server software
    
- Frameworks
    
- CMS platforms
    
- Security controls
    
- Misconfigurations
    
- Version info
    

Think of it as extracting a website’s **digital DNA**.

---

# 🌐 Why Fingerprinting Matters

Fingerprinting lets you tailor recon and exploitation.

It helps with:

|Purpose|Benefit|
|---|---|
|**Targeted Attacks**|Match exploits to exact tech stack|
|**Version Discovery**|Spot outdated software|
|**Misconfig Detection**|Find weak/default configs|
|**Attack Surface Mapping**|Understand infrastructure|

---

# 🧠 What You Can Identify

Common discoveries:

```bash
Apache/2.4.41
Nginx/1.18.0
WordPress
PHP 8.1
Cloudflare
Laravel
React
Wordfence
```

---

# 🔍 Core Fingerprinting Techniques

## 1. Banner Grabbing

Pull server response headers.

```bash
curl -I https://target.com
```

Look for:

- Server
    
- X-Powered-By
    
- Set-Cookie
    
- CSP
    
- Security headers
    

Example:

```bash
Server: Apache/2.4.41
X-Powered-By: PHP/8.1
```

---

## 2. HTTP Header Analysis

Headers leak stack details.

Interesting headers:

|Header|Reveals|
|---|---|
|`Server`|Web server|
|`X-Powered-By`|Backend language/framework|
|`X-Redirect-By`|CMS/plugin behavior|
|`Set-Cookie`|Framework/session engine|

---

## 3. Response Probing

Special requests trigger unique responses.

Examples:

```bash
OPTIONS /
TRACE /
HEAD /
```

Useful for:

- Method enumeration
    
- WAF behavior
    
- Framework detection
    

---

## 4. Content Analysis

Inspect source code.

Look for:

```bash
wp-content
Drupal.settings
__NEXT_DATA__
Laravel Mix
```

Often reveals:

- CMS
    
- JS frameworks
    
- Libraries
    
- Plugins
    

---

# 🛠️ Key Fingerprinting Tools

|Tool|Use|
|---|---|
|Wappalyzer|Browser fingerprinting|
|WhatWeb|CLI tech detection|
|Nikto|Software identification|
|wafw00f|Detect WAFs|
|Nmap|Service/version detection|
|BuiltWith|Tech stack analysis|

---

# 🧠 Banner Grabbing Cheat Sheet

## Basic Headers

```bash
curl -I target.com
```

---

## Follow Redirects

```bash
curl -IL target.com
```

Useful because tech often appears after redirects.

---

## Grab Full Response

```bash
curl -v target.com
```

---

## TLS Certificate Info

```bash
openssl s_client -connect target.com:443
```

Shows:

- Certificate issuer
    
- SANs
    
- TLS versions
    

---

# 🧠 Detect WAF

Using wafw00f

```bash
wafw00f target.com
```

Example output:

```bash
Cloudflare
Wordfence
AWS WAF
Akamai
Imperva
```

This tells you what defensive layer you're dealing with.

---

# 🧠 Website Technology Detection

Using WhatWeb

## Basic Scan

```bash
whatweb target.com
```

---

## Aggressive Detection

```bash
whatweb -a 3 target.com
```

---

## Verbose

```bash
whatweb -v target.com
```

Finds:

- CMS
    
- Frameworks
    
- JS libraries
    
- Analytics
    
- Server software
    

---

# 🧠 Nikto Fingerprinting

Software-only detection:

```bash
nikto -h target.com -Tuning b
```

Useful for finding:

- Outdated software
    
- Security headers
    
- CMS fingerprints
    
- Info leaks
    

---

# 🧠 Nmap Fingerprinting

Service detection:

```bash
nmap -sV target.com
```

---

OS detection:

```bash
nmap -O target.com
```

---

HTTP fingerprinting scripts:

```bash
nmap --script http-enum,http-title -p80,443 target.com
```

---

# ⚡ Practical Recon Workflow

## 1. Check headers

```bash
curl -IL target.com
```

---

## 2. Detect stack

```bash
whatweb target.com
```

---

## 3. Identify WAF

```bash
wafw00f target.com
```

---

## 4. Software fingerprint

```bash
nikto -h target.com -Tuning b
```

---

## 5. Enumerate content

```bash
feroxbuster -u https://target.com -r
```

---

# ⚠️ Things to Watch For

High-value findings:

|Discovery|Why It Matters|
|---|---|
|Old Apache/Nginx|Known CVEs|
|WordPress|Plugin/theme vulns|
|Exposed admin panels|Auth attack surface|
|Missing HSTS|SSL downgrade risk|
|Verbose errors|Info disclosure|

---

# 🧠 Recon Clues to Notice

Big tells:

```bash
wp-json
/wp-admin
/phpmyadmin
Server: Apache
X-Powered-By: PHP
```

These often point directly to attack paths.

---

# 🧠 Key Takeaways

> [!summary]
> 
> - Fingerprinting reveals a target’s tech stack
>     
> - Helps map likely vulnerabilities
>     
> - Guides exploit selection
>     
> - Identifies WAFs and defenses
>     
> - Should always precede exploitation
>     

---

