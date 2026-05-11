---

# 🛠️ Nmap Master Cheat Sheet (Tactical Edition)

---
# 🕵️‍♂️ Nmap Tactical Field Manual (OSCP / HTB / Red-Team Edition)



---

# 🧠 CORE OPERATOR MINDSET

> [!INFO] 🎯 Recon Philosophy
>
> ```bash
> Enumerate everything.
> Trust nothing.
> Validate assumptions.
> ```
>
> 💡 Nmap is not “just a scanner.”
>
> It is:
>
> ✔ Attack surface mapping
> ✔ Service fingerprinting
> ✔ Exposure analysis
> ✔ Initial foothold intelligence
> ✔ AD infrastructure discovery
> ✔ Firewall behavior analysis

---

# ⚡ PHASE 1 — HOST DISCOVERY

---

## 🔍 Basic Ping Sweep

> [!SUCCESS] 🌐 Discover Live Hosts
>
> ```bash
> nmap -sn 10.10.10.0/24
> ```
>
> ✔ ARP + ICMP discovery
> ✔ No port scan
> ✔ Fast internal recon

---

## ⚔️ TCP Discovery (When ICMP is Blocked)

> [!TIP] 🧠 SYN-Based Discovery
>
> ```bash
> nmap -sn -PS21,22,25,80,443,445,3389 10.10.10.0/24
> ```
>
> 💡 Uses SYN probes instead of ICMP
>
> Great for:
>
> * corporate networks
> * firewalled targets
> * VPN environments

---

## 👻 Stealthier Discovery

> [!WARNING] 🕶️ Low-Noise Discovery
>
> ```bash
> nmap -sn -PA80,443 -n --disable-arp-ping <SUBNET>
> ```
>
> ✔ avoids DNS lookups
> ✔ avoids ARP noise
> ✔ blends into web traffic

---

# 🔥 PHASE 2 — FULL PORT ENUMERATION

---

## 💣 The Real OSCP Scan

> [!DANGER] 🚀 Full TCP Port Sweep
>
> ```bash
> nmap -p- --min-rate 5000 -T4 -Pn <IP>
> ```
>
> 💡 NEVER skip full port scans.
>
> Hidden footholds often live on:
>
> * 8080
> * 8443
> * 9001
> * 5985
> * 31337
> * random high ports

---

## ⚡ Fast Open-Port Discovery

> [!SUCCESS] ⚡ Fast Recon
>
> ```bash
> nmap --top-ports 1000 --open -T4 <IP>
> ```
>
> ✔ faster triage
> ✔ ideal for large environments

---

## 🧠 UDP Enumeration (Often Ignored)

> [!WARNING] ☣️ UDP Recon
>
> ```bash
> nmap -sU --top-ports 50 <IP>
> ```
>
> Critical for finding:
>
> * DNS
> * SNMP
> * TFTP
> * NFS
> * Kerberos
> * VPN services

---

# 🧪 PHASE 3 — SERVICE ENUMERATION

---

## 🔬 Core Enumeration Command

> [!SUCCESS] 🧠 Default Enumeration
>
> ```bash
> nmap -sC -sV -p <PORTS> <IP>
> ```
>
> ✔ NSE default scripts
> ✔ Version detection
> ✔ Safe baseline recon

---

## ⚔️ Aggressive Enumeration

> [!WARNING] 🔥 Full Fingerprinting
>
> ```bash
> nmap -A -p <PORTS> <IP>
> ```
>
> Includes:
>
> * OS detection
> * traceroute
> * NSE scripts
> * service fingerprinting
>
> ⚠ noisy

---

## 🧠 Reliable OS Detection

> [!TIP] 🪟 Linux vs Windows Identification
>
> ```bash
> nmap -O --osscan-guess <IP>
> ```
>
> 💡 Useful when banners lie

---

# 🧨 PHASE 4 — NSE SCRIPTING ENGINE

---

## 🔍 Vulnerability Enumeration

> [!BUG] ☠️ NSE Vuln Scan
>
> ```bash
> nmap --script vuln <IP>
> ```
>
> Detects:
>
> * outdated services
> * weak configs
> * exposed CVEs
> * dangerous defaults

---

## 🎯 Service-Specific NSE

