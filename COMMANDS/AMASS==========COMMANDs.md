
# 🧠 Amass Command Cheat Sheet

---

> [!info] 📌 Quick Reference  
> Fast copy-paste commands for **OWASP Amass**

---

# 🔍 Passive Enumeration

## Basic Passive Scan

```bash
amass enum -passive -d example.com
```

---

## Passive + Save Output

```bash
amass enum -passive -d example.com -o subs.txt
```

---

## Show Data Sources

```bash
amass enum -passive -src -d example.com
```

---

# 🔥 Active Enumeration

## Basic Active Scan

```bash
amass enum -active -d example.com
```

---

## Active + Brute Force

```bash
amass enum -active -brute -d example.com
```

---

## Show IP Addresses

```bash
amass enum -active -ip -d example.com
```

---

# 📂 Wordlist Enumeration

## Custom Wordlist

```bash
amass enum -brute -w wordlist.txt -d example.com
```

---

## SecLists Wordlist

```bash
amass enum -brute -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -d example.com
```

---

# 🌐 Multiple Domains

```bash
amass enum -df domains.txt
```

---

# 🧠 Intel Mode

## Domain Intel

```bash
amass intel -d example.com
```

---

## Reverse WHOIS

```bash
amass intel -whois -d example.com
```

---

## ASN Lookup

```bash
amass intel -asn 13335
```

---

# 💾 Output Formats

## Save TXT

```bash
amass enum -d example.com -o results.txt
```

---

## Save JSON

```bash
amass enum -d example.com -json results.json
```

---

# 📊 Database

## List Enumerations

```bash
amass db -list
```

---

## Show Database

```bash
amass db -show
```

---

# 📈 Track Changes

```bash
amass track -d example.com
```

---

# 🖼️ Visualization

## Generate Graph

```bash
amass viz -d3 -dir amass_output
```

---

# ⚡ Useful Combos

## Fast Recon

```bash
 -o subs.txt
```

---

## Full Recon

```bash
amass enum -active -brute -ip -src -d example.com -o full.txt
```

---

## Bug Bounty Mode

```bash
amass enum -passive -active -brute -d example.com
```

---

# 🔑 API Config

```bash
nano ~/.config/amass/config.ini
```

Add API keys for:

- **Shodan**
    
- **Censys**
    
- **VirusTotal**
    
- **SecurityTrails**
    

---

# 🚀 Validation Pipeline

## Probe Live Hosts

```bash
amass enum -passive -d example.com -o subs.txt && httpx -l subs.txt
```

---

## Screenshot Hosts

```bash
amass enum -passive -d example.com -o subs.txt && aquatone -domains subs.txt
```

---

# 🧠 Most Used

```bash
amass enum -passive -d example.com
amass enum -active -brute -d example.com
amass intel -d example.com
amass track -d example.com
```