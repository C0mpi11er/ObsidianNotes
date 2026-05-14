

---

# 🕷️ Web Crawling Cheat Sheet

---

> [!ABSTRACT] 🧠 What is Web Crawling?
> 
> Web crawling (spidering) is the automated process of following links across a website to discover:
> 
> - Hidden pages
>     
> - API endpoints
>     
> - Parameters
>     
> - JavaScript files
>     
> - Internal routes
>     
> - Sensitive files
>     
> 
> **Difference from fuzzing:**
> 
> - **Crawling** → Finds existing linked resources
>     
> - **Fuzzing** → Guesses undiscovered resources
>     

---

## 🔧 Core Crawling Tools

> [!INFO]
> 
> Main tools for modern web recon:
> 
> - katana
>     
> - hakrawler
>     
> - gospider
>     
> - burp suite Spider
>     
> - OWASP ZAP
>     

---

# ⚡ Katana (Best Modern Crawler)

> [!TLDR]
> 
> Fastest + cleanest crawler for recon pipelines.

---

## Basic Crawl

> [!EXAMPLE]

```bash
katana -u https://target.com
```

---

## Silent Output

> [!EXAMPLE]

```bash
katana -u https://target.com -silent
```

---

## Save Results

> [!EXAMPLE]

```bash
katana -u https://target.com -o crawl.txt
```

---

## Deep Crawl

> [!EXAMPLE]

```bash
katana -u https://target.com -d 5
```

> [!NOTE]
> 
> Higher depth = slower scan but more coverage

---

## Crawl JavaScript

> [!EXAMPLE]

```bash
katana -u https://target.com -jc
```

> [!SUCCESS]
> 
> Useful for finding:
> 
> - hidden API routes
>     
> - undocumented endpoints
>     
> - internal references
>     

---

## Crawl Subdomains

> [!EXAMPLE]

```bash
katana -u https://target.com -cs
```

---

## Proxy Through Burp

> [!EXAMPLE]

```bash
katana -u https://target.com -proxy http://127.0.0.1:8080
```

> [!CHECK]
> 
> Verify traffic is reaching Burp before starting scan.

---

## Extract Forms & Params

> [!EXAMPLE]

```bash
katana -u https://target.com -fx
```

> [!ATTENTION]
> 
> Very useful before:
> 
> - XSS testing
>     
> - IDOR checks
>     
> - SQLi probing
>     

---

## Search Interesting Extensions

> [!EXAMPLE]

```bash
katana -u https://target.com -em php,json,bak,zip,sql
```

---

# ⚡ Hakrawler

> [!ABSTRACT]
> 
> Lightweight crawler for quick recon.

---

## Basic

> [!EXAMPLE]

```bash
echo https://target.com | hakrawler
```

---

## Depth Crawl

> [!EXAMPLE]

```bash
echo https://target.com | hakrawler -d 3
```

---

## Include Subdomains

> [!EXAMPLE]

```bash
echo https://target.com | hakrawler -subs
```

---

## Save Output

> [!EXAMPLE]

```bash
echo https://target.com | hakrawler > crawl.txt
```

---

# ⚡ Gospider

> [!ABSTRACT]
> 
> Aggressive crawler for broader discovery.

---

## Basic Crawl

> [!EXAMPLE]

```bash
gospider -s https://target.com
```

---

## Crawl Subdomains

> [!EXAMPLE]

```bash
gospider -s https://target.com --subs
```

---

## Save Output

> [!EXAMPLE]

```bash
gospider -s https://target.com -o output/
```

---

## Multi-threaded Crawl

> [!EXAMPLE]

```bash
gospider -s https://target.com -t 50
```

---

# 🔥 Recon Pipelines

---

## Full Modern Recon Chain

> [!EXAMPLE]

```bash
subfinder -d target.com | httpx | katana -jc -silent
```

Uses:

- subfinder
    
- httpx
    
- katana
    

---

## Crawl → Fuzz

> [!EXAMPLE]

```bash
katana -u https://target.com | ffuf -w wordlist.txt -u FUZZ
```

---

## JS Endpoint Recon

> [!EXAMPLE]

```bash
katana -u https://target.com -jc | grep api
```

---

# 🧪 What Good Crawling Finds

> [!SUCCESS]

```text
/admin
/debug
/graphql
/swagger
/api/v1/
/internal
/config.json
/backup.zip
```

---

# ⚠️ Common Mistakes

> [!WARNING]
> 
> Don’t confuse crawling with fuzzing.

Bad assumption:

> “Crawler didn’t find /admin, so it doesn’t exist.”

Wrong.

Crawler only finds **linked resources**.

Use fuzzing after crawling.

---

> [!CAUTION]
> 
> Some sites detect aggressive crawling.

Reduce noise:

```bash
katana -u https://target.com -rl 2
```

---

# ⚔️ Tool Comparison

|Tool|Best Use|
|---|---|
|**Katana**|Full recon|
|**Hakrawler**|Quick checks|
|**Gospider**|Aggressive crawl|
|**Feroxbuster**|Recursive content brute force|
|**FFUF**|Endpoint fuzzing|

---

# 🛠 Pentester Workflow

> [!DONE]

### 1. Find Subdomains

```bash
amass enum -passive -d target.com
```

### 2. Probe Live Hosts

```bash
cat subs.txt | httpx
```

### 3. Crawl

```bash
cat alive.txt | katana -jc
```

### 4. Fuzz Discovered Paths

```bash
ffuf -u https://target.com/FUZZ -w wordlist.txt
```

### 5. Manual Burp Testing

---

> [!TLDR]
> 
> **Best crawler today:** Katana  
> **Best workflow:**
> 
> ```bash
> subfinder → httpx → katana → ffuf → burp
> ```