# 🧠 WHOIS

---

> [!info] 📌 Overview  
> WHOIS is a **query and response protocol** used to retrieve registration information about internet resources.
> 
> It is mainly used for:
> 
> - Domain names
>     
> - IP address blocks
>     
> - Autonomous Systems (ASNs)
>     

Think of WHOIS as:

**The internet's public ownership directory**

It tells you **who owns, registered, or manages an online asset.**

---

# 🌐 What WHOIS Reveals

A WHOIS lookup provides metadata about a domain.

Example:

```bash
whois example.com
```

Output usually includes:

- Domain ownership
    
- Registrar
    
- Registration dates
    
- Name servers
    
- Technical contacts
    

---

# 🧾 Common WHOIS Fields

|Field|Purpose|
|---|---|
|**Domain Name**|Registered domain|
|**Registrar**|Company that registered the domain|
|**Registrant Contact**|Owner of the domain|
|**Administrative Contact**|Domain management contact|
|**Technical Contact**|Handles technical issues|
|**Creation Date**|Registration date|
|**Expiration Date**|Renewal deadline|
|**Updated Date**|Last modification|
|**Name Servers**|DNS servers for the domain|

---

# 🧠 Example WHOIS Lookup

```bash
whois inlanefreight.com
```

Sample output:

```text
Domain Name: inlanefreight.com
Registrar: Amazon Registrar
Creation Date: 2019-08-05
Updated Date: 2023-07-03
Name Servers: ns-xxxx.awsdns.com
```

---

# 🕰️ Brief History of WHOIS

> [!info]  
> WHOIS was created in the **1970s** during the early ARPANET era.

It was developed to track:

- Network users
    
- Hostnames
    
- Registered internet resources
    

A major contributor was **Elizabeth Feinler**, whose work at the **Stanford Research Institute** helped shape early internet resource management.

WHOIS became one of the internet's first directory systems.

---

# 🎯 Why WHOIS Matters in Web Recon

WHOIS is valuable during reconnaissance because it reveals intelligence about a target's infrastructure.

---

## 👤 Identifying Key Personnel

WHOIS records may expose:

- Admin names
    
- Email addresses
    
- Phone numbers
    

Useful for:

- Social engineering
    
- Phishing target selection
    
- Org structure mapping
    

---

## 🌐 Discovering Infrastructure

WHOIS can reveal:

- DNS providers
    
- Hosting providers
    
- Name servers
    
- Related IP ownership
    

This helps map:

- Target infrastructure
    
- Cloud providers
    
- External services
    

---

## 📅 Historical Analysis

Historical WHOIS records show changes over time.

Useful for spotting:

- Ownership changes
    
- Infrastructure migration
    
- Old contact details
    
- Legacy systems
    

---

# 🔍 Practical Recon Use Cases

## Basic Domain Lookup

```bash
whois target.com
```

---

## Check Expiration Date

Useful for:

- Detecting abandoned assets
    
- Finding neglected domains
    

```bash
whois target.com | grep Expiry
```

---

## Extract Name Servers

```bash
whois target.com | grep "Name Server"
```

This helps identify:

- DNS providers
    
- Potential DNS misconfigs
    
- Cloud infrastructure
    

---

# ⚠️ Recon Insights From WHOIS

A WHOIS record can reveal:

|Finding|Recon Value|
|---|---|
|Personal email exposed|Social engineering|
|Cloud-based name servers|Cloud footprint mapping|
|Recent registration|New infrastructure|
|Old domain|Possible legacy tech|
|Multiple related domains|Attack surface expansion|

---

# 🛠️ Useful WHOIS Tools

|Tool|Purpose|
|---|---|
|**whois**|Command-line lookup|
|**ICANN WHOIS**|Official registry data|
|**WhoisFreaks**|Historical WHOIS records|
|**SecurityTrails**|Historical DNS/WHOIS|
|**ViewDNS**|Quick lookup tools|

---

# 🚫 WHOIS Limitations

Modern privacy protections may hide data.

Common reasons:

- WHOIS privacy protection
    
- GDPR restrictions
    
- Registrar redaction
    

Instead of real contact info, you may see:

```text
REDACTED FOR PRIVACY
```

This doesn’t make WHOIS useless — infrastructure metadata is often still exposed.

---

# 🧠 Key Takeaways

> [!summary]
> 
> - WHOIS is the internet's ownership lookup system
>     
> - It reveals registration and infrastructure metadata
>     
> - Useful for:
>     
>     - Identifying owners
>         
>     - Mapping infrastructure
>         
>     - Discovering related assets
>         
>     - Historical analysis
>         
> - WHOIS is a **core passive reconnaissance technique**
>     
> - Even when redacted, it often exposes valuable technical details
>     

---

## Golden Rule

> **WHOIS often gives your first real clue about who and what you're targeting.**