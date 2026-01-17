Beyond common AD persistence methods, attackers can deploy deeper, stealthier techniques that survive cleanup efforts and often require a full domain rebuild to remove. These techniques typically activate **after privileged access** is obtained.

---

### Key Persistence Techniques

#### 1. Skeleton Key Attack

- Deployed using **Mimikatz**
    
- Introduces a **master password** that works for _all_ domain accounts
    
- Legitimate user passwords continue to function
    
- Extremely stealthy and difficult to detect
    
- Enables impersonation of any domain user at will
    

---

#### 2. Directory Services Restore Mode (DSRM) Abuse

- DSRM is a **break-glass local administrator account** on Domain Controllers
    
- Password is set during DC promotion and **rarely rotated**
    
- Can be extracted using **Mimikatz**
    
- Grants **persistent administrative access to DCs**
    
- Often overlooked by defenders
    

---

#### 3. Malicious Security Support Provider (SSP)

- Windows allows loading custom SSPs
    
- Attackers can install **Mimikatz’s mimilib** as an SSP
    
- Logs all authentication credentials
    
- Credentials can be written to disk or exfiltrated to a **remote share**
    
- Provides continuous credential harvesting persistence
    

---

#### 4. Computer Account Persistence

- Machine account passwords rotate every **30 days** by default
    
- Attacker can:
    
    - Manually set the machine account password
        
    - Disable automatic rotation
        
- Grant machine accounts **admin rights over other hosts**
    
- Blends in with normal AD behavior, making detection difficult
    
- Allows machine accounts to be used like user accounts
    

---

### Important Note

- This module focuses on **AD-level persistence**
    
- **Local persistence** on domain-joined hosts can also enable long-term AD access
    

---

## Mitigations & Detection Strategies

### Detection Indicators

- **Anomalous logon behavior**
    
    - Violations of the AD tiering model
        
- Suspicious changes such as:
    
    - Machine account password modifications
        
    - Overly permissive ACL changes
        
    - Unexpected GPO creation or modification
        

---

### Defensive Best Practices

- Strongly protect **privileged accounts and resources**
    
- Monitor changes to:
    
    - AdminSDHolder
        
    - ACLs on sensitive objects
        
    - Authentication packages and services
        
- Understand that:
    
    - Most devastating persistence techniques require **prior privileged access**
        

---

### Final Takeaway

AD persistence is one of the hardest attacker footholds to eradicate. Once deeply embedded, defenders may have **no option but a full domain rebuild**. Effective defense depends on **early detection, strict privilege separation, and continuous monitoring**.

---