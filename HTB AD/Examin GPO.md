# 🛡️ Examining Group Policy (AD Security)

## 📌 Overview

- Group Policy = centralized control for users & computers in AD.
    
- Critical for **security hardening** but dangerous if abused.
    
- Misconfigured GPO rights → **lateral movement, persistence, domain takeover**.
    
- Key part of **defense-in-depth**.
    

---

## 📂 Group Policy Objects (GPOs)

- Virtual collections of settings.
    
- Linked to **Sites, Domains, OUs**.
    
- Identified by **GUID**.
    
- One GPO → many containers; many GPOs → one container.
    

### Examples:

- Password policies per role.
    
- Disable USB.
    
- Enforce screen lock.
    
- Block CMD/PowerShell.
    
- Audit logging.
    
- Deploy software.
    
- Logon banners.
    
- Startup/shutdown scripts.
    
- Disable LM hashes.
    

---

## 🔐 Default Password Policy (WS2008)

- Min length: **7 chars**.
    
- Must meet **3 of 4**:
    
    - Uppercase
        
    - Lowercase
        
    - Numbers
        
    - Symbols
        

---

## 🏗️ GPO Processing Order (LSDOU)

1. Local
    
2. Site
    
3. Domain
    
4. Parent OU
    
5. Child OU
    

➡️ **Last applied wins**.  
➡️ Computer policies override user policies.

---

## 🔢 Link Order

- Lower number = higher precedence.
    
- Link Order **1** runs last.
    

---

## 🚫 Enforced vs Block Inheritance

- **Enforced** → lower OUs cannot override.
    
- **Block Inheritance** → stops higher GPOs.
    
- **Enforced beats Block**.
    
- Enforced **Default Domain Policy** overrides everything.
    

---

## 🔄 GPO Refresh

- Workstations: every **90 ±30 mins**.
    
- DCs: every **5 mins**.
    
- Manual: `gpupdate /force`.
    
- Refresh interval configurable (don’t set too low).
    

---

## ⚠️ GPO Abuse Risks

Attackers with GPO write rights can:

- Add admins.
    
- Modify group membership.
    
- Deploy malware.
    
- Create malicious scheduled tasks.
    
- Persist in domain.
    

🔎 Tools like **BloodHound** reveal dangerous GPO permissions.

---

## 🧠 Key Takeaways

- GPOs = powerful defense **and** attack vector.
    
- Audit who can **modify** GPOs.
    
- Track inheritance & enforcement carefully.
    
- Secure high-value OUs (DCs, Admins).