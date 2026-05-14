
# 🧠 Certificate Transparency (CT Logs)

---

> [!info] 📌 Overview  
> **Certificate Transparency (CT)** is a public logging system for issued **SSL/TLS certificates**.

It helps detect:

- Rogue certificates
    
- Mis-issued certificates
    
- Unauthorized subdomains
    
- Historical certificate activity
    

Think of it as a **public certificate ledger** for the internet.

---

# 🌐 Why CT Logs Matter

When a Certificate Authority issues a cert, it must submit it to public CT logs.

This provides:

|Benefit|Purpose|
|---|---|
|**Transparency**|Public visibility into certificate issuance|
|**Detection**|Spot fake or unauthorized certs|
|**CA Accountability**|Prevent shady certificate issuance|
|**Recon Value**|Discover hidden subdomains|

---

# 🧠 Why It Matters for Recon

CT logs are one of the cleanest ways to enumerate subdomains.

Unlike brute force, they reveal **real issued certificates**.

They help uncover:

- Dev environments
    
- Staging servers
    
- Legacy infrastructure
    
- Forgotten subdomains
    
- Internal naming conventions
    

Example discoveries:

```bash
dev.target.com
staging.target.com
vpn.target.com
old-api.target.com
```

These often expose weak security controls.

---

# ⚔️ CT Logs vs Brute Force

|Method|How It Works|Strength|
|---|---|---|
|**CT Logs**|Queries issued cert history|Accurate|
|**Brute Force**|Guesses subdomains via wordlists|Finds unlisted hosts|

Best practice: **use both**

---

# 🛠️ Main CT Log Tools

|Tool|Use|
|---|---|
|crt.sh|Fast certificate lookup|
|Censys|Deep cert intelligence|
|Amass|Pulls from CT logs automatically|
|Subfinder|Queries CT sources|

---

# 🧠 Basic crt.sh Search

## Browser Lookup

```bash
https://crt.sh/?q=target.com
```

Shows all issued certificates.

---

## JSON Output

```bash
curl -s "https://crt.sh/?q=target.com&output=json"
```

Useful for automation.

---

# 🧠 Extract Unique Subdomains

```bash
curl -s "https://crt.sh/?q=target.com&output=json" \
| jq -r '.[].name_value' \
| sort -u
```

Returns:

```bash
dev.target.com
vpn.target.com
api.target.com
```

---

# 🧠 Filter Specific Keywords

Find dev-related assets:

```bash
curl -s "https://crt.sh/?q=target.com&output=json" \
| jq -r '.[]
| select(.name_value | contains("dev"))
| .name_value' \
| sort -u
```

Useful filters:

```bash
dev
stage
test
vpn
admin
api
internal
old
```

---

# ⚡ Fast Recon Workflow

## 1. Pull CT subdomains

```bash
curl -s "https://crt.sh/?q=target.com&output=json" \
| jq -r '.[].name_value' \
| sort -u > ct-subs.txt
```

---

## 2. Probe live hosts

Using httpx

```bash
httpx -l ct-subs.txt
```

---

## 3. Enumerate content

Using Feroxbuster

```bash
feroxbuster -u https://dev.target.com -r
```

That’s the practical chain:

**CT Logs → Live Hosts → Content Discovery**

---

# ⚠️ Limitations

CT logs won’t show:

- Hosts without SSL certs
    
- Internal-only services
    
- Expired/unlogged certs
    
- HTTP-only assets
    

That’s why CT logs should complement:

- DNS brute force
    
- Passive enumeration
    
- VHost fuzzing
    

---

# 🧠 Key Takeaways

> [!summary]
> 
> - CT logs record all public SSL/TLS cert issuance
>     
> - They’re excellent for passive subdomain enumeration
>     
> - More reliable than guessing with wordlists
>     
> - Great for discovering forgotten infrastructure
>     
> - Best paired with active recon tools
>     

---

This is one of those recon techniques people underuse.

A lot of interesting assets show up in CT logs because devs love issuing certs for things they forgot to hide.