
---

# 🧠 DNS Zone Transfer (AXFR) Cheat Sheet

---

> [!warning] 📌 Overview  
> A **DNS zone transfer (AXFR)** is the process where a DNS server copies **all zone records** (subdomains + DNS data) to another server.

If misconfigured, it can leak:

- Subdomains
    
- IP addresses
    
- Mail servers (MX)
    
- Internal hosts
    
- Hidden services
    

---

# ⚡ Basic Zone Transfer Test

```bash
dig axfr @ns1.example.com example.com
```

---

# 🌐 Target Specific Nameserver

```bash
dig axfr @<nameserver> <domain>
```

Example:

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

---

# 🔍 Find Nameservers First (IMPORTANT)

```bash
dig ns example.com
```

---

# 🔁 Combine NS + AXFR (manual workflow)

```bash
dig ns example.com
dig axfr @<ns-from-above> example.com
```

---

# ⚡ One-Liner Workflow

```bash
for ns in $(dig ns example.com +short); do dig axfr @${ns} example.com; done
```

---

# 🧠 Check SOA (useful for validation)

```bash
dig soa example.com
```

---

# 🔎 Verify if Zone Transfer is blocked

If secure, you’ll see:

```text
Transfer failed.
```

or:

```text
REFUSED
```

---

# 🚨 Successful AXFR Output Looks Like

If vulnerable, you get full DNS dump:

- A records
    
- MX records
    
- TXT records
    
- NS records
    
- Subdomains
    

Example indicators:

```text
www.example.com     A     192.168.x.x
mail.example.com    MX    ...
dev.example.com     A     ...
```

---

# 🧠 Real-World Enumeration Flow

## Step 1 — Get NS records

```bash
dig ns example.com
```

---

## Step 2 — Test AXFR

```bash
dig axfr @ns1.example.com example.com
```

---

## Step 3 — Automate across all NS

```bash
for ns in $(dig ns example.com +short); do dig axfr @${ns} example.com; done
```

---

# 🔧 Alternative Tools

## dnsenum (auto AXFR test)

```bash
dnsenum example.com
```

---

## dnsrecon

```bash
dnsrecon -d example.com -t axfr
```

---

## fierce

```bash
fierce --domain example.com
```

---

# ⚠️ Common Responses

|Response|Meaning|
|---|---|
|REFUSED|Zone transfer blocked|
|NOTAUTH|Not authoritative|
|SERVFAIL|DNS issue|
|timeout|firewall / filtering|
|full record dump|❌ misconfiguration (vulnerable)|

---

# 🧠 Key Takeaways

> [!summary]
> 
> - AXFR = full DNS database copy
>     
> - Requires misconfigured DNS servers
>     
> - Always test against **all nameservers**
>     
> - One successful transfer = full subdomain takeover map
>     
> - Most modern systems block it, but misconfigs still exist
>     

---

If you want, I can next give you a **“Subdomain enumeration workflow (DNS → brute → CT logs → AXFR → validation)”** that ties everything you’ve learned into a real recon pipeline.