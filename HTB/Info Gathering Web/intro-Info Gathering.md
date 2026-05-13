# 🕵️ Web Reconnaissance

---

> [!info] 📌 Overview  
> Web Reconnaissance is the **information gathering phase** of web penetration testing.
> 
> Its purpose is to collect intelligence about a target before attempting exploitation.
> 
> Think of it as:
> 
> **"Mapping the battlefield before attacking."**

It helps identify:

- Assets
    
- Technologies
    
- Hidden resources
    
- Weaknesses
    
- Potential attack paths
    

---

# 🧠 Where It Fits in Pentesting

### Typical Penetration Testing Flow

1. **Pre-Engagement**
    
2. **Information Gathering (Recon)**
    
3. **Vulnerability Assessment**
    
4. **Exploitation**
    
5. **Post-Exploitation**
    
6. **Lateral Movement**
    
7. **Proof of Concept**
    
8. **Post-Engagement Reporting**
    

> [!tip]  
> Recon is the foundation of everything that follows.

---

# 🎯 Goals of Web Recon

|Goal|Purpose|
|---|---|
|**Identify Assets**|Discover domains, subdomains, IPs, technologies|
|**Find Hidden Information**|Locate backups, configs, exposed files|
|**Map Attack Surface**|Understand exposed entry points|
|**Gather Intelligence**|Collect data useful for exploitation/social engineering|

---

# 🌐 Why Recon Matters

### For Attackers

Recon helps attackers:

- Find weak points
    
- Tailor attacks
    
- Bypass defenses
    
- Increase success rate
    

---

### For Defenders

Recon helps defenders:

- Discover exposed assets
    
- Detect misconfigurations
    
- Patch vulnerabilities early
    

---

# 🧠 Types of Reconnaissance

Web recon has **two core approaches**:

1. **Active Recon**
    
2. **Passive Recon**
    

---

# 🔥 Active Reconnaissance

> [!warning]  
> Directly interacts with the target.

This generates traffic and may trigger:

- IDS alerts
    
- Firewall logs
    
- Security monitoring
    

---

## Common Active Recon Techniques

|Technique|Purpose|Example Tools|Detection Risk|
|---|---|---|---|
|**Port Scanning**|Find open ports/services|Nmap, Masscan|High|
|**Vulnerability Scanning**|Detect known flaws|Nessus, OpenVAS, Nikto|High|
|**Network Mapping**|Discover infrastructure|Traceroute, Nmap|Medium–High|
|**Banner Grabbing**|Identify software versions|Netcat, curl|Low|
|**OS Fingerprinting**|Identify operating system|Nmap `-O`|Low|
|**Service Enumeration**|Detect service versions|Nmap `-sV`|Low|
|**Web Spidering**|Crawl site structure|Burp Spider, OWASP ZAP|Low–Medium|

---

## Example Commands

### Port Scan

```bash
nmap target.com
```

---

### Service Enumeration

```bash
nmap -sV target.com
```

---

### OS Detection

```bash
nmap -O target.com
```

---

### Banner Grabbing

```bash
curl -I target.com
```

---

# 👻 Passive Reconnaissance

> [!info]  
> Collects information **without directly touching the target.**

Safer and stealthier.

---

## Common Passive Recon Techniques

|Technique|Purpose|Tools|Detection Risk|
|---|---|---|---|
|**Search Engines**|Public info discovery|Google, Bing, Shodan|Very Low|
|**WHOIS Lookup**|Domain ownership details|whois|Very Low|
|**DNS Analysis**|Subdomains / infrastructure|dig, dnsrecon|Very Low|
|**Web Archives**|Historical site data|Wayback Machine|Very Low|
|**Social Media Analysis**|Employee intel|LinkedIn, X|Very Low|
|**Code Repository Search**|Find leaks/exposed code|GitHub, GitLab|Very Low|

---

# 🧠 Passive Recon Examples

## WHOIS Lookup

```bash
whois target.com
```

---

## DNS Enumeration

```bash
dig target.com
```

---

## Search for Public Files

```bash
site:target.com filetype:pdf
```

---

## Search GitHub for Secrets

```bash
target.com password
```

(Search manually on GitHub)

---

# ⚖️ Active vs Passive Recon

|Feature|Active Recon|Passive Recon|
|---|---|---|
|Target Interaction|Yes|No|
|Detection Risk|Higher|Lower|
|Data Depth|More detailed|Limited to public info|
|Speed|Faster discovery|Slower|

---

# 🛠️ Key Recon Tools

|Tool|Use|
|---|---|
|**Nmap**|Port/service scanning|
|**Masscan**|High-speed scanning|
|**Burp Suite**|Web crawling|
|**OWASP ZAP**|Spidering / scanning|
|**whois**|Domain lookup|
|**dig / nslookup**|DNS recon|
|**Shodan**|Internet-facing asset discovery|
|**Wayback Machine**|Historical analysis|
|**GitHub**|Code leak discovery|

---

# ⚠️ Common Findings During Recon

Recon often reveals:

- Forgotten subdomains
    
- Backup files (`.bak`, `.zip`)
    
- Exposed admin panels
    
- API endpoints
    
- Tech stack versions
    
- Internal documentation
    
- Misconfigured services
    

---

# 🎯 Recon Output You Want

A strong recon phase should answer:

### Infrastructure

- What servers exist?
    
- Which ports are open?
    
- What technologies are running?
    

---

### Web Surface

- What pages/directories exist?
    
- Hidden endpoints?
    
- APIs?
    

---

### Exposure

- Any leaked files?
    
- Misconfigurations?
    
- Old vulnerable software?
    

---

# 🧠 Why WHOIS Matters

WHOIS gives valuable metadata like:

- Domain owner
    
- Registration dates
    
- Name servers
    
- Registrar details
    

This often helps:

- Infrastructure mapping
    
- Pivoting to related assets
    
- Discovering subdomains
    

---

# 🧠 Key Takeaways

> [!summary]
> 
> - Web Recon is the **foundation of web pentesting**
>     
> - It maps the target before exploitation
>     
> - Recon is split into:
>     
>     - **Active Recon** → direct interaction
>         
>     - **Passive Recon** → public intelligence gathering
>         
> - Active recon is deeper but noisier
>     
> - Passive recon is stealthier but limited
>     
> - Strong recon reveals attack paths early
>     

---

## Golden Rule

> **The better your reconnaissance, the easier exploitation becomes.**