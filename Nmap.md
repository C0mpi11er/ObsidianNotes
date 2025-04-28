Got it — you want a **more detailed**, **updated**, and **brief** version of the Nmap target specification **+** some **popular scanning techniques** — nicely organized in **tables**.

Here’s the improved version 👇:

---

# 📜 **Nmap Target Specification and Scanning Techniques Cheat Sheet (2025 Updated)**

## 🎯 Target Specification

|Method|Syntax|Description|
|:--|:--|:--|
|**Single IP**|`nmap 192.168.1.1`|Scan one IP address.|
|**IP Range**|`nmap 192.168.1.1-10`|Scan from 192.168.1.1 to 192.168.1.10.|
|**Subnet (CIDR)**|`nmap 192.168.1.0/24`|Scan an entire subnet (256 addresses).|
|**Multiple IPs**|`nmap 192.168.1.1 192.168.1.5`|Scan multiple specific IPs.|
|**List from file**|`nmap -iL targets.txt`|Read list of targets from a file.|
|**Exclude Targets**|`nmap 192.168.1.0/24 --exclude 192.168.1.5`|Skip specific IPs.|
|**Exclude from file**|`nmap 192.168.1.0/24 --excludefile exclude.txt`|Exclude IPs listed in a file.|
|**Hostname**|`nmap example.com`|Scan a domain name (resolves to IP).|
|**IPv6**|`nmap -6 2606:4700:4700::1111`|Scan IPv6 addresses.|

---

## ⚡ Scan Modes and Techniques

| Mode                          | Syntax                 | Purpose                                                           |
| :---------------------------- | :--------------------- | :---------------------------------------------------------------- |
| **Fast Scan**                 | `nmap -T4 -F`          | Scan top 100 ports quickly.                                       |
| **Aggressive Scan**           | `nmap -A`              | OS detection, version detection, script scanning, and traceroute. |
| **Stealth SYN Scan**          | `nmap -sS`             | Half-open TCP scan (undetectable by some firewalls).              |
| **Connect TCP Scan**          | `nmap -sT`             | Full TCP connection (when SYN not allowed).                       |
| **UDP Scan**                  | `nmap -sU`             | Scan UDP ports (slower, stealthier).                              |
| **Ping Scan**                 | `nmap -sn`             | Discover live hosts without port scanning.                        |
| **Version Detection**         | `nmap -sV`             | Detect service versions (Apache 2.4, etc.).                       |
| **OS Detection**              | `nmap -O`              | Guess the target’s operating system.                              |
| **Top Ports Only**            | `nmap --top-ports 100` | Scan only the most common ports (faster).                         |
| **Custom Port Range**         | `nmap -p 1-1000`       | Scan specific port ranges.                                        |
| **All Ports**                 | `nmap -p-`             | Scan all 65,535 ports.                                            |
| **Firewall Evasion**          | `nmap -D RND:10`       | Decoy scan to confuse firewalls and IDS.                          |
| **Timing Template**           | `nmap -T<0-5>`         | Adjust speed/aggressiveness (`T0=Paranoid` to `T5=Insane`).       |
| **Scan as Root (Privileged)** | `sudo nmap -sS`        | Required for SYN scans and OS detection.                          |

---

## 🛡️ Advanced + Latest Tricks

|Technique|Syntax|Use Case|
|:--|:--|:--|
|**Scan Specific Ports**|`nmap -p 22,80,443`|Only scan selected ports.|
|**Aggressive with Safe Scripts**|`nmap -sC`|Run default scripts (safe scanning).|
|**Aggressive with All Scripts**|`nmap --script vuln`|Scan with scripts targeting vulnerabilities.|
|**Spoof MAC Address**|`nmap --spoof-mac 00:11:22:33:44:55`|Hide your real MAC address.|
|**IP Spoofing (Experimental)**|`nmap -S <spoofed-IP>`|Spoof source IP address (needs special network setup).|
|**Fragment Packets**|`nmap -f`|Break packets into smaller pieces to evade firewalls.|
|**Randomize Target Order**|`nmap --randomize-hosts`|Harder to detect patterns during scanning.|
|**Timing Control**|`nmap --min-rate 1000 --max-rate 5000`|Force faster scan rates.|
|**IPv6 Fast Scan**|`nmap -6 -T4 -F`|Scan IPv6 networks quickly.|

---

# 🧠 Pro Tip: Combine Options for Power Scanning!

Example:

```bash
nmap -A -T4 -p 1-1000 --open example.com
```

- Aggressive scan
    
- Faster timing
    
- Only ports 1-1000
    
- Show only open ports
    

---

# 🎯 Summary

✅ **Target types**: Single, Range, Subnet, Hostname, File  
✅ **Modes**: Fast, Stealth, UDP, Version, OS Detection  
✅ **Advanced tricks**: Firewall evasion, spoofing, fragmentation

---

Would you also like me to create a **super compact printable version** of this in a fancy **one-page PDF** for quick reference? 📄🔥  
(Perfect if you're planning to do a lot of scanning!)