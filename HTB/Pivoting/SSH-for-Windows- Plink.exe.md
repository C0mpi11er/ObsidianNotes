
# 🛰️ SSH for Windows: Plink.exe Cheat Sheet

> [!info] 🧠 What is Plink.exe Pivoting?
> 
> Bash
> 
> ```
> Windows-native command-line pivoting methodology that utilizes PuTTY Link (plink.exe) to establish dynamic SOCKS proxies over SSH, allowing for stealthy "Living off the Land" lateral movement.
> ```
> 
> 💡 Allows administrators and penetration testers to:
> 
> - **Live Off the Land (LotL):** Leverage legitimate, pre-installed administrative tools (`plink.exe`) on a compromised Windows host to pivot without uploading loud hacking tools.
>     
> - **Establish SOCKS Proxies on Windows:** Replicate Linux-style `ssh -D` capabilities inside native Windows environments (`cmd.exe` or PowerShell).
>     
> - **Tunnel Graphical Applications:** Route standard Windows desktop utilities smoothly into completely isolated internal subnets.
>     
> 
> ✔ Common in:
> 
> - Legacy Windows environment pentests, enterprise Active Directory assessments, and operations where the primary attack host is Windows-based.
>     

> [!tip] 🔧 Core Idea
> 
> Bash
> 
> ```
> Windows Attack/Foothold Host (plink.exe) ➔ SSH Tunnel ➔ Dual-Homed Linux Pivot ➔ Isolated Subnet
> ```
> 
> 💡 Key Architectural Components:
> 
> - **The Proxy Engine (`plink.exe`):** Handles the encrypted SSH transport layer and opens the local proxy gate.
>     
> - **The Traffic Driver (Proxifier):** A graphical Windows utility that acts as the Windows equivalent to `proxychains`, forcing desktop applications into the tunnel.
>     
> 
> ✔ Think of it as **setting up a secure transport lane directly out of a Windows command prompt. If you land on an older or locked-down Windows system where PuTTY is trusted, Plink allows you to spin up a dynamic network bridge while blending in completely with normal administrative traffic**.

# 🌳 Structure: Tunnel Command & Traffic Redirection

> [!info] 📚 Activating the Dynamic Proxy (`plink`)
> 
> Bash
> 
> ```
> plink -ssh -D <Local_SOCKS_Port> <User>@<Pivot_IP>
> ```
> 
> 💡 Function:
> 
> - Establishes a secure SSH connection to the Linux pivot host while simultaneously binding a dynamic SOCKS proxy listener to a local port (e.g., `9050`) on the Windows host.
>     

> [!tip] 📥 Intercepting Desktop Traffic (Proxifier)
> 
> Bash
> 
> ```
> Profile Setup: Proxy Server ➔ 127.0.0.1 : 9050 (SOCKS v4/v5)
> Proxification Rule: Target Application (e.g., mstsc.exe) ➔ Action: Proxy SOCKS4 127.0.0.1
> ```
> 
> 💡 Logic:
> 
> - Because Windows lacks a native command-line tool like `proxychains`, Proxifier acts at the OS level to automatically catch, repackage, and force application packets straight down the Plink listener socket.
>     

# 🔐 Access Methods

|**Pivoting Component**|**Interface Style**|**Configuration Target**|**Operational Purpose**|
|---|---|---|---|
|**Plink Tunnel**|Command Line (`cmd.exe`)|`ubuntu@10.129.15.50`|Builds the initial encrypted transport layer over SSH to the dual-homed host.|
|**Proxifier Engine**|Graphical GUI Profile|`127.0.0.1:9050`|Intercepts Windows system socket calls and forces them into the local proxy gate.|
|**Native Client (`mstsc.exe`)**|Graphical Desktop Interface|`Internal_Target_IP`|Launches a standard remote desktop session to a hidden target without generating anomalous tool alerts.|

# 🔍 Footprinting & Enumeration

> [!success] 📡 Initializing the Windows Pivot Line
> 
> Bash
> 
> ```
> # 1. Open cmd.exe and initialize the dynamic SOCKS proxy over the remote Linux gateway
> C:\> plink -ssh -D 9050 ubuntu@10.129.15.50
>  
> # 2. Verify the local port is actively holding an established socket loop open
> C:\> netstat -ant | findstr 9050
> # Expected: TCP    127.0.0.1:9050    0.0.0.0:0    LISTENING
> ```
> 
> 💡 Flag breakdown & execution limits:
> 
> - `-ssh` forces Plink to invoke the Secure Shell protocol for session transport.
>     
> - `-D 9050` establishes the dynamic local port routing configuration.
>     
> - **Interactive Note:** Plink will prompt to cache the server's host key on the first connection. If running non-interactively, scripts can freeze unless configured properly.
>     

