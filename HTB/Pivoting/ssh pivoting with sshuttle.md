
# 🛰️ SSH Pivoting with Sshuttle Cheat Sheet

> [!info] 🧠 What is Sshuttle?
> 
> Bash
> 
> ```
> Python-based data transport utility that achieves transparent network pivoting by automatically manipulating local kernel routing tables over a standard SSH session.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Eliminate Proxychains Dependency:** Remove the requirement to prefix commands with `proxychains` or manually modify application-level proxy settings.
>     
> - **Automate Firewall Routing:** Dynamically write temporary `iptables`/`nftables` rules onto your local attack machine at the operating system layer.
>     
> - **Achieve Full Tool Interoperability:** Run any standard security testing utility, browser, or script natively against hidden internal subnets.
>     
> 
> ✔ Common in:
> 
> - Rapid network infrastructure profiling, red team situational awareness phases, and any Linux-to-Linux pivot layout.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Hacking Application ➔ Native Linux Kernel (iptables) ➔ Sshuttle Tunnel ➔ Pivot Host ➔ Internal Network
> ```
> 
> 💡 Key Operational Advantage:
> 
> - **The Transparent VPN Experience:** Instead of opening a local SOCKS port that tools must be forced into, Sshuttle captures all outbound traffic bound for the target subnet globally at the network stack layer.
>     
> 
> ✔ Think of it as **instantly spinning up a software-defined VPN connection using nothing but a standard user-level SSH account. Your system firewall transparently hooks, packages, and routes your network queries down the pipe automatically**.

# 🌳 Structure: Kernel Modification & Bare Tool Execution

> [!info] 📚 Initializing the Gateway Tunnel (`sshuttle`)
> 
> Bash
> 
> ```
> sudo sshuttle -r <User>@<Pivot_IP> <Internal_Target_Subnet> -v
> ```
> 
> 💡 Function:
> 
> - Authenticates to the intermediate pivot point via SSH, drops an active routing controller, and spins up a local kernel redirector daemon on loopback port `12300`.
>     

> [!tip] 📥 Unwrapped Internal Targeting
> 
> Bash
> 
> ```
> # Execute scans directly against internal assets without proxychains wrappers
> sudo nmap -v -A -sT -p3389 172.16.5.19 -Pn
> ```
> 
> 💡 Logic:
> 
> - Because your operating system's local routing rules are altered, applications interact with hidden internal IPs natively. Traffic is seamlessly hooked and shipped down the SSH stream behind the scenes.
>     

# 🔐 Access Methods

|**Routing Protocol**|**Execution Context**|**System Requirements**|**Primary Limitation**|
|---|---|---|---|
|**Standard Sshuttle Layer**|`sudo sshuttle -r user@ip subnet`|Requires `sudo` root rights on the **Attack Machine** only.|Strictly limited to SSH transport lines; cannot tunnel over TOR or HTTPS proxies.|
|**TCP Handling (Default)**|Automatic state routing|Relies on a working Python 3 installation on both endpoints.|Forwards TCP traffic exclusively unless additional arguments are passed.|
|**UDP / DNS Extension**|`--udp` or `--dns` flags included|Requires explicit kernel capability match on the host routing engine.|Can introduce overhead or latency delays on high-throughput discovery streams.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Activating the Transparent Network Bridge
> 
> Bash
> 
> ```
> # 1. Establish the global network intercept tunnel from your attack machine
> 0xtheta@htb[/htb]$ sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 -v
>  
> # 2. Run naked fingerprint scans directly against target nodes in a separate terminal window
> 0xtheta@htb[/htb]$ sudo nmap -sT -p3389 172.16.5.19 -Pn
> ```
> 
> 💡 Flag breakdown & environmental behaviors:
> 
> - `-r` targets the intermediate SSH authentication pivot node interface.
>     
> - `172.16.5.0/23` defines the precise target network mask to capture.
>     
> - `fw: iptables... -A sshuttle-12300 -j REDIRECT --dest 172.16.5.0/32 -p tcp --to-ports 12300` logs show Sshuttle globally redirecting matching traffic into the local handler thread.
>     

> [!info] 🔍 Information Revealed
> 
> - **Clean Banner Responses:** Scanning yields detailed, uncorrupted service fingerprints (e.g., `Microsoft Terminal Services`, product build `10.0.17763`) because tools interact cleanly with standard TCP sockets.
>     
> - **Domain Architecture Context:** Pulls extended NTLM attributes (`Target_Name: INLANEFREIGHT`, `DNS_Computer_Name: DC01.inlanefreight.local`) across network perimeters reliably.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Missing Local Privilege Rights & Invalid Nmap Scan Flags
> 
> Bash
> 
> ```
> Execution Failure: Attempting to launch sshuttle without local 'sudo' root permission properties
> Protocol Packet Corruptions: Executing raw stealth SYN sweeps ('-sS') or UDP requests through a standard TCP tunnel
> ```
> 
> 💡 Impact:
> 
> - **Immediate Daemon Refusals:** Because the application must directly re-engineer your host's local system packet filter tables (`iptables`), standard unprivileged user accounts will drop out with a permission denied block.
>     
> - **Empty Scan Results:** Sshuttle operates via a user-space proxy tunnel hook. If you attempt an Nmap stealth SYN scan (`-sS`), the socket-level requirements fail, generating completely blank or corrupted output sets.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Executing Automated Multi-Tool Infiltration
> 
> Bash
> 
> ```
> # With Sshuttle running in the background, interact with targets natively across multiple windows
> 0xtheta@htb[/htb]$ xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
> 0xtheta@htb[/htb]$ curl http://172.16.5.25:8080/internal-portal/
> ```
> 
> 💡 Impact:
> 
> - Permits smooth workflow context switching. You can run graphical RDP connections (`xfreerdp`), check web apps (`curl`), or spin up exploitation tools simultaneously without configuring separate proxy config block files.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Compromise an internet-facing server and acquire valid SSH access parameters.
> 2. Document the hidden network subnets tied to the host's secondary network cards using standard host commands.
> 3. Verify Python 3 exists on both your local infrastructure setup and the remote pivot server node.
> 4. Initialize Sshuttle as root on your machine, assigning it to bridge and capture the target subnet scope.
> 5. Input user credentials to bind the local system iptables rules directly to loopback port 12300.
> 6. Open a fresh terminal array and execute raw security tools natively against internal machines without using proxychains.
> ```
> 
> 💡 Always make sure to gracefully kill the Sshuttle process thread (`Ctrl+C`) when closing your engagement to clean up and remove lingering iptables redirect rules from your attack machine.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> Standard SSH Proxy (-D) → Forcing your individual tools to wear a badge and travel down a manual transit lane
> Sshuttle Tunnel         → Globally paving a brand new automated highway system across the entire operating system
> Local iptables Rules    → Traffic control signs that instantly catch and divert target vehicles onto the new highway
> Full TCP Scan (-sT)     → Driving a standard passenger car through the tunnel to complete a full handshake delivery
> ```
> 
> 💡 If you are working out of a Linux attack platform and want a seamless, VPN-like pivoting experience that eliminates proxy config files, deploy Sshuttle to route your entire network layer.