# 🎟️ Golden & Silver Tickets (Kerberos Abuse)

## Context & Motivation

In Active Directory post-exploitation, **credential persistence** is more valuable than one-time privilege escalation.  
Service accounts with **delegation permissions** enable attackers to forge **Golden** and **Silver Kerberos tickets**, which is why blue teams panic and shout:

> **“Flush all golden and silver tickets!”**

---

## 🧠 Kerberos Authentication (Quick Recap)

### Normal Kerberos Flow

1. **AS-REQ** → User requests a **TGT** from the DC
    
    - Timestamp encrypted with **user NTLM hash**
        
2. **AS-REP** → DC returns **TGT**
    
    - TGT is signed with **KRBTGT password hash**
        
3. **TGS-REQ** → User presents TGT to request service access
    
4. **TGS-REP** → DC issues **TGS**
    
    - Encrypted with **service account NTLM hash**
        
5. **Service Access** → User presents TGS to service
    

> 🔑 Trust is based on cryptographic signatures, not passwords after ticket issuance.

---

## 🟡 Golden Tickets

### What is a Golden Ticket?

- A **forged TGT**
    
- Bypasses **user authentication entirely**
    
- Signed using the **KRBTGT account NTLM hash**
    

### Why Golden Tickets Are Dangerous

- Grants access to **any service in the domain**
    
- Provides **Domain Admin–level persistence**
    
- Can impersonate **any user** (even non-existent ones)
    

### Key Properties

- No need for victim’s password hash
    
- User validation only occurs if ticket is **older than 20 minutes**
    
- Ticket lifetime can be **manipulated** (e.g., 10 years)
    
- KRBTGT password **never rotates by default**
    
- Requires **two rotations** to invalidate (current + previous)
    
- Bypasses **smart card authentication**
    
- Can be generated **off-domain**
    

### Required Data

- KRBTGT NTLM hash
    
- Domain name
    
- Domain SID
    
- User RID (often `500` – Administrator)
    

---

## ⚪ Silver Tickets

### What is a Silver Ticket?

- A **forged TGS**
    
- Skips **all communication with the DC**
    
- Authenticates **directly to a service**
    

### Key Differences from Golden Tickets

|Feature|Golden Ticket|Silver Ticket|
|---|---|---|
|Ticket Type|TGT|TGS|
|Signed By|KRBTGT|Machine account|
|Scope|Entire domain|Single service/host|
|DC Contact|Yes (initially)|❌ No|
|Detectability|Higher|Very low|
|Persistence|Very high|Host-limited|

### Key Properties

- Uses **machine account NTLM hash**
    
- Logs exist **only on target host**
    
- Can impersonate **non-existent users**
    
- SID injection allows **local admin access**
    
- Machine passwords rotate every **30 days**
    
- Rotation can be disabled via registry → persistence
    

> ⚠️ While limited in scope, Silver Tickets are **stealthier** than Golden Tickets.

---

## 🧪 Forging Tickets with Mimikatz

### Required Information

- KRBTGT NTLM hash (Golden)
    
- Machine account NTLM hash (Silver)
    
- Domain SID
    
- Domain name
    

### Get Domain SID

`Get-ADDomain`

---

## 🟡 Generate Golden Ticket

`kerberos::golden  /admin:FakeAdmin  /domain:za.tryhackme.loc  /id:500  /sid:<Domain SID>  /krbtgt:<KRBTGT NTLM hash>  /endin:600  /renewmax:10080  /ptt`

### Parameter Highlights

- `/admin` → impersonated username (can be fake)
    
- `/id` → RID (`500` = Administrator)
    
- `/ptt` → inject ticket into current session
    

### Verify Golden Ticket

`dir \\thmdc.za.tryhackme.loc\c$\`

If this works → **Domain Admin access confirmed**

---

## ⚪ Generate Silver Ticket

`kerberos::golden  /admin:FakeAdmin  /domain:za.tryhackme.loc  /id:500  /sid:<Domain SID>  /target:THMSERVER1.za.tryhackme.loc  /rc4:<Machine NTLM hash>  /service:cifs  /ptt`

### Verify Silver Ticket

`dir \\thmserver1.za.tryhackme.loc\c$\`

---

## 🛡️ Blue Team Mitigation Notes

- Rotate **KRBTGT password twice**
    
- Rotate **machine account passwords**
    
- Monitor:
    
    - Abnormal ticket lifetimes
        
    - Service access without DC logs
        
- Golden Tickets = **domain-wide blast radius**
    
- Silver Tickets = **stealth persistence**