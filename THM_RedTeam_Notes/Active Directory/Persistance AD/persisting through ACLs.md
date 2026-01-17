Standard group membership persistence can be removed by blue teams through cleanup. To achieve **durable persistence across all protected groups**, attackers can target **AD group templates** instead of individual groups.

The most critical template is **AdminSDHolder**.

---

## Key Concept: AdminSDHolder

- **AdminSDHolder** is an AD container whose **ACL acts as a template**
    
- Applies to **Protected Groups**, including:
    
    - Domain Admins
        
    - Enterprise Admins
        
    - Administrators
        
    - Schema Admins
        
- A service called **SDProp**:
    
    - Runs **every 60 minutes**
        
    - Copies AdminSDHolder ACL → all protected groups
        

### Why This Matters

- Any permission added to AdminSDHolder:
    
    - **Automatically reappears** on protected groups
        
    - Survives cleanup
        
    - Generates **no obvious alerts**
        
- Blue team removes access → it returns within the hour
    

---

## Persistence Strategy

Instead of adding yourself to privileged groups:

1. Modify **AdminSDHolder ACL**
    
2. Grant your user **Full Control**
    
3. Let SDProp propagate permissions
    
4. Use permissions to re-add yourself to groups at will
    

---

## Attack Flow

1. Access AD with admin credentials (via `runas`)
    
2. Modify AdminSDHolder permissions
    
3. Trigger SDProp manually
    
4. Gain control over all protected groups
    
5. Add yourself to any privileged group as needed
    

---

## Commands / Actions

### Elevate Context (Without Killing RDP Sessions)

`runas /netonly /user:thmchilddc.tryhackme.loc\Administrator cmd.exe`

---

### Modify AdminSDHolder (GUI – MMC)

1. Open **MMC**
    
2. Add **Active Directory Users and Computers**
    
3. Enable:
    
    - `View → Advanced Features`
        
4. Navigate to:
    

5. `Domain → System → AdminSDHolder`
    
6. Properties → Security
    
7. Add **low-privileged user**
    
8. Grant **Full Control**
    

---

### Trigger SDProp Manually (No Waiting 60 Minutes)

`Import-Module .\Invoke-ADSDPropagation.ps1 Invoke-ADSDPropagation`

---

### Verify Permissions

- Check **Domain Admins → Security**
    
- Your low-privileged user should have **Full Control**
    
- Remove permissions → rerun SDProp → permissions reappear
    

---

### Add Yourself to a Protected Group

`Add-ADGroupMember -Identity "Domain Admins" -Members "<username>"`

---

## Verification

- Permissions persist even after removal
    
- SDProp reapplies ACL every execution
    
- Access restored without touching AdminSDHolder again
    

---

## Detection Gaps (Why This Works)

- SDProp is a **legitimate AD process**
    
- No alerts for ACL propagation
    
- Cleanup appears ineffective
    
- Root cause (AdminSDHolder) often overlooked
    

---

## Impact

- Persistent control over **all protected groups**
    
- Survives credential rotation
    
- Scales across domain
    
- Extremely difficult to remediate without full AD rebuild
    

---

## Advanced Abuse

- Grant **Domain Users** full control in AdminSDHolder
    
- Any low-priv user gains control of protected groups
    
- Combined with **DCSync** → full domain compromise
    
- Blue team must reset **every credential** to recover
    

---

## 🧠 Mind Map (Obsidian Mermaid)

`mindmap   root((AdminSDHolder Persistence))     Template Abuse       ACL Inheritance       Protected Groups     SDProp       Runs Every 60 Min       Auto Reapply Permissions     Execution       Modify AdminSDHolder       Grant Full Control       Trigger SDProp     Result       Control All Privileged Groups       Persistence After Cleanup     Detection Failure       Legitimate AD Process       No Alerts     Impact       Domain-Wide Persistence       Near-Irreversible Compromise`

---