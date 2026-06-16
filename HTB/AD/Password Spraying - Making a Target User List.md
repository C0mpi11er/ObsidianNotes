# Active Directory: Building a Target User List

> [!info] 🧠 What is Target User List Generation?
> 
> Bash
> 
> ```
> Compiling a verified index of Active Directory usernames prior to launching an authentication spray.
> ```
> 
> 💡 A high-quality user list ensures your password spray hits actual accounts, optimizing engagement time-boxing and minimizing the footprint of unneeded authentication requests.

# ⚙️ Account Sourcing Pipelines

Depending on your access layer (Unauthenticated, Passive, or Credentialed), you extract the target schema using one of four tactical avenues:

### 1. SMB NULL Sessions

- Legacy or sequentially upgraded Domain Controllers may inadvertently allow unauthenticated connection requests to the IPC$ share. This exposes raw directory data blocks containing full user matrices, service accounts, and RIDs.
    

### 2. LDAP Anonymous Binds

- Default settings on legacy architectures (Windows Server 2000/2003) or custom third-party application configurations can leave the LDAP directory open to blank user identification queries.
    

### 3. Kerberos Pre-Authentication Sniffing

- Leverages the protocol exchange behavior of the Kerberos Key Distribution Center (KDC) to distinguish valid accounts from invalid accounts without generating standard login failure alerts.
    

### 4. Credentialed Context Dump

- Utilizing a low-privilege domain account or a Windows host `SYSTEM` token (which impersonates the local domain machine account `COMPUTER$`) to query Active Directory with legitimate authority.
    

# 🗺️ User Enumeration Command Framework

## 🔓 Layer 1: Unauthenticated SMB Data Extraction

> [!success] 📡 Method A: Surgical Extraction via `enum4linux`
> 
> Bash
> 
> ```
> enum4linux -U 172.16.5.5 | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
> ```
> 
> 💡 Queries the domain controller via null shares and strings the raw output down into a single-line configuration file layout.

> [!tip] 🔗 Method B: Manual Session Mapping via `rpcclient`
> 
> Bash
> 
> ```
> # 1. Establish an anonymous pipe session connection
> rpcclient -U "" -N 172.16.5.5
> 
> # 2. Interrogate the PDC Emulator for user objects
> rpcclient $> enumdomusers
> ```

> [!success] 🛠️ Method C: Lockout Auditing via CrackMapExec (CME)
> 
> Bash
> 
> ```
> crackmapexec smb 172.16.5.5 --users
> ```
> 
> 💡 **The Critical Flag:** CME explicitly exposes the `badpwdcount` (current failed attempts) and `badpwdtime` (last failure timestamp).
> 
> > [!warning]
> > 
> > **Defensive Filter Rule:** Scrub your generated target list to remove any account where `badpwdcount` is close to the lockout limit. In multi-DC networks, these counters track independently on each node—verify against the Primary Domain Controller (PDC) Emulator FSMO role to secure accurate totals.

## 🧬 Layer 2: Unauthenticated LDAP Directory Scraping

> [!tip] 🔍 Method A: Raw Native Filtering via `ldapsearch`
> 
> Bash
> 
> ```
> ldapsearch -H ldap://172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))" | grep sAMAccountName: | cut -f2 -d" "
> ```
> 
> 💡 Uses an anonymous bind request (`-x`) to query all schema leaves matching structural user classes.

> [!success] 🤖 Method B: Automated Context Mining via `windapsearch`
> 
> Bash
> 
> ```
> ./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
> ```
> 
> 💡 Automatically maps naming contexts dynamically and extracts full `userPrincipalName` matrices.

## 🔴 Layer 3: Unauthenticated Kerberos Verification

> [!success] 🎯 High-Speed Blind Verification via `kerbrute`
> 
> Bash
> 
> ```
> kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt
> ```
> 
> - **The Mechanism:** Sends a TGT request without pre-authentication data.
>     
>     - If the KDC returns `PRINCIPAL UNKNOWN` $\rightarrow$ The user is invalid.
>         
>     - If the KDC requests pre-authentication $\rightarrow$ The account is confirmed **VALID** without causing a login failure count.
>         
> - **Defensive Trace:** This generates Windows **Event ID 4768** (TGT Requested). Monitoring bursts of this ID allows defenders to identify rapid account harvesting.
>     

## 🟢 Layer 4: Credentialed Extraction Mapping

> [!success] 👑 Full Directory Mining via Legitimate Tokens
> 
> Bash
> 
> ```
> crackmapexec smb 172.16.5.5 -u htb-student -p 'Academy_student_AD!' --users
> ```
> 
> 💡 Bypasses filtering defenses entirely to document the explicit security postures of every account in the forest directory.

# 📊 Logging and Accountability Baseline

When conducting a password spray or user harvest, you must preserve precise operational timelines to support downstream blue team correlation:

Plaintext

```
┌──────────────────────────────────────────────────────────────────────────┐
│                      ENGAGEMENT ENGAGEMENT LOG AUDIT                     │
├──────────────┬──────────────────┬──────────┬───────────┬─────────────────┤
│ Target User  │ Domain Controller│  Time    │   Date    │ Password Tested │
├──────────────┼──────────────────┼──────────┼───────────┼─────────────────┤
│ avazquez     │ 172.16.5.5 (DC01)│ 22:16:11 │ 2026-06-16│ Spring2026!     │
└──────────────┴──────────────────┴──────────┴───────────┴─────────────────┘
```

# 🧠 Key Takeaway

> [!summary]
> 
> Target List Generation = **Safety Filter Operation**
> 
> Building a list is not just about grabbing names—it is about verifying account status. By matching unauthenticated extraction layers with CME data, you can isolate active users, drop accounts already sitting on the verge of lockout, and calibrate your spray delays to remain entirely silent.
