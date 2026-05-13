# 🧠 Subdomains

---

> [!info] 📌 Overview  
> A **subdomain** is an extension of a main domain.
> 
> It helps separate services, applications, or environments.

Example:

```text
blog.example.com
mail.example.com
dev.example.com
```

Structure:

```text
[subdomain].[domain].[TLD]
```

Example:

```text
api.example.com
```

Where:

- **api** → subdomain
    
- **example** → domain
    
- **.com** → TLD
    

---

# 🌐 Why Subdomains Matter

Subdomains often expose hidden parts of an organization's infrastructure.

They frequently host:

- Internal services
    
- Testing environments
    
- Admin portals
    
- Legacy systems
    

This makes them high-value recon targets.

---

# 🎯 Why They Matter in Web Recon

Subdomains can reveal systems not visible on the main site.

These are often less monitored and less secure.

---

## 🧪 Development & Staging Environments

Examples:

```text
dev.example.com
staging.example.com
test.example.com
```

These may expose:

- Debug pages
    
- Source code
    
- Misconfigurations
    
- Test credentials
    

---

## 🔐 Hidden Login Portals

Examples:

```text
admin.example.com
portal.example.com
vpn.example.com
```

Potential targets for:

- Authentication attacks
    
- Access control testing
    
- Weak credential discovery
    

---

## 🏚️ Legacy Applications

Old forgotten systems often remain accessible.

Examples:

```text
old.example.com
legacy.example.com
backup.example.com
```

Common risks:

- Outdated software
    
- Unpatched CVEs
    
- Weak configurations
    

---

## 📂 Sensitive Information Exposure

Subdomains may expose:

- Internal dashboards
    
- Backups
    
- Config files
    
- Documentation
    

Example:

```text
files.example.com
docs.example.com
internal.example.com
```

---

# 🔍 What is Subdomain Enumeration?

Subdomain enumeration is the process of discovering subdomains tied to a target domain.

Goal:

Build a larger attack surface map.

Example target:

```text
example.com
```

Enumeration may reveal:

```text
api.example.com
mail.example.com
vpn.example.com
dev.example.com
shop.example.com
```

---

# 🧠 Types of Enumeration

There are **two primary approaches**:

1. Active Enumeration
    
2. Passive Enumeration
    

---

# 🔥 Active Subdomain Enumeration

> [!warning]  
> Directly interacts with target DNS infrastructure.

Higher visibility.

Potentially detectable.

---

## Method 1: DNS Zone Transfer

Attempts to retrieve the entire DNS zone.

Command:

```bash
dig axfr example.com @ns1.example.com
```

If successful, it may reveal:

- All subdomains
    
- Internal hosts
    
- Infrastructure layout
    

---

### Example Output

```text
dev.example.com
vpn.example.com
mail.example.com
db.example.com
```

---

> [!warning]  
> Rarely works today due to secure configurations.

Still always worth testing.

---

## Method 2: Brute Force Enumeration

Tests possible subdomain names against DNS.

Example wordlist:

```text
admin
dev
test
mail
vpn
api
staging
```

---

### Using `ffuf`

```bash
ffuf -u http://FUZZ.example.com -w subdomains.txt
```

---

### Using `gobuster`

```bash
gobuster dns -d example.com -w subdomains.txt
```

---

### Using `dnsenum`

```bash
dnsenum example.com
```

---

# 👻 Passive Subdomain Enumeration

> [!info]  
> No direct interaction with target DNS servers.

Stealthier.

Often preferred during early recon.

---

## Method 1: Certificate Transparency Logs

SSL/TLS certificates often list subdomains.

These are publicly logged.

Useful sources include:

- **crt.sh**
    
- **Censys**
    

Search example:

```text
%.example.com
```

May reveal:

- Forgotten hosts
    
- Historical subdomains
    
- Internal naming patterns
    

---

## Method 2: Search Engines

Use search operators.

### Google Dork

```text
site:*.example.com
```

This may uncover indexed subdomains.

---

## Method 3: Online Enumeration Platforms

Useful tools:

|Tool|Purpose|
|---|---|
|**SecurityTrails**|Historical DNS|
|**VirusTotal**|Domain relationships|
|**Shodan**|Service discovery|
|**Amass**|Enumeration|

---

# ⚖️ Active vs Passive Enumeration

|Feature|Active|Passive|
|---|---|---|
|Detection Risk|Higher|Lower|
|Completeness|More thorough|Sometimes incomplete|
|Speed|Faster results|Depends on sources|
|Stealth|Lower|High|

---

# 🛠️ Common Enumeration Tools

|Tool|Best For|
|---|---|
|**dig**|Manual DNS queries|
|**dnsenum**|Automated brute-force|
|**dnsrecon**|Full DNS recon|
|**ffuf**|Fast fuzzing|
|**gobuster**|DNS brute-force|
|**Amass**|Advanced passive + active|
|**theHarvester**|OSINT discovery|

---

# 🎯 Valuable Subdomains to Prioritize

During recon, prioritize:

|Subdomain Pattern|Why Important|
|---|---|
|**dev**|Test environments|
|**staging**|Pre-production apps|
|**admin**|Management portals|
|**vpn**|Remote access|
|**mail**|Email infrastructure|
|**api**|Backend endpoints|
|**old / legacy**|Outdated software|

---

# 🚨 Common Security Risks

Poorly secured subdomains often lead to:

- Subdomain takeover
    
- Exposed admin panels
    
- Weak authentication
    
- Debug interfaces
    
- Information disclosure
    

---

# 🧠 Recon Workflow

A practical approach:

### Step 1

Passive discovery

---

### Step 2

Validate findings

```bash
dig sub.example.com
```

---

### Step 3

Brute-force missing entries

---

### Step 4

Investigate live hosts

---

### Step 5

Prioritize high-value targets

---

# 🧠 Key Takeaways

> [!summary]
> 
> - Subdomains extend a main domain
>     
> - They often reveal hidden infrastructure
>     
> - Enumeration expands your attack surface map
>     
> - Two methods:
>     
>     - **Active** → direct querying
>         
>     - **Passive** → external sources
>         
> - Combining both gives the best coverage
>     
> - Subdomains frequently expose vulnerable systems
>     

---

## Golden Rule

> **The main domain shows the front door — subdomains reveal the side entrances.**