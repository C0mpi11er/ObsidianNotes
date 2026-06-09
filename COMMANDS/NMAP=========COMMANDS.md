---

# 🛠️ Nmap Master Cheat Sheet (Tactical Edition)

---
# 🛠️ Nmap Tactical Field Manual (OSCP / HTB / Red-Team Edition)

# 🧠 Core Operator Mindset

> [!INFO] 🎯 Recon Philosophy
> 
> Bash
> 
> ```
> Enumerate everything.
> Trust nothing.
> Validate assumptions.
> ```
> 
> 💡 Nmap is not “just a scanner.” It is your primary platform for:
> 
> - Attack surface mapping & exposure analysis
>     
> - Service fingerprinting & initial foothold intelligence
>     
> - Active Directory infrastructure discovery
>     
> - Firewall statefulness and defensive behavior analysis
>     

# ⚡ Phase 1 — Host Discovery

## 🔍 Basic Ping Sweep

> [!SUCCESS] 🌐 Discover Live Hosts
> 
> Bash
> 
> ```
> nmap -sn 10.10.10.0/24
> ```
> 
> 💡 Mechanics:
> 
> - Uses localized **ARP + ICMP** requests to identify active targets.
>     
> - Completely skips the port scanning engine for lightning-fast internal network mapping.
>     

## ⚔️ TCP Discovery (When ICMP is Blocked)

> [!TIP] 🧠 Multi-Probe Layer-4 Discovery
> 
> Bash
> 
> ```
> nmap -sn -PE -PS22,80,443,445 -PA3389 -n 10.10.10.0/24
> ```
> 
> 💡 Strategy:
> 
> - Combines ICMP Echo (`-PE`), TCP SYN (`-PS`), and TCP ACK (`-PA`) probes simultaneously.
>     
> - Guarantees high-fidelity target discovery inside stateful, enterprise-firewalled, or VPN environments.
>     

## 👻 Stealthier Discovery (No-ARP / Layer-3 Only)

> [!WARNING] 🕶️ Low-Noise Discovery
> 
> Bash
> 
> ```
> nmap -sn -PE -PS22,80,443 -n --disable-arp-ping <SUBNET>
> ```
> 
> 💡 Tactics:
> 
> - **`-n`**: Strips out noisy, reverse DNS lookups.
>     
> - **`--disable-arp-ping`**: Suppresses localized Layer-2 broadcast storms that alert Blue Teams.
>     

# 🔥 Phase 2 — Full Port Enumeration

## 💣 The Real OSCP Scan

> [!DANGER] 🚀 Full TCP Port Sweep
> 
> Bash
> 
> ```
> sudo nmap -p- --min-rate 5000 --max-retries 2 -Pn -oG scans/all_ports.gnmap <IP>
> ```
> 
> 💡 Strategy:
> 
> - **NEVER** skip full port scans. High-value custom foothills almost always sit outside the standard top 1000 ports.
>     
> - **Keep your eyes peeled for:** Web UI alternates (`8080`, `8443`), remote entryways (`5985`, `5986`, `9001`), or backdoors (`31337`).
>     

## ⚡ Fast Open-Port Discovery

> [!SUCCESS] ⚡ Fast Recon Triage
> 
> Bash
> 
> ```
> nmap --top-ports 1000 --open -n -T4 <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Only displays valid, operational connections (`--open`).
>     
> - Skips host resolution bottlenecks for immediate multi-host scoping.
>     

## 🧠 UDP Enumeration (Often Ignored)

> [!WARNING] ☣️ UDP Recon Operations
> 
> Bash
> 
> ```
> sudo nmap -sU --top-ports 100 --min-rate 1000 -Pn -oN scans/udp_top100.nmap <IP>
> ```
> 
> 💡 Critical Targets:
> 
> - Core network protocols depend entirely on connectionless UDP sockets.
>     
> - Focus heavily on parsing **DNS (`53`)**, **Kerberos (`88`)**, **SNMP (`161`)**, **NFS (`111`/`2049`)**, and routing portals.
>     

# 🧪 Phase 3 — Service Enumeration

## 🔬 Core Enumeration Command

> [!SUCCESS] 🧠 Baseline Verification
> 
> Bash
> 
> ```
> nmap -sC -sV -p <PORTS> <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Fires standard safe scripts (`-sC`) to dynamically parse system components.
>     
> - Inspects service banners (`-sV`) to uncover running software titles and precise version numbers.
>     