> [!SUCCESS] 🧩 Focused Enumeration
>
> ```bash
> nmap --script "ftp* and safe" -p21 <IP>
> ```
>
> ```bash
> nmap --script "smb* and vuln" -p445 <IP>
> ```
>
> ```bash
> nmap --script "http* and safe" -p80,443 <IP>
> ```

---

## 📂 Discover Available NSE Scripts

> [!INFO] 📦 NSE Discovery
>
> ```bash
> ls /usr/share/nmap/scripts/
> ```
>
> ```bash
> grep "categories" /usr/share/nmap/scripts/*.nse
> ```

---

# 🌐 PHASE 5 — WEB ATTACK SURFACE ENUMERATION

---

## 🌍 HTTP Fingerprinting

> [!SUCCESS] 🧠 Web Intel
>
> ```bash
> nmap -p80,443 \
> --script http-title,http-server-header,http-methods \
> <IP>
> ```
>
> Reveals:
>
> * server tech
> * allowed methods
> * proxy leaks
> * misconfigs

---

## 🧭 Directory Enumeration

> [!TIP] 🔎 Lightweight Discovery
>
> ```bash
> nmap --script http-enum -p80,443 <IP>
> ```

---

## 🔥 SSL/TLS Analysis

> [!WARNING] 🔐 TLS Weakness Discovery
>
> ```bash
> nmap --script ssl-enum-ciphers -p443 <IP>
> ```
>
> Finds:
>
> * weak ciphers
> * TLS versions
> * insecure crypto

---

## 🍪 HTTP Security Headers

> [!SUCCESS] 🛡️ Header Analysis
>
> ```bash
> nmap --script http-security-headers -p80,443 <IP>
> ```

---

# 🏢 PHASE 6 — WINDOWS / ACTIVE DIRECTORY RECON

---

## 🧠 Essential AD Ports

> [!DANGER] 🪟 Domain Infrastructure Scan
>
> ```bash
> nmap -p53,88,135,139,389,445,464,593,636,3268,3269,3389 <IP>
> ```

---

## 🔐 SMB Intelligence Gathering

> [!SUCCESS] 📂 SMB Recon
>
> ```bash
> nmap -p445 \
> --script smb-os-discovery,smb2-security-mode,smb2-time \
> <IP>
> ```
>
> Extracts:
>
> * domain name
> * hostname
> * SMB signing
> * server time skew

---

## 👥 SMB Share Enumeration

> [!TIP] 📁 Anonymous Shares
>
> ```bash
> nmap --script smb-enum-shares -p445 <IP>
> ```

---

## 👤 User Enumeration

> [!WARNING] 🧑 Active Directory Users
>
> ```bash
> nmap --script smb-enum-users -p445 <IP>
> ```

---

# ☠️ PHASE 7 — FIREWALL EVASION

---

## 👻 SYN Stealth Scan

> [!SUCCESS] 🕶️ Half-Open Scan
>
> ```bash
> nmap -sS <IP>
> ```
>
> 💡 Default “stealth” scan

---

## 🎭 Decoy Scanning

> [!WARNING] 🧠 Attribution Obfuscation
>
> ```bash
> nmap -D RND:10 <IP>
> ```

---

## 🌐 Fragment Packets

> [!DANGER] 🧩 IDS Evasion
>
> ```bash
> nmap -f <IP>
> ```
>
> 💡 Splits packets into fragments

---

## 🔥 Source Port Manipulation

> [!TIP] ⚡ Firewall Bypass
>
> ```bash
> nmap --source-port 53 <IP>
> ```

---

## 🐢 Slow Timing (Reduce Detection)

> [!INFO] ⏳ Quiet Scan
>
> ```bash
> nmap -T2 -sS -Pn <IP>
> ```

---

# ⚡ PHASE 8 — PERFORMANCE TUNING

---

## 🚀 CTF / HTB Speed Scan

> [!SUCCESS] ⚡ Maximum Speed
>
> ```bash
> nmap -T5 --min-rate 5000 --open <IP>
> ```
>
> ⚠ ONLY for:
>
> * labs
> * CTFs
> * Hack The Box

---

## 🧠 Reliable Real-World Speed

> [!TIP] 🎯 Balanced Scan
>
> ```bash
> nmap -T4 --max-retries 2 --min-rate 1000 <IP>
> ```

