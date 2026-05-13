# 🧠 DNS (Domain Name System)

---

> [!info] 📌 Overview  
> DNS is the internet’s **name translation system**.
> 
> It converts:
> 
> - Human-readable domains → IP addresses
>     
> - IP addresses → Domain names (reverse lookup)
>     

Example:

```text
www.example.com → 192.0.2.1
```

Think of DNS as:

**The internet’s GPS**

It helps systems find the exact destination for a domain.

Without DNS, you'd need to memorize raw IP addresses.

---

# 🌐 How DNS Works

When you visit a website, DNS translates the domain into an IP.

### Resolution Process

1. You enter a domain (`example.com`)
    
2. Your computer checks local cache
    
3. If not found, it queries a DNS resolver
    
4. Resolver asks Root Server
    
5. Root points to TLD server
    
6. TLD points to Authoritative server
    
7. Authoritative server returns IP
    
8. Browser connects to target server
    

---

## DNS Resolution Flow

```text
Browser
   ↓
Local Cache
   ↓
DNS Resolver
   ↓
Root Server
   ↓
TLD Server (.com)
   ↓
Authoritative Server
   ↓
IP Address Returned
```

---

# 🌍 DNS Hierarchy

DNS is structured like a tree.

|Level|Example|
|---|---|
|**Root**|`.`|
|**TLD**|`.com`, `.org`, `.net`|
|**Second-Level Domain**|`example.com`|
|**Subdomain**|`mail.example.com`|
|**Host**|`api.dev.example.com`|

---

# 🖥️ Types of DNS Servers

|Server Type|Purpose|
|---|---|
|**Root Server**|Directs to TLD servers|
|**TLD Server**|Handles domain extensions|
|**Authoritative Server**|Holds actual DNS records|
|**Recursive Resolver**|Resolves requests for clients|
|**Caching Server**|Stores responses temporarily|

---

# 📂 The Hosts File

> [!info]  
> The hosts file manually maps hostnames to IPs.

It bypasses DNS completely.

---

## Locations

### Linux / macOS

```bash
/etc/hosts
```

### Windows

```text
C:\Windows\System32\drivers\etc\hosts
```

---

## Syntax

```text
IP_ADDRESS hostname
```

Example:

```text
127.0.0.1 localhost
192.168.1.10 devserver.local
```

---

## Common Uses

### Local Development

```text
127.0.0.1 myapp.local
```

---

### Test Server Mapping

```text
192.168.1.20 testserver.local
```

---

### Block Websites

```text
0.0.0.0 unwanted-site.com
```

---

# 🧠 DNS Zones

A **zone** is a managed section of the DNS namespace.

Example:

`example.com`

Includes:

- `mail.example.com`
    
- `api.example.com`
    
- `dev.example.com`
    

---

## Zone File

A zone file stores DNS records.

Example:

```bash
$TTL 3600

@   IN NS ns1.example.com
@   IN MX 10 mail.example.com
www IN A 192.0.2.1
ftp IN CNAME www.example.com
```

---

# 🧾 DNS Record Types

|Record|Purpose|
|---|---|
|**A**|Maps hostname → IPv4|
|**AAAA**|Maps hostname → IPv6|
|**CNAME**|Alias to another hostname|
|**MX**|Mail server|
|**NS**|Name servers|
|**TXT**|Arbitrary text / verification|
|**SOA**|Zone authority info|
|**PTR**|Reverse lookup|
|**SRV**|Service location|

---

# 🔍 Record Examples

## A Record

```dns
www.example.com IN A 192.0.2.1
```

Maps domain to IPv4.

---

## CNAME

```dns
blog.example.com IN CNAME web.example.com
```

Alias.

---

## MX Record

```dns
example.com IN MX 10 mail.example.com
```

Mail routing.

---

## TXT Record

```dns
example.com IN TXT "v=spf1 mx -all"
```

Used for:

- SPF
    
- Domain verification
    
- Security policies
    

---

## PTR Record

```dns
1.2.0.192.in-addr.arpa IN PTR example.com
```

Reverse DNS.

---

# 🧠 What "IN" Means

You’ll often see:

```dns
IN
```

It stands for:

**Internet**

It specifies the DNS class.

Most modern DNS records use this.

---

# 🛠️ Important DNS Commands

## Basic Lookup

```bash
dig example.com
```

---

## Nameserver Lookup

```bash
dig ns example.com
```

---

## Mail Server Lookup

```bash
dig mx example.com
```

---

## Reverse Lookup

```bash
dig -x 192.0.2.1
```

---

## Full DNS Info

```bash
dig any example.com
```

---

# 🎯 Why DNS Matters for Web Recon

DNS is a goldmine during reconnaissance.

---

## 1. Discover Assets

DNS can reveal:

- Subdomains
    
- Mail servers
    
- Internal hosts
    
- Legacy infrastructure
    

Example:

```text
dev.example.com
vpn.example.com
staging.example.com
```

---

## 2. Map Infrastructure

DNS reveals:

- Hosting providers
    
- Cloud services
    
- Load balancers
    
- External integrations
    

This helps build a network map.

---

## 3. Detect Technology Stack

Records may expose:

- Cloudflare
    
- AWS
    
- Google Workspace
    
- Microsoft 365
    

Through:

- NS records
    
- MX records
    
- TXT records
    

---

## 4. Monitor Changes

DNS changes often indicate infrastructure changes.

Examples:

|Change|Possible Meaning|
|---|---|
|New subdomain|New service|
|Changed MX record|Email migration|
|New TXT record|New security platform|

---

# ⚠️ Recon Opportunities

Interesting findings during DNS recon:

|Finding|Recon Value|
|---|---|
|`dev.example.com`|Potential test environment|
|Old CNAME|Legacy vulnerable host|
|TXT with vendor names|Tech stack intel|
|Internal naming conventions|Org structure clues|

---

# 🛠️ DNS Recon Tools

|Tool|Purpose|
|---|---|
|**dig**|Manual DNS queries|
|**nslookup**|Simple lookups|
|**host**|Quick DNS checks|
|**dnsenum**|Automated enumeration|
|**dnsrecon**|Advanced DNS recon|
|**fierce**|Subdomain discovery|

---

# ⚠️ DNS Security Risks

|Misconfiguration|Risk|
|---|---|
|Zone transfer enabled|Full DNS leakage|
|Open recursion|DDoS abuse|
|Excessive TXT exposure|Info leakage|
|Poor subdomain hygiene|Forgotten assets|

---

# 🧠 Key Takeaways

> [!summary]
> 
> - DNS translates domains into IP addresses
>     
> - It works through a hierarchical lookup process
>     
> - Hosts file overrides DNS locally
>     
> - DNS records reveal valuable infrastructure data
>     
> - DNS is one of the most powerful passive recon sources
>     
> - Misconfigured DNS can expose critical attack surface
>     

---

## Golden Rule

> **If WHOIS tells you who owns the target, DNS tells you how it’s built.**