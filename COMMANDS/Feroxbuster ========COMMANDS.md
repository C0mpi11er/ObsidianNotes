
---

# 🦊 Feroxbuster Command Cheat Sheet

> Fast web content discovery with **Feroxbuster**

Best for:

- Directory brute-forcing
    
- Recursive enumeration
    
- Hidden file discovery
    
- API endpoint discovery
    

---

# ⚡ Basic Syntax

```bash
feroxbuster -u http://target.com
```

---

# 🔍 Core Commands

## Basic Scan

```bash
feroxbuster -u http://target.com
```

---

## Custom Wordlist

```bash
feroxbuster -u http://target.com -w wordlist.txt
```

---

## Recursive Scan

```bash
feroxbuster -u http://target.com -r
```

---

## Limit Recursion Depth

```bash
feroxbuster -u http://target.com -r -d 2
```

---

## Save Results

```bash
feroxbuster -u http://target.com -o results.txt
```

---

# 🚀 Speed & Performance

## More Threads

```bash
feroxbuster -u http://target.com -t 50
```

---

## High-Speed Aggressive

```bash
feroxbuster -u http://target.com -t 100 -r
```

Use carefully — this can annoy rate limiters fast.

---

## Rate Limited / Stealth

```bash
feroxbuster -u http://target.com --rate-limit 5
```

---

# 📂 Extension Hunting

## PHP / TXT / HTML

```bash
feroxbuster -u http://target.com -x php,txt,html
```

---

## API Recon

```bash
feroxbuster -u http://target.com -x json
```

---

## Backup File Hunting

```bash
feroxbuster -u http://target.com -x bak,old,zip,tar.gz
```

---

# 🎯 Filtering

## Filter Status Codes

```bash
feroxbuster -u http://target.com -C 404,403
```

---

## Show Only Interesting Codes

```bash
feroxbuster -u http://target.com -s 200 204 301 302
```

---

## Filter Response Size

```bash
feroxbuster -u http://target.com --filter-size 1234
```

Useful against wildcard responses.

---

# 🔒 HTTPS / Cert Issues

## Ignore TLS Errors

```bash
feroxbuster -u https://target.com -k
```

---

# 🕵️ Proxying

## Through **Burp Suite**

```bash
feroxbuster -u http://target.com -p http://127.0.0.1:8080
```

---

## SOCKS Proxy

```bash
feroxbuster -u http://target.com -p socks5://127.0.0.1:9050
```

---

# 🧠 Header Manipulation

## Custom User-Agent

```bash
feroxbuster -u http://target.com -H "User-Agent: Googlebot"
```

---

## Host Header Testing

```bash
feroxbuster -u http://TARGET_IP -H "Host: dev.example.com"
```

Useful for VHost enumeration.

---

## Internal Header Testing

```bash
feroxbuster -u http://target.com -H "X-Forwarded-For: 127.0.0.1"
```

---

# 📊 Output Formats

## JSON

```bash
feroxbuster -u http://target.com --json -o results.json
```

---

# 🔥 Real-World Presets

## Fast CTF Scan

```bash
feroxbuster -u http://target.com -t 50 -r
```

---

## Bug Bounty Safe Scan

```bash
feroxbuster -u http://target.com -t 15 --rate-limit 5 -r
```

---

## Burp-Assisted Recon

```bash
feroxbuster -u http://target.com -p http://127.0.0.1:8080 -r
```

---

## Backup File Hunt

```bash
feroxbuster -u http://target.com -x bak,zip,tar,old
```

---

## API Endpoint Hunt

```bash
feroxbuster -u http://target.com -x json -r
```

---

# ⚔️ Quick Comparison

|Tool|Best For|
|---|---|
|**Feroxbuster**|Recursive deep scans|
|**Gobuster**|Fast lightweight brute-force|
|**ffuf**|Precision fuzzing|
|**Dirsearch**|Simplicity|

---

# 🛠 Best Wordlists

From **SecLists**

Small:

```text
Discovery/Web-Content/common.txt
```

Medium:

```text
Discovery/Web-Content/directory-list-2.3-medium.txt
```

Large:

```text
Discovery/Web-Content/raft-large-directories.txt
```

---

# 🧠 Field Workflow

### 1

```bash
feroxbuster -u http://target.com
```

### 2

```bash
feroxbuster -u http://target.com -r
```

### 3

```bash
feroxbuster -u http://target.com -x php,json,bak
```

### 4

Proxy through Burp and inspect manually.

---

# 🎯 Most Used Commands

```bash
feroxbuster -u http://target.com -r
```

```bash
feroxbuster -u http://target.com -x php,txt
```

```bash
feroxbuster -u http://target.com -p http://127.0.0.1:8080
```

```bash
feroxbuster -u http://target.com --rate-limit 5
```

---

# Key Rule

Start quiet.

Escalate only if the target tolerates it.

People love launching `-t 100 -r` immediately and then act surprised when the WAF says “absolutely not.”