# 🛰️ DNS Reconnaissance Guide

## 🚀 Introduction
DNS reconnaissance is a critical part of network security and penetration testing, involving the discovery and analysis of subdomains, MX records, NS servers, and more.

## 🔍 Tools Overview

### [!ABSTRACT] dig - Manual DNS Queries
`dig` provides detailed information about various aspects of domain configurations. It can perform DNS lookups, zone transfers, and WHOIS queries.
```bash
# Basic DNS query for A record
dig example.com A

# Zone transfer attempt (AXFR)
dig @ns1.example.com example.com axfr
```

### [!ABSTRACT] dnsenum - Comprehensive Scan
`dnsenum` performs a comprehensive scan including zone transfers, brute-force subdomain discovery, and WHOIS lookups.
```bash
# Basic DNS enumeration
dnsenum --enum example.com

# Brute-force subdomains with a custom wordlist
dnsenum --subdomains=wordlist.txt --whois -f example.com
```

### [!ABSTRACT] amass - Passive and Active Reconnaissance
`amass` uses over 30 data sources for passive reconnaissance, and supports active enumeration techniques.
```bash
# Passive subdomain discovery
amass enum -passive -d example.com

# Brute-forcing with custom wordlist
amass enum -active -wordlists=custom.txt -d example.com -brute
```

### [!ABSTRACT] puredns - High-Performance Brute-Forcing
`puredns` is designed for speed and efficiency, capable of handling massive wordlists.
```bash
# Bruteforcing with a large wordlist
puredns bruteforce /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt example.com

# Wildcard subdomain detection
puredns wildcard-check all_subdomains.txt --rate-limit 500
```

### [!ABSTRACT] fierce - Simple and Fast Brute-Forcing
`fierce` performs a quick DNS brute-force with built-in wildcard checks.
```bash
# Basic DNS brute-forcing
fierce -dns example.com

# Advanced options (e.g., wordlist, rate limit)
fierce --wordlist /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -r 200
```

### [!ABSTRACT] dnsrecon - Multi-Faceted Scan
`dnsrecon` offers various techniques for DNS reconnaissance.
```bash
# Comprehensive DNS enumeration
dnsrecon -d example.com

# Zone transfer brute-forcing
dnsrecon -t axfr -d example.com

# MX record enumeration
dnsrecon -t mx -d example.com
```

### [!ABSTRACT] subfinder - Passive Subdomain Discovery
`subfinder` uses passive data collection techniques to discover subdomains.
```bash
# Basic subdomain discovery
subfinder -d example.com

# With specific sources (e.g., crtsh, virustotal)
subfinder -d example.com -sources crtsh,hackertarget,virustotal
```

### [!ABSTRACT] assetfinder - Quick Subdomain Enumeration
`assetfinder` is a quick and lightweight tool for subdomain discovery.
```bash
# Fast subdomain discovery
assetfinder example.com

# Find only subdomains
assetfinder --subs-only example.com
```

## 📝 Tool Selection Guide

### [!INFO] When to Use What

| Scenario | Recommended Tool | Reason |
|----------|------------------|--------|
| **Quick manual DNS queries** | **dig** | Most versatile, detailed output |
| **Comprehensive automated scan** | **dnsenum** | All-in-one: zone transfers, brute-force, WHOIS |
| **Passive reconnaissance only** | **amass** (passive) | 80+ data sources, stealthy |
| **Maximum subdomain coverage** | **amass** (active) | Passive + active brute-forcing |
| **High-performance brute-forcing** | **puredns** | Massive wordlists, wildcard filtering |
| **User-friendly quick scan** | **fierce** | Simple interface, wildcard detection |
| **Multi-technique approach** | **dnsrecon** | Various techniques, custom outputs |
| **Quick lightweight scan** | **assetfinder** | Fast, simple discovery |

### [!INFO] Performance Comparison

| Tool | Speed | Accuracy | Stealth | Wordlist Size | Resource Usage |
|------|-------|----------|---------|---------------|----------------|
| **dig** | Manual | High | High | Manual | Low |
| **dnsenum** | Medium | High | Medium | Large | Medium |
| **amass** | Medium-Fast | Very High | High (passive) | Large | High |
| **puredns** | Very Fast | High | Medium | Very Large | Medium |
| **fierce** | Fast | High | Medium | Medium | Low |
| **dnsrecon** | Medium | High | Medium | Large | Medium |
| **assetfinder** | Fast | Medium | High | N/A | Low |

## 🕵️ Recommended Workflow

### [!CHECK] Phase 1: Quick Discovery
```bash
# Fast initial enumeration
assetfinder example.com
subfinder -d example.com

# Certificate transparency
curl -s https://crt.sh/\?q\=example.com\&output\=json | jq -r '.[].name_value' | sort -u
```

### [!CHECK] Phase 2: Passive Enumeration
```bash
# Comprehensive passive discovery
amass enum -passive -d example.com

# OSINT gathering
theHarvester -d example.com -l 200 -b google,bing
```

### [!CHECK] Phase 3: Active Enumeration
```bash
# Comprehensive active enumeration
amass enum -active -d example.com -brute

# Automated comprehensive scan
dnsenum --enum example.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
```

### [!CHECK] Phase 4: High-Performance Brute-Forcing
```bash
# Massive wordlist brute-forcing
puredns bruteforce /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt example.com --rate-limit 1000

# Validation and cleanup
puredns resolve all_subdomains.txt --write validated_subdomains.txt
```

## 🛡️ Security Considerations

### [!WARNING] Rate Limiting
```bash
# Avoid detection with delays
for sub in $(cat wordlist.txt); do
  dig $sub.example.com
  sleep 1
done

# Use multiple DNS servers
dns_servers=(8.8.8.8 1.1.1.1 9.9.9.9)
for server in "${dns_servers[@]}"; do
  dig @$server example.com
done
```

### [!WARNING] Stealth Techniques
```bash
# Passive enumeration only
amass enum -passive -d example.com
subfinder -d example.com

# Certificate transparency (no DNS queries)
curl -s https://crt.sh/\?q\=example.com\&output\=json | jq -r '.[].name_value'
```

## 🛡️ Defensive Measures

### [!INFO] DNS Server Hardening
```bash
# Restrict zone transfers
allow-transfer { trusted-servers; };

# Disable recursion for external queries
allow-recursion { internal-networks; };

# Hide DNS version
version "Not disclosed";

# Rate limiting
rate-limit {
    responses-per-second 5;
    window 5;
};
```

### [!INFO] Monitoring and Detection
```bash
# Monitor DNS queries
tail -f /var/log/named/queries.log

# Detect enumeration attempts
grep -E "(axfr|version.bind)" /var/log/named/queries.log
```

## 📝 Key Takeaways

1. **dig is essential** for manual DNS analysis and troubleshooting.
2. **dnsenum provides** comprehensive automated enumeration.
3. **amass offers** maximum subdomain coverage with 80+ sources.
4. **puredns excels** at high-performance brute-forcing.
5. **Passive enumeration** (amass passive, subfinder) avoids detection.
6. **Rate limiting** is crucial to prevent blocking.
7. **Zone transfers** should be tested on all name servers.
8. **Certificate transparency** provides valuable subdomain data.
9. **Tool combination** yields better results than single tools.
10. **DNS security** can be assessed through enumeration attempts.

---

## 📚 References

- HTB Academy: Information Gathering - Web Edition
- RFC 1034, 1035: Domain Names - Concepts and Facilities
- OWASP Testing Guide: Information Gathering
- SecLists: https://github.com/danielmiessler/SecLists
- Amass Documentation: https://github.com/OWASP/Amass