> [!info] 🔍 Information Revealed
> 
> - **Trusted Application Presence:** Finding `plink.exe` or `putty.exe` inside administrative directories (`C:\Program Files\PuTTY\`) identifies an immediate, signature-free network pivoting vector.
>     
> - **Proxied Session Verification:** Checking Proxifier's live connection logs displays application streams (e.g., `mstsc.exe` matching target `172.16.5.19`), confirming the tunnel layer is processing data properly.
>     

# ⚠️ Common Misconfigurations

> [!danger] 🔓 Registry Cache Hangs & Missing OS Proxification Rules
> 
> Bash
> 
> ```
> Host Key Verification: Plink halts non-interactively waiting for an "Are you sure you want to trust this host" prompt
> Blind Application Drops: Executing GUI tools directly without defining an active Proxifier redirection policy
> ```
> 
> 💡 Impact:
> 
> - **Execution Stalls:** If Plink is dropped into a blind automated script or web exploit shell, missing the trusted host key registry entry will cause the execution process to hang infinitely in the background.
>     
> - **Tunnel Evasion Errors:** Outbound application queries (like RDP connections) will attempt to exit your machine directly instead of routing through the proxy, causing immediate routing errors or exposing your true IP.
>     

# 💥 Protocol-Specific Attacks

> [!warning] ⚙️ Setting Up the Proxifier SOCKS Map
> 
> Bash
> 
> ```
> 1. Open Proxifier ➔ Profile ➔ Proxy Servers ➔ Add.
> 2. Set Address to '127.0.0.1', Port to '9050', and Protocol to 'SOCKS Version 4' (or 5).
> 3. Profile ➔ Proxification Rules ➔ Add ➔ Target Hosts: Specify your isolated target subnet range (e.g., 172.16.5.*).
> ```
> 
> 💡 Impact:
> 
> - Hooks into the Windows network stack seamlessly. Any desktop application matching your rule behaves as though it is physically sitting inside the internal network subnet.
>     

> [!danger] 🎯 Launching Native Windows RDP Across the Pivot
> 
> Bash
> 
> ```
> # Launch the standard Windows Remote Desktop client directly from the run menu
> C:\> mstsc.exe
> ```
> 
> 💡 Risk:
> 
> - Allows you to input an internal IP address (e.g., `172.16.5.19`) directly into the standard connection box.
>     
> - Proxifier intercepts the thread, packages the session, slides it through Plink's open port 9050, and cleanly projects the internal server's GUI onto your local desktop interface.
>     

# ⚡ Real-World Workflow

> [!success] 🧠 Attack Flow
> 
> Bash
> 
> ```
> 1. Enumerate a compromised Windows host to look for pre-existing administrative PuTTY installations or file share copies.
> 2. Execute plink.exe via 'cmd.exe' using the '-D 9050' flag to link back to a known intermediate Linux pivot server.
> 3. Accept the remote host key validation token to open up the active local port listener.
> 4. Initialize Proxifier on your attack setup and bind a profile server explicitly matching loopback port 9050.
> 5. Build a custom proxification rule targeting the discovered internal isolated enterprise network spaces.
> 6. Run native administrative tools like 'mstsc.exe' to execute graphical control overrides on internal hidden assets.
> ```
> 
> 💡 Always review host logs; utilizing an existing administrative tool path like Plink drastically minimizes the generation of high-severity endpoint alerts.

# 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Bash
> 
> ```
> plink.exe            → Laying down a hidden railway line directly out of a Windows command prompt terminal
> Proxifier            → An automatic train switch that hooks specific apps and forces them onto the line
> mstsc.exe (RDP)      → The passenger train traveling securely down the line to visit a hidden internal room
> Linux Pivot Host     → The switching terminal where the train exits safely into the isolated corporate valley
> ```
> 
> 💡 When auditing locked-down environments, do not deploy noisy or untrusted tool sets. Live off the land using Plink to establish your core proxy, and let Proxifier steer your standard administrative utilities cleanly into the target network.