## ⚔️ Aggressive Enumeration

> [!WARNING] 🔥 Deep-Dive Fingerprinting
> 
> Bash
> 
> ```
> nmap -A --script-timeout 30s -p <PORTS> <IP>
> ```
> 
> 💡 Payload Profile:
> 
> - Highly active footprint. Enforces OS deduction, traceroute analysis, and extensive scripts simultaneously.
>     
> - **`--script-timeout 30s`** prevents hanging on degraded processes or honeypots.
>     

## 🧠 Reliable OS Detection

> [!TIP] 🪟 Linux vs Windows Verification
> 
> Bash
> 
> ```
> sudo nmap -O --osscan-guess --max-os-tries 2 <IP>
> ```
> 
> 💡 Tactics:
> 
> - Analyzes underlying TCP packet variations (TTL, Window Size, DF bits).
>     
> - Excellent workaround when system banners have been stripped or falsified.
>     

# 🧨 Phase 4 — NSE Scripting Engine

## 🔍 Vulnerability Enumeration

> [!BUG] ☠️ Automated Vulnerability Discovery
> 
> Bash
> 
> ```
> nmap --script vuln --script-timeout 45s -Pn <IP>
> ```
> 
> 💡 Payload Profile:
> 
> - Matches remote service configurations against known public exploits (CVEs).
>     
> - Identifies clear structural issues: missing security patches, outdated components, and active default paths.
>     

## 🎯 Service-Specific NSE Targets

> [!SUCCESS] 🧩 Protocol Exploitation Scopes
> 
> Bash
> 
> ```
> # Auditing FTP for Anonymous/Safe structures
> nmap --script "ftp* and safe" -p21 <IP>
> 
> # Probing SMB for exposures (e.g., EternalBlue)
> nmap --script "smb* and (safe or vuln)" -p445 <IP>
> 
> # Automated HTTP mapping
> nmap --script "http* and safe" -p80,443 <IP>
> ```

## 📂 Discover Available NSE Scripts

> [!INFO] 📦 Local Script Inventory
> 
> Bash
> 
> ```
> # List the absolute path directory
> ls /usr/share/nmap/scripts/
> 
> # Query existing scripts by functional categories
> grep "categories" /usr/share/nmap/scripts/*.nse
> ```

# 🌐 Phase 5 — Web Attack Surface Enumeration

## 🌍 HTTP Fingerprinting

> [!SUCCESS] 🧠 Web Intel Extraction
> 
> Bash
> 
> ```
> nmap -p80,443 --script http-title,http-server-header,http-methods,http-canvas-fingerprinting <IP>
> ```
> 
> 💡 Reveals:
> 
> - Exposed development technologies, custom backend banners, and permitted request mutations (`GET`, `POST`, `OPTIONS`).
>     

## 🧭 Directory Enumeration

> [!TIP] 🔎 Lightweight Discovery Web Scans
> 
> Bash
> 
> ```
> nmap --script http-enum --script-args http-enum.basepath=/ -p80,443 <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Inspects structural configurations to locate standard administration portals, backup archives, or unlinked directories.
>     

## 🔥 SSL/TLS Analysis

> [!WARNING] 🔐 TLS Weakness Mapping
> 
> Bash
> 
> ```
> nmap --script ssl-enum-ciphers,ssl-cert -p443 <IP>
> ```
> 
> 💡 Reveals:
> 
> - Deprecated cryptographic configurations (SSLv3, TLS 1.0) and uncovers alternative corporate subdomains within the valid TLS certificate fields.
>     

## 🍪 HTTP Security Headers

> [!SUCCESS] 🛡️ Defensive Hardening Verification
> 
> Bash
> 
> ```
> nmap --script http-security-headers -p80,443 <IP>
> ```

# 🏢 Phase 6 — Windows / Active Directory Recon

## 🧠 Essential AD Ports

> [!DANGER] 🪟 Domain Controller Mapping
> 
> Bash
> 
> ```
> sudo nmap -p53,88,135,139,389,445,464,593,636,3268,3269,3389 -sV -sC <IP>
> ```
> 
> 💡 Crucial Infrastructure Validation:
> 
> - Evaluates internal core frameworks: Kerberos (`88`), LDAP (`389`), SMB (`445`), and WinRM (`5985`/`5986`) in a single pass.
>     

## 🔐 SMB Intelligence Gathering

> [!SUCCESS] 📂 Operational SMB Recon
> 
> Bash
> 
> ```
> nmap -p445 --script smb-os-discovery,smb2-security-mode,smb2-time <IP>
> ```
> 
> 💡 Extracts:
> 
> - Exact operating system versions, organizational domain names, and mandatory SMB signing constraints.
>     

## 👥 SMB Share Enumeration

> [!TIP] 📁 Anonymous Data Leak Audits
> 
> Bash
> 
> ```
> nmap --script smb-enum-shares,smb-protocols -p445 <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Verifies if unauthenticated context binds can view or extract network shared folders.
>     

