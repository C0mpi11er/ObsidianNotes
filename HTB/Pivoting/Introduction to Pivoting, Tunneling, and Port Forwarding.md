

![[PivotingandTunnelingVisualized.gif]]

# 🛰️ SSH Port Forwarding & SOCKS Tunneling Cheat Sheet

> [!info] 🧠 What is Port Forwarding & Pivoting?
> 
> Bash
> 
> ```
> Network encapsulation technique used to redirect a communication request from one port to another
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - Bypass firewalls and boundary filters by wrapping restricted communication layers inside allowed streams (e.g., SSH).
>     
> - Route interaction pipelines seamlessly through a compromised dual-homed host to reach isolated target subnets.
>     
> - Interact directly with remote services configured to only accept incoming local requests (`localhost`).
>     
> 
> ✔ Common in:
> 
> - Post-exploitation network environments, lateral movement workflows, and remote data administration pipelines.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Attack Host ↔ Compromised Pivot Host (SSH Layer) ↔ Hidden Internal Network Target
> ```
> 
> 💡 Key Ports & Channels:
> 
> - **TCP 22** → SSH Tunneling Backbone (Establishes the initial secure transport session used to bridge internal networks).
>     
> - **SOCKS 4/5** → Dynamic Routing Channel (Non-application layer protocol that acts as an adjustable proxy gateway).
>     
> 
> ✔ Think of it as **building a secure, hidden extension cord out of a compromised machine, allowing your local hacking tools to safely access rooms inside the network that they cannot see directly**.

# 🌳 Structure: Static Map vs. Dynamic Tunneling

> [!info] 📚 SSH Local Port Forwarding (`-L`)
> 
> Bash
> 
> ```
> ssh -L <Local_Port>:<Target_IP>:<Target_Port> <User>@<Pivot_IP>
> ```
> 
> 💡 Function:
> 
> - Opens an active local interface port to bind directly to a single explicit service hosted on the internal network segment.
>     

> [!tip] 📥 SSH Dynamic Port Forwarding (`-D`)
> 
> Bash
> 
> ```
> ssh -D <Local_SOCKS_Port> <User>@<Pivot_IP>
> ```
> 
> 💡 Logic:
> 
> - Turns your local machine into a SOCKS proxy server. Rather than pointing to a single pre-defined service, it routes traffic to any endpoint requested by your applications on the fly.
>     

# 🔐 Access Methods

|**Pivoting Method**|**Configuration Parameters**|**Risk / Flexibility**|**Notes**|
|---|---|---|---|
|**Static Local Forwarding**|`-L 1234:localhost:3306`|**LOW / RIGID**|Excellent for accessing individual known targets (e.g., exposing a local MySQL database instance).|
|**Multi-Port Static Mapping**|`-L 1234:localhost:3306 -L 8080:localhost:80`|**MEDIUM / MODULAR**|Allows multiple individual pipelines to pass over a single active SSH host connection simultaneously.|
|**Dynamic SOCKS Proxying**|`-D 9050`|**HIGH / DYNAMIC**|Turns the pivot point into an agile local VPN gateway; mandatory for exploratory subnet mapping.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration Tools
> 
> Bash
> 
> ```
> # 1. Identify active internal subnets on the compromised host
> ifconfig   # (Look for hidden network interfaces like ens224 pointing to 172.16.5.0/23)
> 
> # 2. Establish the local SOCKS listener connection
> ssh -D 9050 ubuntu@10.129.202.64
> 
> # 3. Map out a completely hidden subnet via Proxychains and Nmap
> proxychains nmap -v -Pn -sT 172.16.5.1-200
> ```
> 
> 💡 Flag breakdown:
> 
> - `-sT` **(Strict Enforcement):** Mandates a full TCP Connect Scan. Proxychains works at the application/socket layer; it cannot interpret partial packets or half-open states (like stealth `-sS` scans) and will return invalid data.
>     
> - `-Pn` **(Strict Enforcement):** Skips the initial ICMP ping sweep, preventing false negatives against Windows targets whose firewalls block pings by default.
>     

