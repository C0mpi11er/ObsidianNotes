# 🧠 DNS (Domain Name System)

---

> [!info] 📌 Overview
> DNS is a **distributed naming system** that translates:
>
> * Domain names → IP addresses
> * IP addresses → domain names (reverse lookup)
>
> Example:
>
> * `www.hackthebox.com → 104.18.x.x`
>
> DNS acts like a **global phonebook for the internet**, mapping human-readable names to machine-readable IP addresses.

---

# 🌐 DNS Hierarchy

> [!info]
> DNS is structured in layers:

* **Root (.)**
* **Top-Level Domains (TLDs)** → `.com`, `.org`, `.net`, `.io`
* **Second-Level Domains** → `hackthebox.com`
* **Subdomains** → `academy.hackthebox.com`
* **Hosts** → `ws01.dev.hackthebox.com`

---

# 🌐 Types of DNS Servers

| Server Type                   | Description                                                                  |
| ----------------------------- | ---------------------------------------------------------------------------- |
| **Root Server**               | Directs queries to TLD servers. Only 13 logical root servers exist globally. |
| **Authoritative Name Server** | Holds official DNS records for a domain zone.                                |
| **Non-authoritative Server**  | Retrieves DNS data via recursive or iterative queries.                       |
| **Caching DNS Server**        | Stores previous DNS responses temporarily for speed.                         |
| **Forwarding Server**         | Forwards queries to another DNS server.                                      |
| **Resolver**                  | Client-side service that performs DNS queries locally.                       |

---

# 🧠 DNS Resolution Process

1. User enters domain (e.g. `example.com`)
2. Resolver checks local cache
3. Query goes to recursive DNS server
4. Root server directs to TLD server
5. TLD server directs to authoritative server
6. IP address is returned to client

---

# 🧠 DNS Security & Encryption

> [!warning]
> DNS is **not encrypted by default**, meaning queries can be intercepted.

### Modern protections:

* **DNS over TLS (DoT)**
* **DNS over HTTPS (DoH)**
* **DNSCrypt**

These prevent ISP/network spying on DNS queries.

---

# 🧠 DNS Records

| Record    | Purpose                                 |
| --------- | --------------------------------------- |
| **A**     | Maps domain → IPv4 address              |
| **AAAA**  | Maps domain → IPv6 address              |
| **MX**    | Mail servers for domain                 |
| **NS**    | Nameservers for domain                  |
| **TXT**   | Text data (SPF, verification, policies) |
| **CNAME** | Alias to another domain                 |
| **PTR**   | Reverse lookup (IP → domain)            |
| **SOA**   | Zone administrative info                |

---

# 🧠 SOA (Start of Authority)

> [!info]
> SOA record defines zone authority and control information.

Includes:

* Primary nameserver
* Admin email
* Serial number
* Refresh / retry / expiry timers

Example:

```
awsdns-hostmaster.amazon.com → awsdns-hostmaster@amazon.com
```

---

# 🧠 DNS Zone Structure

> [!info]
> DNS data is organized into zones:

* **Forward zone** → domain → IP mapping
* **Reverse zone** → IP → domain mapping

---

# 🧠 BIND9 DNS Server

> [!info]
> Most common Linux DNS server implementation.

Config files:

```bash
/etc/bind/named.conf.local
/etc/bind/named.conf.options
```

---

# 🧠 DNS Configuration Structure

## Zone Declaration

```bash id="b4b0hz"
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
};
```

---

# 🧠 Zone File (Forward Lookup)

> [!info]
> Defines domain → IP mappings

Example:

```bash id="5z0c6f"
$ORIGIN domain.com
$TTL 86400

@   IN  SOA dns1.domain.com hostmaster.domain.com (
        2001062501 ; serial
        21600
        3600
        604800
        86400 )

    IN  NS  ns1.domain.com
    IN  NS  ns2.domain.com

server1 IN A 10.129.14.5
server2 IN A 10.129.14.7
www     IN CNAME server2
```

---

# 🧠 Reverse Zone File

> [!info]
> Maps IP → hostname using PTR records

```bash id="y1b3n2"
$ORIGIN 14.129.10.in-addr.arpa

@   IN SOA dns1.domain.com hostmaster.domain.com (
        2001062501
        21600
        3600
        604800
        86400 )

    IN NS ns1.domain.com

5   IN PTR server1.domain.com
7   IN PTR server2.domain.com
```

---

# ⚠️ Dangerous DNS Settings

| Setting                     | Risk                      |
| --------------------------- | ------------------------- |
| **allow-query any**         | Public access to DNS data |
| **allow-recursion any**     | DNS amplification attacks |
| **allow-transfer any**      | Zone transfer leakage     |
| **zone-statistics enabled** | Information leakage       |

---

# 🧠 DNS Footprinting

## NS Record Query

```bash id="k5b7gq"
dig ns example.com @<DNS-IP>
```

---

## ANY Record Query

```bash id="l0c8xq"
dig any example.com @<DNS-IP>
```

---

## SOA Query

```bash id="v8n2bd"
dig soa example.com
```

---

## DNS Version Query

```bash id="t2n5qk"
dig CH TXT version.bind <DNS-IP>
```

---

# 🧠 Zone Transfer (AXFR)

> [!warning]
> If misconfigured, attackers can extract **entire DNS zone database**

```bash id="r7m3pd"
dig axfr example.com @<DNS-IP>
```

---

### Internal Zone Exposure Example

Zone transfer may reveal:

* Internal hostnames
* Private IPs
* Services (mail, VPN, DB servers)

---

# 🧠 Subdomain Enumeration

## Manual Brute Force

```bash id="q9x1mz"
for sub in $(cat wordlist.txt); do
dig $sub.example.com @<DNS-IP>;
done
```

---

## DNSenum Tool

```bash id="c3p8ka"
dnsenum --dnsserver <DNS-IP> --enum example.com
```

---

# 🧠 DNS Security Risks

| Risk                      | Impact                      |
| ------------------------- | --------------------------- |
| Zone transfer enabled     | Full domain mapping exposed |
| Open recursion            | DDoS amplification attacks  |
| Weak ACLs                 | Unauthorized DNS queries    |
| No encryption             | DNS sniffing                |
| Misconfigured TXT records | Data leakage                |

---

# 🧠 Important DNS Commands

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
dig -x 10.10.10.10
```

---

## Zone Transfer Test

```bash
dig axfr example.com @DNS-IP
```

---

# 🧠 Key Tools for DNS Enumeration

| Tool         | Purpose               |
| ------------ | --------------------- |
| **dig**      | Manual DNS queries    |
| **nslookup** | Simple DNS lookups    |
| **dnsenum**  | Automated enumeration |
| **fierce**   | Subdomain discovery   |
| **host**     | Quick DNS resolution  |

---

# 🧠 Key Takeaways

> [!summary]
>
> * DNS is the backbone of internet name resolution.
> * It maps domains to IP addresses using hierarchical servers.
> * It also stores additional records (mail, aliases, verification data).
> * Misconfigurations like **zone transfers and open recursion** are critical vulnerabilities.
> * DNS enumeration is a key reconnaissance step in penetration testing.
