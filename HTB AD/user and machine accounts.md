
---

# 👤 Active Directory Users & Accounts

User accounts allow people or services to authenticate and access resources. On logon, Windows creates an **access token** containing:

- User SID
    
- Group memberships
    
- Privileges
    

This token is presented whenever the user interacts with system resources.

---

## 🎯 Why User Accounts Matter

Used to:

- Log in to systems
    
- Run services/apps under specific contexts
    
- Control access to files, shares, printers, apps
    
- Delegate permissions via groups
    

⚠️ Users are a major **attack surface** due to:

- Weak/shared passwords
    
- Excessive privileges
    
- Misconfigurations
    
- Forgotten/disabled accounts
    

---

## 🗂️ Account Types in AD

### 🔹 Standard Users → Enterprise Admin

- Rights range from minimal to full domain control
    
- Misconfigurations → privilege escalation
    

### 🔹 Service Accounts

- Run applications/services
    
- Often highly privileged
    
- Frequent attack targets
    

### 🔹 Disabled / Former Employees

- Usually kept for audit
    
- Stored in special OUs (e.g., _FORMER EMPLOYEES_)
    

---

## 🖥️ Local Accounts (Non-Domain)

Stored only on a single host.

**Important Defaults**

- **Administrator** – Full local control (RID 500)
    
- **Guest** – Disabled by default
    
- **SYSTEM** – Highest privilege on host
    
- **Network Service** – Authenticates remotely
    
- **Local Service** – Minimal rights, anonymous to network
    

⚠️ SYSTEM = top privilege level on Windows.

---

## 🌐 Domain Users

- Authenticated by AD
    
- Can log into any domain host
    
- Access domain resources
    

### 🔑 KRBTGT Account

- Kerberos service account
    
- Target of **Golden Ticket** attacks
    
- Enables full domain persistence if compromised
    

---

## 🏷️ Important User Attributes

|Attribute|Purpose|
|---|---|
|**UPN**|Primary logon (email-style)|
|**SAMAccountName**|Legacy logon|
|**ObjectGUID**|Permanent unique ID|
|**objectSID**|Security identity|
|**SIDHistory**|Previous SIDs after migrations|

---

## 🖥️ Domain-Joined vs Workgroup

### Domain-Joined

- Centrally managed via Group Policy
    
- Users roam across machines
    
- Standard enterprise setup
    

### Non-Domain / Workgroup

- No central policy
    
- Local accounts only
    
- Common in home/small networks
    

---

## ⚙️ Machine Accounts & SYSTEM in AD

- Domain-joined SYSTEM ≈ Domain User privileges
    
- SYSTEM compromise on a host enables:
    
    - Domain enumeration
        
    - Recon
        
    - Launching AD attacks
        

💡 SYSTEM access is a powerful foothold in AD.

---

## 📝 Key Takeaways

- Users & service accounts = primary AD attack vectors
    
- Groups simplify admin but expand blast radius
    
- SYSTEM on a domain host is extremely valuable
    
- KRBTGT compromise = full domain control
    
- Naming attributes help identify & track users
    

---