> [!info] 🔍 Information Revealed
> 
> - **Internal Route Assets:** Identifies active IP blocks hidden entirely behind NAT or external firewall perimeters.
>     
> - **Exposed Internal Ports:** Pinpoints network protocols listening deep inside the corporate workspace (e.g., RDP `3389`, SMB `445`).
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Dual-Homed Server Configurations & Loose SSH Configurations
> 
> Bash
> 
> ```
> Networking Architecture: Server possesses multiple active network cards (NICs) across isolated zones
> User Shell Rights: Compromised server accounts retain unrestricted SSH tunnel capabilities
> ```
> 
> 💡 Impact:
> 
> - **Lateral Network Breach:** Exposes the entire unrouted internal architecture of a company to external scanning routines if any single public-facing asset falls.
>     
> - **Traffic Evasion:** Allows anomalous, hostile exploit payloads to sail silently through standard perimeter defense monitoring points undetected by masquerading as standard internal host traffic.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Executing Local Services Traffic Extraction
> 
> Bash
> 
> ```
> ssh -L 1234:localhost:3306 ubuntu@10.129.202.64
> nmap -v -sV -p1234 localhost
> ```
> 
> 💡 Impact:
> 
> - Exposes backend services restricted strictly to local interfaces (`127.0.0.1:3306`) out to your local attack environment.
>     
> - Enables complex remote payload execution or brute forcing on applications without requiring you to compile or upload heavy files directly onto the compromised production host.
>     

> [!danger] 🎯 Interrogating Internal Hosts through SOCKS via Metasploit
> 
> Bash
> 
> ```
> # 1. Route your framework utility explicitly through your configured /etc/proxychains.conf parameters
> proxychains msfconsole
> 
> # 2. Launch your desired exploit or scanning modules against the hidden internal targets
> msf6 > use auxiliary/scanner/rdp/rdp_scanner
> msf6 auxiliary(rdp_scanner) > set rhosts 172.16.5.19
> msf6 auxiliary(rdp_scanner) > run
> ```
> 
> 💡 Risk:
> 
> - Drives complex scanning algorithms seamlessly over the SOCKS tunnel network boundary.
>     
> - Extracts targeted OS build and service confirmation strings (e.g., finding an unpatched Active Directory Domain Controller at `172.16.5.19`).
>     

> [!danger] 🔑 Interactive Remote Desktop Takeover (RDP)
> 
> Bash
> 
> ```
> proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
> ```
> 
> 💡 Risk:
> 
> - Bypasses perimeter blocks to capture and project a fully operational graphical GUI environment out from an isolated server.
>     
> - Masks the physical source signature of the attacker, ensuring the targeted host's security log files only note an access entry from the local pivot machine.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Run an Nmap scan to discover open SSH access (Port 22) on an internet-facing machine.
> 2. Compromise the host and review active network interfaces using 'ifconfig' to find hidden interior subnets.
> 3. Establish an adjustable tunnel listener connection ('ssh -D 9050') back to your attack setup.
> 4. Update the local '/etc/proxychains.conf' text file to forward traffic requests into local port 9050.
> 5. Direct network utilities using 'proxychains nmap -Pn -sT <Internal_IP_Subnet>' to identify isolated target endpoints.
> 6. Move laterally by executing GUI software or exploitation frameworks prefixed with 'proxychains' to compromise internal targets.
> ```
> 
> 💡 Always configure Proxychains to run full TCP scans (`-sT`) without ping checks (`-Pn`) to avoid getting inaccurate or completely empty scan outputs.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Local Forwarding (-L)    → A single-lane pipeline feeding a specific target from an internal room
> Dynamic Forwarding (-D)  → An unrestricted master keycard to navigate all floors in the target facility
> Proxychains              → The courier forcing your tools to use the card to complete deliveries
> Full TCP Scan (-sT)      → Knocking on a door completely, waiting for an answer, and shaking hands
> ```
> 
> 💡 If you only need to interact with a single local target service, build a pipeline (`-L`). If you want to map and move throughout the entire building interior, deploy a gateway proxy (`-D`).