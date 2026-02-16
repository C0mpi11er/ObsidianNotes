
### 🗒️ AD Lecture — FSMO, Functional Levels & Trusts (Brief Notes)

**FSMO Roles** — Special DC roles preventing conflicts and ensuring AD stability.

- **Schema Master** — Controls schema changes.
    
- **Domain Naming Master** — Prevents duplicate domain names.
    
- **RID Master** — Issues RID pools to keep SIDs unique.
    
- **PDC Emulator** — Auth, password changes, time sync, GPO authority.
    
- **Infrastructure Master** — Resolves SIDs/DNs across domains.
    

---

**Domain Functional Level** — Determines AD features + allowed DC OS versions.

- **2000 Native** — Universal groups, SID history.
    
- **2003** — Delegation, selective auth, lastLogonTimestamp.
    
- **2008** — AES Kerberos, fine-grained passwords, DFS-R.
    
- **2008 R2** — Managed Service Accounts.
    
- **2012** — Kerberos armoring, claims auth.
    
- **2012 R2** — Protected Users, auth silos.
    
- **2016** — Smart-card logon, credential protections.
    

---

**Forest Functional Level** — Enables forest-wide capabilities.

- **2003** — Forest trusts, domain rename, RODC.
    
- **2008 R2** — AD Recycle Bin.
    
- **2016** — Privileged Access Mgmt (PAM).
    

---

**Trusts** — Auth links between domains/forests.

- **Parent–Child** — Default inside a forest.
    
- **Cross-link** — Speeds child-domain auth.
    
- **External** — Non-transitive; SID filtering.
    
- **Tree-root** — New tree ↔ forest root.
    
- **Forest** — Root-to-root trust.
    

---

**Trust Properties**

- **Transitive** — Trust extends to child domains.
    
- **Non-transitive** — Only specific domain trusted.
    
- **Two-way** — Mutual access.
    
- **One-way** — Access flows opposite trust direction.
    

---

**Security Note** — Misconfigured trusts create attack paths (e.g., cross-forest Kerberoasting → domain admin compromise).