---

### What is SID History?

**SID History** is an extra field on a user account that stores **old SIDs**.

✅ Legitimate use:

- During **Active Directory migrations**
    
- A user moves to a new domain
    
- Keeps access to old resources using their **old SID**
    

🚨 Abuse case (attacker):

- Add **powerful SIDs** (like Domain Admin) to SID History
    
- User _looks_ normal
    
- But acts like a Domain Admin
    

---

## Why SID History Is Dangerous

When a user logs in:

- Windows builds a **security token**
    
- This token includes:
    
    - User SID
        
    - Group SIDs
        
    - **SID History SIDs**
        

👉 Windows **does not care** where the SID came from  
👉 If the SID exists in the token, **permissions are granted**

---

## How Attackers Abuse SID History

### The Core Trick

> Add the **Domain Admin SID** to the **SID History** of a low-privileged account.

Result:

- Account is **not** in the Domain Admins group
    
- But **has Domain Admin powers**
    

Very stealthy.

---

## Privileges Required

- Requires **Domain Admin** or equivalent access
    
- This is a **persistence technique**, not initial access
    

---

## Why This Is Sneaky Persistence

- Account remains in **Domain Users**
    
- No visible group membership changes
    
- Standard tools don’t alert on this
    
- Privileges only appear **after login**
    

Even worse:

- You can inject **Enterprise Admin SID**
    
- That gives admin access to **every domain in the forest**
    

---

## Forging SID History (What Happened in the Lab)

### Step 1: Confirm No SID History

`Get-ADUser aaron.jones -Properties sidhistory`

➡ SIDHistory is empty

---

### Step 2: Get Domain Admin SID

`Get-ADGroup "Domain Admins"`

Example:

`S-1-5-21-...-512`

`512` = Domain Admins

---

### Step 3: Modify AD Database Directly

Why?

- Mimikatz can no longer patch this reliably
    
- SID History is stored in **ntds.dit**
    

Process:

1. Stop AD service
    
2. Patch `ntds.dit`
    
3. Restart AD service
    

`Stop-Service ntds -Force Add-ADDBSidHistory -SamAccountName aaron.jones -SidHistory <DA SID> -DatabasePath C:\Windows\NTDS\ntds.dit Start-Service ntds`

⚠️ NTDS must be restarted or the domain breaks

---

### Step 4: Verify Privilege Escalation

`dir \\thmdc.za.tryhackme.loc\c$`

✅ Access granted  
➡ Low-privileged user now behaves like **Domain Admin**

---

## Why Blue Team Hates This

### Detection Problems

- User is **not in Domain Admins**
    
- No obvious alerts
    
- SID History only applies **after login**
    
- Requires manual attribute inspection
    

### Removal Problems

- SID History is **protected**
    
- Cannot be removed via GUI
    
- Requires:
    
    - AD-RSAT
        
    - Careful SID history cleanup
        
- Easy to miss during incident response
    

---

## Why This Beats Other Persistence Methods

|Technique|Removed by Rotation?|Stealth|
|---|---|---|
|Passwords|✅ Yes|Low|
|NTLM Hash|✅ Yes|Medium|
|Golden Tickets|⚠️ Partial|Medium|
|Certificates|❌ No|High|
|**SID History**|❌ No|**Very High**|

---

## Key Takeaways (Memorize These)

- SID History grants **real privileges**
    
- Group membership is **not required**
    
- Works even after:
    
    - Password rotation
        
    - Ticket resets
        
    - CA rebuilds
        
- Extremely hard to detect
    
- Perfect **post-compromise persistence**
    
- Blue team nightmare
    

---

## One-Sentence Summary

> **SID History lets a normal user secretly carry Domain Admin powers inside their login token.**