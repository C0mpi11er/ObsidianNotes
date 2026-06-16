


# External Reconnaissance & Enumeration

> [!info]
> 
> External reconnaissance is the act of mapping a target's public footprint to validate scope, identify information leaks, and harvest credentials for initial access.

## 🧩 Core Idea

> [!important]
> 
> **Passive Recon = The "Zero-Touch" Blueprint**
> 
> Primary objectives:
> 
> - **Infrastructure Mapping:** Validating ASNs, netblocks, and Cloud vs. Self-hosted status.
>     
> - **Domain Discovery:** Uncovering subdomains and public-facing services (VPN, OWA, RDP).
>     
> - **Human Harvesting:** Identifying email/username schemas and employee rosters.
>     
> - **Data Leaks:** Hunting for GitHub keys, document metadata, and historical breach credentials.
>     

# ⚙️ Attack Flow (The Passive-to-Active Pipeline)

> [!note]
> 
> The process moves from broad internet datasets to specific target validation.

### 1. Broad Discovery (Passive)

- Identify Netblocks (BGP Toolkit).
    
- Identify Subdomains (ViewDNS / Search Engines).
    
- Identify Employees (LinkedIn / Job Postings).
    

### 2. Deep Extraction (Targeted Passive)

- Extract Metadata (Document Scraping).
    
- Extract Credentials (DeHashed / GitHub).
    

### 3. Entry Validation (Active)

- **Password Spraying:** Using harvested usernames + breach passwords against exposed portals.
    

# 🗺️ Attack Workflow (From IP to Foothold)

> [!tip]
> 
> Use this sequence to transition from an IP address to a valid domain credential.

Plaintext

```
 [1] ASN/IP Lookup ──> [2] DNS Enumeration ──> [3] Document/Dork Scraping
                                                           │
 [6] Internal Pivot ◄── [5] External Portal Login ◄── [4] Breach Match (DeHashed)
```

1. **Verify Boundaries:** Use **BGP.he** to find the ASN. If the target is on **AWS/Azure**, check the scoping doc for third-party testing permissions.
    
2. **Expand Surface:** Resolve subdomains via `nslookup` and `viewdns.info`. Look for "hidden" IPs that the client missed in the scope.
    
3. **Scrape Logic:**
    
    - `filetype:pdf inurl:target.com` (Metadata reveals internal username format).
        
    - `intext:"@target.com"` (Builds the initial user list).
        
4. **Credential Harvest:** Cross-reference the user list against **DeHashed**. Search for cleartext passwords from old leaks.
    
5. **Establish Foothold:** Use the found credentials to log into a **VPN, Citrix, or OWA** portal.
    
6. **Pivot:** Once authenticated, move from **External Recon** to **Internal Active Directory Enumeration**.
    

# 🛠️ Tool & Resource Mapping

> [!important]
> 
> Match the tool to the specific recon layer.

|**Layer**|**Tool / Source**|**Purpose**|
|---|---|---|
|**Network**|BGP Toolkit / ARIN / RIPE|Identify ASNs and Netblock ownership.|
|**DNS**|Viewdns.info / nslookup|Validate IPs and find subdomains.|
|**Leaked Keys**|Trufflehog / GitHub|Find API keys/creds in public code.|
|**Usernames**|linkedin2username|Automated employee-to-username scraping.|
|**Breaches**|DeHashed / HIBP|Extract historical cleartext passwords/hashes.|

# 🧠 Key Takeaway

> [!summary]
> 
> External Recon = **Credential Sourcing**
> 
> In modern pentesting, you rarely "hack" the perimeter with an exploit. You **source** a valid credential from OSINT, **spray** it against a portal, and **walk in** the front door.