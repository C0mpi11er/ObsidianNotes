
---

# 👥 Active Directory Groups

Groups are used to **mass-assign permissions** to users, computers, and services. They are a major attack surface because:

- Privileges may be hidden via nesting
    
- Group sprawl causes excessive access
    
- Misconfigurations enable lateral movement & escalation
    

Regular **group audits** are essential.

---

## 🆚 Groups vs Organizational Units (OUs)

|Object|Purpose|
|---|---|
|**Groups**|Assign permissions to resources|
|**OUs**|Apply Group Policy & delegate admin tasks|

⚠️ OUs do **not** grant access—groups do.

---

## 🎯 Why Groups Are Used

- Simplify access control
    
- Easier permission revocation
    
- Easier auditing
    
- Centralized admin
    

---

## 🧩 Group Characteristics

Groups have two attributes:

- **Type** → purpose
    
- **Scope** → where it can be used
    

---

## 🔐 Group Types

### Security Groups

- Assign permissions
    
- Used for access control
    

### Distribution Groups

- Email lists (Exchange)
    
- ❌ Cannot assign permissions
    

---

## 🌍 Group Scopes

### Domain Local

- Grant access only inside domain
    
- Can contain users from other domains
    
- Can nest only into Domain Local
    

### Global

- Used across domains
    
- Members only from same domain
    
- Can nest into Global & Domain Local
    

### Universal

- Forest-wide permissions
    
- Stored in Global Catalog
    
- Triggers replication on changes
    
- Best practice: add **Global groups**, not users
    

---

## 🔁 Scope Conversion Rules

- Global → Universal (not in another Global)
    
- Domain Local → Universal (no Domain Local members)
    
- Universal → Domain Local (no restriction)
    
- Universal → Global (no Universal members)
    

---

## 🏗️ Built-in vs Custom Groups

- AD ships with many built-in groups
    
- Some are highly privileged (Domain Admins, Enterprise Admins)
    
- Exchange & other apps add privileged groups
    
- Some built-ins prevent group nesting
    

---

## 🪆 Nested Group Membership

Users can inherit rights indirectly.

- Group → Group → Privileged Group
    
- Hard to spot manually
    
- **BloodHound** commonly used to visualize paths
    

⚠️ Nesting frequently causes unintended privilege escalation.

---

## 🏷️ Important Group Attributes

|Attribute|Description|
|---|---|
|**cn**|Group name|
|**member**|Members|
|**memberOf**|Parent groups|
|**groupType**|Type + scope|
|**objectSID**|Security identity|

---

## 📝 Key Takeaways

- Groups = permission engine in AD
    
- Nesting hides privilege paths
    
- Universal groups impact replication
    
- Audits prevent access creep
    
- Enumeration of groups is vital in pentests
    

---
