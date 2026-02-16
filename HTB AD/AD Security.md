## 🧠 AD Security Philosophy

- AD is **insecure by default** → prioritizes **Availability & Confidentiality** over Integrity.
    
- Must be hardened using built-in Microsoft controls + defense-in-depth.
    
- Security balances the **CIA Triad**:
    
    - **Confidentiality** – access control
        
    - **Integrity** – data trust
        
    - **Availability** – uptime/resources
        

---

## 🛡️ General Hardening Measures

### 🔄 LAPS

- Rotates local admin passwords automatically.
    
- Reduces lateral movement risk.
    

### 📊 Logging & Audit Policies

- Detect:
    
    - Password spraying
        
    - Kerberos attacks
        
    - Unauthorized object changes
        
    - Privilege abuse
        
- Use **Advanced Audit Policy Configuration**.
    

---

## ⚙️ Group Policy Security

Applied via GPOs:

- **Account Policies** – passwords, lockouts, Kerberos tickets.
    
- **Local Policies** – user rights, auditing, device control.
    
- **Software Restriction Policies** – control executable use.
    
- **Application Control / AppLocker** – restrict PowerShell, CMD, scripts.
    
- **Advanced Auditing** – log file access, logons, policy changes.
    

---

## 🩹 Patch Management

- **WSUS** – free Windows patching.
    
- **SCCM** – enterprise patching + automation.
    
- Ensures no vulnerable hosts remain unpatched.
    

---

## 🤖 gMSA (Group Managed Service Accounts)

- Domain-managed service accounts.
    
- 120-character rotating passwords.
    
- No human knows the password.
    
- Safe for multi-host services.
    

---

## 👥 Security Groups

- Assign permissions **via groups**, not users.
    
- Examples:
    
    - Domain Admins
        
    - Backup Operators
        
    - Account Operators
        
- Supports least privilege.
    

---

## 🔑 Account Separation

- Admins must use:
    
    - **Standard account** → daily work.
        
    - **Privileged account** → admin tasks.
        
- Prevents credential theft from compromised machines.
    

---

## 🔐 Passwords & MFA

- Use:
    
    - Passphrases (12+ chars minimum)
        
    - Password filters
        
    - MFA for RDP
        
- Avoid weak but “complex” passwords (e.g., `Welcome1`).
    

---

## 🚫 Limit Domain Admin Usage

- Only log into **Domain Controllers**.
    
- Never workstations or servers.
    
- Prevents credential dumping.
    

---

## 🧹 Remove Stale Objects

- Disable unused:
    
    - Users
        
    - Service accounts
        
    - Computers
        
- Old accounts = easy attack paths.
    

---

## 🔍 Permission Audits

- Review:
    
    - Local admin memberships
        
    - Domain Admin count
        
    - File shares
        
    - Privileged groups
        

---

## 📜 Restricted Groups

- Enforce group membership via GPO.
    
- Control:
    
    - Local admins
        
    - Schema Admins
        
    - Enterprise Admins.
        

---

## 🧱 Role Separation

- Never install IIS or apps on DCs.
    
- Separate:
    
    - Web servers
        
    - DB servers
        
    - Mail servers.
        

---

## 🖥️ Limit Local Admin & RDP

- No broad Domain Users admin access.
    
- Restrict RDP carefully.
    
- Reduces lateral movement and credential theft.