## 👤 User Enumeration

> [!WARNING] 🧑 Active Directory Account Enumeration
> 
> Bash
> 
> ```
> nmap --script smb-enum-users,smb-enum-domains -p445 <IP>
> ```

# ☠️ Phase 7 — Firewall Evasion

## 👻 SYN Stealth Scan

> [!SUCCESS] 🕶️ Half-Open Probing
> 
> Bash
> 
> ```
> sudo nmap -sS -Pn <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Drops connections using a structural Reset (`RST`) directly after the server responds with a `SYN-ACK`.
>     
> - Prevents fully established TCP sessions, sliding beneath legacy system log thresholds.
>     

## 🎭 Decoy Scanning

> [!WARNING] 🧠 Attribution Blurring
> 
> Bash
> 
> ```
> sudo nmap -D 10.10.5.2,10.10.5.4,10.10.5.9,ME -sS <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Generates simultaneous spoofed probe queries originating from multiple random source points.
>     
> - Floods incident responder dashboards, masking your true machine IP (`ME`).
>     

## 🌐 Fragment Packets

> [!DANGER] 🧩 Deep Packet Inspection (DPI) Bypass
> 
> Bash
> 
> ```
> sudo nmap -f --mtu 16 <IP>
> ```
> 
> 💡 Mechanics:
> 
> - Splits standard TCP header tracking across tiny 16-byte packet intervals.
>     
> - Triggers signature assembly errors on old IDSes, passing raw data safely to the target endpoint.
>     

## 🔥 Source Port Manipulation

> [!TIP] ⚡ Stateless Rule Bypass
> 
> Bash
> 
> ```
> sudo nmap --source-port 53 -Pn <IP>
> ```
> 
> 💡 Tactics:
> 
> - Masks traffic signatures as inbound DNS server replies to trick legacy edge routers that allow raw high-port inbound connections.
>     

## 🐢 Slow Timing (Reduce Detection)

> [!INFO] ⏳ Low-and-Slow Discovery Operation
> 
> Bash
> 
> ```
> nmap -T2 -sS -Pn --max-retries 1 <IP>
> ```

# ⚡ Phase 8 — Performance Tuning

## 🚀 CTF / HTB Speed Scan

> [!SUCCESS] ⚡ Maximum Labs Acceleration
> 
> Bash
> 
> ```
> nmap -T5 --min-rate 5000 --max-retries 2 --open -Pn <IP>
> ```
> 
> ⚠️ **Operator Warning:** **ONLY** use this aggressive sequence inside simulated Capture The Flag arenas (Hack The Box, TryHackMe, OSCP exams). Running this across live enterprise networks can collapse unstable switches or trigger automatic perimeter lockdowns.

## 🧠 Reliable Real-World Speed

> [!TIP] 🎯 Production-Safe Optimization
> 
> Bash
> 
> ```
> sudo nmap -T4 --max-retries 2 --min-rate 1000 --initial-rtt-timeout 50ms <IP>
> ```

# 📦 Phase 9 — Output & Reporting

## 💾 Save Everything

> [!SUCCESS] 🧾 Professional Evidence Gathering Workflow
> 
> Bash
> 
> ```
> mkdir -p scans && sudo nmap -oA scans/initial_recon -p- --min-rate 3000 <IP>
> ```
> 
> 💡 Resulting Output Profiles:
> 
> - `scans/initial_recon.nmap`: Standard flat format for quick terminal reference.
>     
> - `scans/initial_recon.gnmap`: Grepable layout for lightning-fast command-line string operations.
>     
> - `scans/initial_recon.xml`: Structured markup file ready for secondary automation integration.
>     