---

# 📦 PHASE 9 — OUTPUT & REPORTING

---

## 💾 Save Everything

> [!SUCCESS] 🧾 Professional Workflow
>
> ```bash
> nmap -oA scans/initial <IP>
> ```
>
> Generates:
>
> ```bash
> initial.nmap
> initial.xml
> initial.gnmap
> ```

---

## 🔎 Parse Open Ports Quickly

> [!TIP] ⚡ Extract Ports
>
> ```bash
> grep "/open/" scan.gnmap
> ```

---

## 📂 XML for Automation

> [!INFO] 🤖 Tool Chaining
>
> ```bash
> xsltproc scan.xml -o report.html
> ```

---

# 🧠 PHASE 10 — REAL OPERATOR WORKFLOW

---

> [!QUOTE] 🎯 Professional Enumeration Chain
>
> ```bash
> 1. Identify live hosts
> 2. Run full TCP scan
> 3. Run targeted UDP scan
> 4. Enumerate versions
> 5. Identify technologies
> 6. Run focused NSE scripts
> 7. Validate manually
> 8. Build attack paths
> 9. Prioritize weakest service
> ```

---

# ⚔️ HIGH-VALUE NSE CATEGORIES

---

| Category    | Purpose                      |
| ----------- | ---------------------------- |
| `auth`      | authentication bypass checks |
| `broadcast` | network discovery            |
| `brute`     | credential attacks           |
| `default`   | safe default scripts         |
| `discovery` | intel gathering              |
| `dos`       | denial-of-service checks     |
| `exploit`   | exploitation attempts        |
| `external`  | third-party integrations     |
| `fuzzer`    | malformed input testing      |
| `intrusive` | noisy aggressive scripts     |
| `malware`   | malware detection            |
| `safe`      | low-risk enumeration         |
| `vuln`      | vulnerability discovery      |

---

# 🔥 REAL-WORLD TARGET PRIORITY

---

> [!SUCCESS] 🎯 Services Pentesters Prioritize
>
> ```bash
> SMB     → credential exposure
> LDAP    → Active Directory intel
> Kerberos→ domain attacks
> HTTP    → web exploitation
> SSH     → weak creds / keys
> FTP     → anonymous access
> SNMP    → information leakage
> RDP     → lateral movement
> WinRM   → remote execution
> NFS     → mounted shares
> ```

---

# 🧠 ELITE ENUMERATION PRINCIPLES

---

> [!INFO] 🧩 Advanced Recon Truths
>
> ```bash
> Every open port matters.
> Version numbers matter.
> Time skew matters.
> Default configs matter.
> Misconfigurations matter more than CVEs.
> ```
>
> 💡 Most compromises happen because:
>
> * weak configurations
> * exposed services
> * credential reuse
> * poor segmentation
>
> NOT “zero-days.”

---

# 📚 HIGH-VALUE LEARNING RESOURCES

---

## 🎥 Practical Enumeration

* [IppSec YouTube](https://www.youtube.com/@ippsec?utm_source=chatgpt.com)
* [0xdf HackTheBox Writeups](https://0xdf.gitlab.io/?utm_source=chatgpt.com)
* [HackTricks Enumeration Guide](https://book.hacktricks.wiki/?utm_source=chatgpt.com)
* [Nmap NSE Documentation](https://nmap.org/nsedoc/?utm_source=chatgpt.com)

---

## 📘 Books & References

* [Nmap Network Scanning (Official Book)](https://nmap.org/book/?utm_source=chatgpt.com)
* [The Hacker Playbook 3](https://www.amazon.com/Hacker-Playbook-Practical-Penetration-Testing/dp/1980901759?utm_source=chatgpt.com)
* [Red Team Field Manual](https://www.amazon.com/Red-Team-Field-Manual-RTFM/dp/1494295504?utm_source=chatgpt.com)

---

# ☠️ FINAL OPERATOR TRUTH

---

> [!DANGER] 🧠 Elite Pentest Mindset
>
> ```bash
> Nmap does not hack systems.
> Nmap reveals opportunities.
> ```
>
> The real skill is:
>
> ✔ interpreting results
> ✔ correlating exposure
> ✔ spotting anomalies
> ✔ chaining weaknesses
> ✔ thinking like an operator
