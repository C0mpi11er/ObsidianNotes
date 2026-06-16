# 🌐 Active Directory Password Policy Enumeration Cheat Sheet

> [!info] 🧠 What is Password Policy Enumeration?
> 
> Bash
> 
> ```
> Extracting Active Directory authentication rules before launching credential attacks.
> ```
> 
> 💡 Knowing the policy guarantees you do not cause **Denial of Service (DoS)** by locking out thousands of corporate production users. You map out exactly:
> 
> - How short a password can be (`min_pw_length`).
>     
> - How many bad attempts trigger a lockout (`lockout_threshold`).
>     
> - How long you must wait between horizontal sprays (`lockout_duration`).
>     

# 🐧 1. Linux Credentialed Enumeration

When you have captured or been provided a single set of valid low-privileged domain credentials, query the domain controller directly over SMB.

> [!success] 🎯 Remote Policy Extraction via CrackMapExec (CME)
> 
> Bash
> 
> ```
> crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
> ```
> 
> 💡 Quick, automated, and parses the binary flags into a clear, readable breakdown right in your terminal.

# 🔓 2. Linux Unauthenticated Enumeration (Null Sessions)

Legacy or upgraded Domain Controllers sometimes permit unauthenticated users to establish connections to the **IPC$ (Inter-Process Communication)** share or bind anonymously to **LDAP**.

> [!tip] 🔗 Method A: RPC Client Null Session Interception
> 
> Bash
> 
> ```
> # 1. Establish an unauthenticated connection layer (Empty user and password strings)
> rpcclient -U "" -N 172.16.5.5
> 
> # 2. Query basic domain metadata
> rpcclient $> querydominfo
> 
> # 3. Dump specific password complexity restrictions
> rpcclient $> getdompwinfo
> ```
> 
> 💡 Useful when running surgical queries without generating massive network noise.

> [!success] 🤖 Method B: Automated Mapping via enum4linux-ng
> 
> Bash
> 
> ```
> enum4linux-ng -P 172.16.5.5 -oA ilfreight
> ```
> 
> 💡 Combines multiple backend utilities (`net`, `rpcclient`, `smbclient`) into a unified wrapper scan. The `-oA` flag dumps the entire environment structure directly to structured JSON/YAML targets.

> [!tip] 🧬 Method C: Directory Harvesting via LDAP Anonymous Bind
> 
> Bash
> 
> ```
> ldapsearch -H ldap://172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
> ```
> 
> 💡 Scrapes Active Directory schema configuration objects directly from the raw database loop. Look for `lockoutThreshold` and `minPwdLength`.

# 🪟 3. Windows Native Enumeration

If your testing architecture places you directly onto an internal Windows host or a virtual machine session provided by the client, use built-in tools or memory-resident scripts to evade Endpoint Detection (EDR).

> [!info] 🔎 Living off the Land (Zero-Tool Footprint)
> 
> DOS
> 
> ```
> net accounts
> ```
> 
> 💡 Requires no external compilation or file transfers. Instantly prints lockout metrics to the command prompt console window.

> [!success] ⚡ Memory-Resident Recon via PowerView
> 
> PowerShell
> 
> ```
> Import-Module .\PowerView.ps1; Get-DomainPolicy
> ```
> 
> 💡 Pulls comprehensive Group Policy Objects (GPOs) from the domain `sysvol` architecture, detailing domain-wide rules including Kerberos ticket lifetimes.

# 🧠 Deciphering the Policy Target Model

Once you run your tools, analyze the raw values returned from `INLANEFREIGHT.LOCAL` to build your defensive execution structure:

YAML

```
Minimum Password Length: 8          # Weak baseline. "Welcome1" or "Spring2026!" satisfies this constraint.
Account Lockout Threshold: 5        # Critical Warning: You have exactly 4 attempts before account lock.
Lockout Observation Window: 30 min  # Time baseline. Bad logon counters reset after half an hour.
Lockout Duration: 30 minutes        # Auto-unlock interval. The target clears itself without admin interaction.
Password Complexity: Enabled        # Requires combining 3/4 item types (Upper, Lower, Number, Special).
```

# ⚡ The Operational Spray Formula

> [!quote] 🎯 Safe Spray Execution Map
> 
> Plaintext
> 
> ```
> Policy Threshold = 5 ──> Attack Strategy: Limit to 2 attempts per user cycle.
> Observation Window = 30 min ──> Cooldown Strategy: Wait 31+ minutes before re-spraying.
> ```
> 
> 💡 By staying well below the target metrics, you guarantee the attack remains entirely silent and operationally safe.

> [!warning] ⚠️ The "No-Policy" Blind Rule
> 
> Bash
> 
> ```
> When the password policy cannot be extracted, execute a "Hail Mary" strategy.
> ```
> 
> ❌ **Never blind spray rapidly.** Assume a strict threshold layout (e.g., limit of 3 failed attempts). Run exactly **one targeted iteration**, then immediately pivot or wait several hours to ensure the environment completely flushes security tracking logs.

# 🧩 Mental Model

> [!quote] 🎯 The Policy Pipeline Flow
> 
> Bash
> 
> ```
> FOOTPRINT ESTABLISHED → POLICY ENUMERATION → EXTRACT METRICS → CALCULATE SPRAY GAP → EXECUTE SAFE ATTACK
> ```