## 🔎 Parse Open Ports Quickly

> [!TIP] ⚡ Clean Port Number Extraction
> 
> Bash
> 
> ```
> grep "/open/tcp/" scans/initial_recon.gnmap | awk -F'/' '{print $1}' | awk '{print $NF}' | sort -u | tr '\n' ',' | sed 's/,$//'
> ```

## 📂 XML for Automation

> [!INFO] 🤖 Automated Report Generation
> 
> Bash
> 
> ```
> xsltproc scans/initial_recon.xml -o scans/report.html
> ```

# 🧠 Phase 10 — Real Operator Workflow

> [!QUOTE] 🎯 Professional Enumeration Chain
> 
> Bash
> 
> ```
> 🗺️ [1. Map Live Subnet] ---> 🔓 [2. Full TCP Sweep] ---> 🛰️ [3. Top-Port UDP] 
>                                                                  |
> 💥 [6. Execute Vector] <--- 🔬 [5. Focused Scripts] <--- 🔍 [4. Target Banners]
> ```

# ⚔️ High-Value NSE Categories

|**Category**|**Tactical Implementation Profile**|
|---|---|
|**`auth`**|Probes targeting authentication bypass mechanics or weak system endpoints.|
|**`brute`**|Automates dictionary-based credential spraying against discovered endpoints.|
|**`default`**|Standard baseline operational suite running safe scripts automatically.|
|**`discovery`**|Deep information-gathering actions aimed at mining corporate infrastructure layouts.|
|**`intrusive`**|Noisy, aggressive scripts with high potential to trigger logging alerts or crash processes.|
|**`safe`**|Low-risk discovery interactions built to skip structural overloads.|
|**`vuln`**|Cross-references target version layouts against public exploit databases.|

# 🔥 Real-World Target Priority

> [!SUCCESS] 🎯 Services Pentesters Attack First
> 
> - **`SMB (445)`** → Null sessions, credential harvesting, or critical system-level RCE.
>     
> - **`LDAP (389)`** → Active Directory schema harvesting and anonymous binds.
>     
> - **`Kerberos (88)`** → User account identification and structural AS-REP Roasting options.
>     
> - **`HTTP (80/443)`** → Custom code flaws, source code leaks, or old web platforms.
>     
> - **`FTP (21)`** → Anonymous read-write permissions enabling malicious script drops.
>     
> - **`SNMP (161)`** → Cleartext community strings leaking internal network maps.
>     

# 🧠 Elite Enumeration Principles

> [!INFO] 🧩 Advanced Recon Truths
> 
> - Every single open socket is a potential entryway.
>     
> - App version numbers map out the underlying exploitation blueprint.
>     
> - System time drift directly reveals critical enterprise sync weaknesses.
>     
> - Default out-of-the-box system setups leak information better than zero-day vulnerabilities.
>     
> 
> 💡 **The Reality:** Real systems drop because of basic configuration failure, exposed assets, and credential oversight—not complex exploits.

# 🛸 Pivot Enumeration (Ligolo-NG / SSH Tunnels)

> [!NOTE] 🔀 Internal Proxy Routing Adaptation
> 
> Bash
> 
> ```
> nmap -Pn -sS -sC -sV -p- --min-rate 1000 --max-retries 3 --open --disable-arp-ping <IP>
> ```
> 
> 💡 Why this is modified:
> 
> - Standard Layer-2 discoveries (like automated ARP sweeps) break down entirely across internal pivoting tooling like **Ligolo-NG** or **SSH Local Port Forwards**.
>     
> - **`--disable-arp-ping`** forces raw Layer-3 scanning, preventing Nmap from inaccurately flagging an internal host as offline. Capping **`--max-retries 3`** keeps high tunnel latency from stalling your scan speed.
>     

# ☠️ Final Operator Truth

> [!DANGER] 🧠 Elite Pentest Mindset
> 
> Bash
> 
> ```
> Nmap does not hack systems.
> Nmap reveals opportunities.
> ```
> 
> The real skill is:
> 
> ✔ Interpreting output variations
> 
> ✔ Correlating seemingly isolated open ports
> 
> ✔ Spotting systemic infrastructure anomalies
> 
> ✔ Chaining weak findings into full RCE pathways