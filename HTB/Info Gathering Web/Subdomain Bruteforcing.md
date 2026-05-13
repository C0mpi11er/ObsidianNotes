
# 🧠 Subdomain Bruteforcing

---

> [!info] 📌 Overview  
> Subdomain bruteforcing is an **active enumeration technique** used to discover hidden subdomains.
> 
> It works by testing possible subdomain names against a target domain.

Example:

If the target is:

```text
example.com
```

Bruteforcing tests names like:

```text
dev.example.com
admin.example.com
mail.example.com
staging.example.com
```

If they resolve, they exist.

---

# 🎯 Why It Matters

Many valuable systems are hidden behind subdomains.

Bruteforcing helps uncover:

- Development environments
    
- Admin panels
    
- APIs
    
- Internal portals
    
- Legacy services
    

These are often more vulnerable than the main site.

---

# ⚙️ How It Works

The process follows four stages.

---

# 1️⃣ Wordlist Selection

Success depends heavily on the wordlist.

A bad wordlist = poor discovery.

---

## General-Purpose Wordlists

Contain common subdomains.

Examples:

```text
admin
dev
test
mail
vpn
api
blog
staging
```

Best when:

You know nothing about the target.

---

## Targeted Wordlists

Built for specific industries or technologies.

Examples:

For cloud-heavy orgs:

```text
aws
cdn
lambda
s3
portal
```

Best when:

You know the target's stack.

---

## Custom Wordlists

Built from intelligence gathered during recon.

Sources:

- WHOIS
    
- DNS records
    
- Employee names
    
- Internal naming patterns
    
- GitHub repos
    

Example:

If you discover:

```text
corp.example.com
```

You might test:

```text
hr.example.com
finance.example.com
vpn.example.com
```

---

# 2️⃣ Iteration & Querying

The tool appends each word to the main domain.

Example:

Wordlist:

```text
admin
dev
test
```

Target:

```text
example.com
```

Generated queries:

```text
admin.example.com
dev.example.com
test.example.com
```

---

# 3️⃣ DNS Lookup

Each generated subdomain is queried.

Usually via:

- **A records**
    
- **AAAA records**
    

Example:

```bash
dig dev.example.com
```

If an IP is returned, it exists.

---

# 4️⃣ Filtering & Validation

Not every response is useful.

Validation confirms:

- It resolves
    
- It's live
    
- It's serving content
    

Example:

```bash
curl http://dev.example.com
```

---

# 🛠️ Popular Bruteforcing Tools

|Tool|Purpose|
|---|---|
|**dnsenum**|Full DNS enumeration|
|**fierce**|Recursive subdomain discovery|
|**dnsrecon**|Advanced DNS recon|
|**OWASP Amass**|Comprehensive discovery|
|**assetfinder**|Lightweight passive/active|
|**puredns**|Fast brute-force resolution|
|**ffuf**|Flexible fuzzing|
|**gobuster**|DNS brute-forcing|

---

# 🔥 DNSEnum

> [!info]  
> `dnsenum` is one of the most widely used DNS enumeration tools.

It supports:

- DNS record enumeration
    
- Zone transfer attempts
    
- Subdomain bruteforcing
    
- Reverse lookups
    
- WHOIS lookups
    
- Search engine scraping
    

---

# Key Features of DNSEnum

|Feature|Purpose|
|---|---|
|**Record Enumeration**|Retrieves A, MX, NS, TXT|
|**Zone Transfer Testing**|Checks AXFR misconfigs|
|**Bruteforcing**|Tests subdomain wordlists|
|**Recursive Discovery**|Finds deeper subdomains|
|**WHOIS Integration**|Ownership metadata|
|**Reverse Lookups**|Maps IPs to domains|

---

# Basic Usage

## Standard Enumeration

```bash
dnsenum example.com
```

Performs basic recon.

---

## Brute-Force With Wordlist

```bash
dnsenum --enum example.com -f wordlist.txt
```

---

## Recursive Bruteforce

```bash
dnsenum --enum example.com -f wordlist.txt -r
```

This means:

If it finds:

```text
dev.example.com
```

It will test:

```text
api.dev.example.com
admin.dev.example.com
```

Very powerful.

---

# Real-World Example

Using **SecLists**

```bash
dnsenum --enum example.com \
-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
-r
```

---

# Command Breakdown

### Target Domain

```bash
example.com
```

Domain being tested.

---

### `--enum`

Enables enumeration mode.

Performs:

- DNS lookups
    
- NS discovery
    
- MX discovery
    
- Subdomain enumeration
    

---

### `-f`

Specifies wordlist file.

Example:

```bash
-f wordlist.txt
```

---

### `-r`

Recursive enumeration.

Searches deeper levels.

---

# Example Output

```text
www.example.com
support.example.com
mail.example.com
dev.example.com
```

These become investigation targets.

---

# What to Do After Discovery

Finding a subdomain is only step one.

Next:

---

## Resolve It

```bash
dig dev.example.com
```

---

## Check Web Access

```bash
curl -I http://dev.example.com
```

---

## Fingerprint Technology

Use:

- **Wappalyzer**
    
- Burp Suite
    
- Manual inspection
    

---

## Look for Weaknesses

Common findings:

- Debug panels
    
- Exposed APIs
    
- Default credentials
    
- Old frameworks
    

---

# ⚠️ Common Bruteforcing Challenges

---

## Wildcard DNS

Some domains resolve every subdomain.

Example:

```text
anything.example.com
random.example.com
fake.example.com
```

All return an IP.

This causes false positives.

Tools like:

- `dnsrecon`
    
- `puredns`
    
- `fierce`
    

can detect this.

---

## Rate Limiting

Too many queries may trigger:

- Blocks
    
- Delays
    
- Monitoring alerts
    

---

## Large Wordlists

Huge lists improve coverage but increase:

- Noise
    
- Scan time
    

Balance matters.

---

# 🎯 Best Practices

## Start Passive

Use:

- CT logs
    
- Search engines
    
- DNS history
    

Then brute-force.

---

## Use Targeted Wordlists

Much more efficient.

---

## Validate Results

Always confirm discovered hosts.

---

## Investigate Naming Patterns

If you find:

```text
dev-us.example.com
```

Try:

```text
dev-eu.example.com
dev-uk.example.com
```

Patterns reveal more assets.

---

# Recon Workflow

### Step 1

Passive discovery

---

### Step 2

Bruteforce

```bash
dnsenum --enum example.com -f wordlist.txt
```

---

### Step 3

Validate

---

### Step 4

Fingerprint

---

### Step 5

Prioritize

---

# 🧠 Key Takeaways

> [!summary]
> 
> - Subdomain bruteforcing actively discovers hidden hosts
>     
> - It works by testing candidate names against DNS
>     
> - Wordlist quality is critical
>     
> - `dnsenum` is a powerful enumeration tool
>     
> - Recursive bruteforcing reveals deeper infrastructure
>     
> - Always validate results to avoid false positives
>     

---

## Golden Rule

> **The right wordlist can uncover what the main website never intended you to see.**