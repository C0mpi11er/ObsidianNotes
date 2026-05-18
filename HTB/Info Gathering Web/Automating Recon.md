

# 🤖 Automated Web Recon Cheat Sheet

---

> [!ABSTRACT] 🧠 What is Automated Recon?
> 
> ```bash
> Using tools to automate information gathering against web targets.
> ```
> 
> Instead of manually checking DNS, headers, subdomains, crawling, and ports one by one, recon frameworks chain them together.

---

# 🎯 Why Automate Recon?

> [!SUCCESS] Benefits

- Faster enumeration
    
- Less manual repetition
    
- Better coverage
    
- Consistent output
    
- Easier scaling across multiple targets
    

---

> [!WARNING]
> 
> Automation is fast, but noisy.
> 
> Running aggressive recon against production infra can trigger:
> 
> - WAF alerts
>     
> - Rate limits
>     
> - Temporary IP bans
>     
> - SOC investigation
>     

---

# 🛠 Popular Recon Frameworks

## 🔹 FinalRecon

> [!INFO]  
> Python-based all-in-one web recon toolkit

### Best For

- Quick target profiling
    
- HTB / Labs
    
- Fast recon snapshots
    

### Modules

|Module|Purpose|
|---|---|
|Headers|Server fingerprinting|
|SSL|Cert inspection|
|Whois|Domain intel|
|Crawl|Page/resource discovery|
|DNS|Record enumeration|
|Subdomains|Find subs|
|Dir|Directory brute force|
|Wayback|Historical URLs|
|Port Scan|Quick service discovery|

---

## 🔹 Recon-ng

> [!INFO]  
> Modular OSINT framework

Best for:

- Structured recon workflow
    
- API integrations
    
- Large target profiling
    

---

## 🔹 theHarvester

Best for:

- Emails
    
- Employee names
    
- Subdomains
    
- Public source intel
    

---

## 🔹 SpiderFoot

Best for:

- Passive recon
    
- Attack surface mapping
    
- Visualization
    

---

# ⚡ Installing FinalRecon

---

> [!EXAMPLE]

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
pip3 install -r requirements.txt
chmod +x finalrecon.py
```

---

## Verify Install

```bash
./finalrecon.py --help
```

---

> [!CHECK]  
> If help menu appears → installation successful

---

# 🚀 Core Commands

---

## Basic Scan

```bash
./finalrecon.py --url http://target.com
```

---

## Header Analysis

```bash
./finalrecon.py --headers --url http://target.com
```

Finds:

- Server type
    
- Framework clues
    
- Security headers
    
- Redirect behavior
    

---

## SSL Certificate Info

```bash
./finalrecon.py --sslinfo --url https://target.com
```

Useful for:

- Cert validity
    
- SAN entries
    
- Hidden subdomains
    

---

## Whois Lookup

```bash
./finalrecon.py --whois --url target.com
```

Finds:

- Registrar
    
- Name servers
    
- Creation date
    
- Expiry
    

---

## DNS Enumeration

```bash
./finalrecon.py --dns --url target.com
```

---

## Subdomain Enumeration

```bash
./finalrecon.py --sub --url target.com
```

Pulls from:

- crt.sh
    
- ThreatMiner
    
- CertSpotter
    
- VirusTotal
    
- Shodan (API)
    

---

## Crawl Website

```bash
./finalrecon.py --crawl --url http://target.com
```

Extracts:

- Links
    
- JS files
    
- Forms
    
- Images
    
- robots.txt
    
- sitemap.xml
    

---

## Directory Bruteforce

```bash
./finalrecon.py --dir --url http://target.com
```

Custom wordlist:

```bash
./finalrecon.py --dir -w /usr/share/seclists/Discovery/Web-Content/common.txt --url http://target.com
```

---

## Wayback Enumeration

```bash
./finalrecon.py --wayback --url target.com
```

Finds:

- Archived endpoints
    
- Deleted pages
    
- Old admin paths
    

---

## Port Scan

```bash
./finalrecon.py --ps --url target.com
```

---

# ☠ Full Recon Scan

---

> [!DANGER]  
> Can generate lots of requests.

```bash
./finalrecon.py --full --url target.com
```

Runs:

- Headers
    
- Whois
    
- Crawl
    
- DNS
    
- Subdomains
    
- Ports
    
- Directory enum
    
- Wayback
    

---

# Useful Flags

|Flag|Function|
|---|---|
|`-T`|Timeout|
|`-dt`|Dir threads|
|`-pt`|Port threads|
|`-w`|Wordlist|
|`-r`|Follow redirects|
|`-s`|SSL verify|
|`-e`|Extensions|
|`-o`|Export format|

---

## Example

### PHP File Hunt

```bash
./finalrecon.py --dir -e php --url target.com
```

---

### Aggressive Recon

```bash
./finalrecon.py --full -dt 80 -pt 100 --url target.com
```

---

### Stealthier Scan

```bash
./finalrecon.py --headers --dns --whois --url target.com
```

---

# Real Workflow

> [!TLDR]

### Phase 1 — Passive

```bash
./finalrecon.py --headers --whois --sslinfo --url target.com
```

---

### Phase 2 — Discovery

```bash
./finalrecon.py --sub --dns --crawl --url target.com
```

---

### Phase 3 — Attack Surface

```bash
./finalrecon.py --dir --ps --wayback --url target.com
```

---

# Pro Tips

> [!SUCCESS]

✅ Start passive first  
✅ Review output manually  
✅ Investigate JS files  
✅ Check archived endpoints  
✅ Verify subdomains manually

---

> [!FAILURE]  
> Common mistake:

Running:

```bash
./finalrecon.py --full
```

Without rate awareness and getting blocked immediately.

---

# FinalRecon vs Other Tools

|Tool|Best Use|
|---|---|
|FinalRecon|Fast all-in-one recon|
|Feroxbuster|Directory brute force|
|Amass|Deep subdomain enum|
|Gobuster|VHost + dirs|
|theHarvester|OSINT|
|SpiderFoot|Automated passive intel|

---

> [!DONE]  
> Use FinalRecon when you want:

**“Give me everything quickly.”**

Use specialized tools when you need:

**“Go deeper into one recon vector.”**