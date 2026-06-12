# 🛰️ Port Forwarding with Windows Netsh Cheat Sheet

> [!info] 🧠 What is Netsh PortProxy?
> 
> Bash
> 
> ```
> Built-in Windows administrative routing mechanism that sets up kernel-level IPv4/IPv6 port relays, enabling seamless "Living off the Land" port forwarding without external binaries.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Avoid Endpoint Detection:** Create a fully functional network relay using a legitimate, pre-installed Windows operating system binary (`netsh.exe`).
>     
> - **Expose Internal Resources:** Connect to hidden internal assets (like domain controllers or internal servers) by routing traffic through a compromised workstation interface.
>     
> - **Maintain Persistent Bridges:** Establish forwarding rules that natively write to the system registry, allowing the pivot to survive system reboots.
>     
> 
> ✔ Common in:
> 
> - Phishing/social engineering post-exploitation phases, highly monitored Active Directory enterprise networks, and locked-down administrative workstations.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Attack Host (Client tool) ➔ Workstation Listen Port (Netsh Proxy) ➔ Internal Subnet Target Port
> ```
> 
> 💡 Key Operational Advantage:
> 
> - **No Uploaded Artifacts:** Unlike `plink.exe` or `socat.exe`, Netsh requires zero files to be introduced to the victim disk, allowing operators to bypass strict AppLocker or Application Whitelisting (AWL) policies.
>     
> 
> ✔ Think of it as **programming a built-in traffic sign inside the Windows network stack. You tell the compromised host to watch one of its own ports and instantly throw any incoming traffic straight over the network wall to a completely different internal IP**.

# 🌳 Structure: Port Relays & Configuration Queries

> [!info] 📚 Creating the Port Proxy Rule (`netsh`)
> 
> Bash
> 
> ```
> netsh.exe interface portproxy add v4tov4 listenport=<Port> listenaddress=<Pivot_IP> connectport=<Target_Port> connectaddress=<Target_IP>
> ```
> 
> 💡 Function:
> 
> - Binds an active IPv4 listener to an external-facing interface port on the pivot machine, pointing it directly at an isolated target network service.
>     

> [!tip] 📥 Querying and Reviewing the Relay State
> 
> Bash
> 
> ```
> netsh.exe interface portproxy show v4tov4
> ```
> 
> 💡 Logic:
> 
> - Prints out the running network routing table explicitly inside the shell window, confirming exactly which ports are currently bridged across interfaces.
>     

# 🔐 Access Methods

|**Pivoting Element**|**Command Parameter**|**Execution Context**|**Operational Purpose**|
|---|---|---|---|
|**Listener Setup**|`listenport=8080`|External Attack Face|Opens up an entry socket on the compromised workstation to accept tool input.|
|**Forward Destination**|`connectaddress=172.16.5.25`|Internal Private Subnet|Defines the hidden network target that the workstation will speak to on your behalf.|
|**Target Service Port**|`connectport=3389`|Application Protocol|The specific service port being targeted on the hidden machine (e.g., RDP).|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Activating and Testing the Native Relay
> 
> Bash
> 
> ```
> # 1. Set up the local portproxy relay rule (Requires Administrative Command Prompt)
> C:\Windows\system32> netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.15.150 connectport=3389 connectaddress=172.16.5.25
>  
> # 2. Launch your attack utility from your machine, targeting the workstation's open port
> 0xtheta@htb[/htb]$ xfreerdp /v:10.129.15.150:8080 /u:victor /p:pass@123
> ```
> 
> 💡 Architectural & permission requirements:
> 
> - **Administrative Execution:** You must possess high-integrity administrative privileges (`nt authority\system` or local administrator) on the Windows workstation to alter `portproxy` rules.
>     
> - **IP Helper Dependencies:** Netsh portproxy relies directly on the native Windows **IP Helper (`iphlpsvc`)** service being active in the background.
>     

> [!info] 🔍 Information Revealed
> 
> - **Legitimate Interface Maps:** Querying `show v4tov4` displays clear, explicit mappings of internal addresses, detailing exactly where traffic is being funneled.
>     
> - **Trusted Network Context:** The internal destination server sees an incoming connection originating from the **workstation's internal IP address** (`172.16.5.19`), masking the external attacker signature behind a trusted asset profile.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Local Firewall Obstacles & Orphaned Persistent Rules
> 
> Bash
> 
> ```
> Host Firewall Block: The Windows Defender firewall drops incoming connection traffic hitting the designated 'listenport'
> Stale Registry Records: Forgetting to delete rules after an engagement leaves an active, unauthenticated backdoor open
> ```
> 
> 💡 Impact:
> 
> - **Silent Connection Timeouts:** Even if the Netsh configuration is correct, the local Windows firewall will block incoming connections on port `8080` unless an explicit allowance rule is added:
>     
>     DOS
>     
>     ```
>     netsh advfirewall firewall add rule name="Port Relay" protocol=TCP dir=in localport=8080 action=allow
>     ```
>     
> - **Persistent Security Exposures:** Netsh configurations write directly to the registry and survive reboots. To clean up and close the relay link securely, operators must manually purge the configuration:
>     
>     DOS
>     
>     ```
>     netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=10.129.15.150
>     ```
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Evading Application Whitelisting via Local Host Relays
> 
> Bash
> 
> ```
> # Target the workstation listener to transparently hit restricted backend infrastructure
> 0xtheta@htb[/htb]$ xfreerdp /v:10.129.15.150:8080 /u:administrator /p:SecurePass123!
> ```
> 
> 💡 Impact:
> 
> - Allows attackers to move laterally into highly locked-down internal zones without importing any external binary exploit tool sets that would trigger defensive monitoring solutions.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Compromise a Windows workstation and check your current privileges; escalate to local administrator if necessary.
> 2. Map local network interfaces using 'ipconfig' to document any adjacent internal target subnets.
> 3. Execute netsh to open an available port on the machine's external interface and map it to an internal target port.
> 4. Verify the registry rule has been applied cleanly using the 'show v4tov4' parameter argument.
> 5. Add a local inbound firewall rule allowing external systems to connect to your designated listening port.
> 6. Launch your local client utilities directly against the workstation interface to access the hidden internal targets.
> 7. Post-assessment: Run the netsh delete command to tear down the tunnel and remove all associated firewall rules.
> ```
> 
> 💡 Always clean up your networking modifications; leaving residual Netsh rules behind alters host system configurations permanently.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Netsh Portproxy       → Stationing a native security guard at a building entrance to forward specific visitors
> listenport Flag       → The entry gate door number that the guard monitors for external traffic arrivals
> connectaddress Flag   → The private internal office address destination where the guard guides visitors
> delete v4tov4         → Safely removing the guard instructions to seal the entrance back up permanently
> ```
> 
> 💡 When dropping external tools onto a Windows disk is too risky, live off the land. Use Netsh to turn the system's own kernel stack into an instant, persistent network bridge.