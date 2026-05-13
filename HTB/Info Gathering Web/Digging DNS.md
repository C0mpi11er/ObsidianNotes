# 🧠 Digging DNS (`dig`)

---

> [!info] 📌 Overview  
> `dig` (**Domain Information Groper**) is the most powerful command-line DNS lookup tool.
> 
> It is used to:
> 
> - Query DNS records
>     
> - Troubleshoot DNS issues
>     
> - Perform DNS reconnaissance
>     
> - Inspect DNS resolution paths
>     

For web recon, `dig` is one of the most important DNS tools.

---

# 🔍 Why `dig` Matters

`dig` helps you uncover:

- IP addresses
    
- Mail servers
    
- Name servers
    
- TXT records
    
- DNS misconfigurations
    
- Hidden infrastructure clues
    

It provides far more detail than basic tools like:

- `nslookup`
    
- `host`
    

---

# 🛠️ Popular DNS Recon Tools

|Tool|Purpose|
|---|---|
|**dig**|Advanced DNS queries|
|**nslookup**|Basic DNS lookup|
|**host**|Quick DNS resolution|
|**dnsenum**|Automated enumeration|
|**fierce**|Subdomain discovery|
|**dnsrecon**|Full DNS recon|
|**theHarvester**|OSINT + email gathering|

---

# 🧠 The `dig` Syntax

Basic structure:

```bash
dig [server] domain [record type]
```

Example:

```bash
dig example.com A
```

---

# 📌 Common `dig` Commands

## Default Lookup (A Record)

```bash
dig example.com
```

Finds IPv4 address.

---

## IPv4 Lookup

```bash
dig example.com A
```

---

## IPv6 Lookup

```bash
dig example.com AAAA
```

---

## Mail Servers

```bash
dig example.com MX
```

---

## Name Servers

```bash
dig example.com NS
```

---

## TXT Records

```bash
dig example.com TXT
```

Useful for finding:

- SPF
    
- DKIM
    
- Verification records
    

---

## CNAME Record

```bash
dig example.com CNAME
```

Reveals aliases.

---

## SOA Record

```bash
dig example.com SOA
```

Shows zone authority info.

---

## Reverse Lookup

```bash
dig -x 192.168.1.1
```

Maps IP → hostname.

---

## Query Specific DNS Server

```bash
dig @1.1.1.1 example.com
```

Queries **Cloudflare DNS**

Useful for comparison.

---

## Trace Full Resolution Path

```bash
dig +trace example.com
```

Shows the complete DNS lookup chain.

---

## Short Output

```bash
dig +short example.com
```

Returns only the answer.

Example:

```text
104.18.20.126
104.18.21.126
```

---

## Answer Only

```bash
dig +noall +answer example.com
```

Cleaner output.

---

## ANY Record Query

```bash
dig example.com ANY
```

Attempts to retrieve all records.

> [!warning]  
> Many modern DNS servers block this.

---

# 🔍 Reading `dig` Output

Example:

```bash
dig google.com
```

---

## Sample Output

```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR

;; QUESTION SECTION:
;google.com. IN A

;; ANSWER SECTION:
google.com. IN A 142.251.47.142
```

---

# 🧠 Output Breakdown

---

## Header

```text
status: NOERROR
```

Means query succeeded.

Other common statuses:

|Status|Meaning|
|---|---|
|**NOERROR**|Success|
|**NXDOMAIN**|Domain doesn't exist|
|**SERVFAIL**|Server error|
|**REFUSED**|Query denied|

---

## Flags

Example:

```text
qr rd ad
```

|Flag|Meaning|
|---|---|
|**qr**|Response|
|**rd**|Recursion requested|
|**ad**|Authenticated data|

---

## Question Section

Shows what was asked.

Example:

```text
google.com IN A
```

Translation:

**"What is Google's IPv4 address?"**

---

## Answer Section

The actual result.

Example:

```text
google.com IN A 142.251.47.142
```

---

## TTL (Time To Live)

TTL defines cache duration.

Example:

```text
300
```

Means:

Cache for **300 seconds**

Low TTL often suggests:

- Dynamic infrastructure
    
- Load balancing
    
- Frequent changes
    

---

## Footer

Contains:

- Query time
    
- DNS server used
    
- Timestamp
    
- Packet size
    

Useful for troubleshooting.

---

# ⚠️ Important Warning

Sometimes you'll see:

```text
recursion requested but not available
```

This means:

The DNS server won’t recursively resolve requests.

It only answers for domains it directly manages.

---

# 🧠 EDNS / OPT Pseudosection

You may also see:

```text
OPT PSEUDOSECTION
```

This indicates **EDNS (Extension Mechanisms for DNS)**

Adds support for:

- Larger packet sizes
    
- DNSSEC
    
- Extended features
    

---

# 🎯 Using `dig` for Web Recon

## 1. Identify Hosting Infrastructure

```bash
dig example.com
```

Helps reveal:

- Hosting provider
    
- CDN usage
    
- Load balancers
    

---

## 2. Enumerate Mail Servers

```bash
dig example.com MX
```

Can reveal:

- Email providers
    
- Internal naming conventions
    

---

## 3. Discover DNS Providers

```bash
dig example.com NS
```

May expose:

- AWS Route53
    
- Cloudflare
    
- Akamai
    

---

## 4. Gather Security Policies

```bash
dig example.com TXT
```

Look for:

- SPF
    
- DKIM
    
- Verification tokens
    

---

## 5. Reverse Infrastructure Mapping

```bash
dig -x target-ip
```

Useful for pivoting.

---

# 🚨 Recon Tips

Useful combinations:

### Fast Subdomain Check

```bash
dig +short sub.example.com
```

---

### Compare Resolvers

```bash
dig @8.8.8.8 example.com
dig @1.1.1.1 example.com
```

---

### Trace Delegation

```bash
dig +trace example.com
```

Excellent for spotting DNS delegation issues.

---

# ⚠️ Recon Etiquette

> [!warning]
> 
> Excessive DNS queries may:
> 
> - Trigger rate limits
>     
> - Alert defenders
>     
> - Cause temporary blocks
>     

Always stay within scope.

---

# 🧠 Key Takeaways

> [!summary]
> 
> - `dig` is the primary DNS recon tool
>     
> - It retrieves detailed DNS records
>     
> - Helps map infrastructure
>     
> - Reveals hosting and mail systems
>     
> - Useful for troubleshooting and enumeration
>     
> - Understanding output structure is essential
>     

---

## Golden Rule

> **If DNS is the map, `dig` is your magnifying glass.**