---

# 🛠️ Nmap Master Cheat Sheet (Tactical Edition)

---

## 🛰️ 1. Host Discovery & Recon
*Map the network boundary and identify live targets without full port scans.*

> [!ABSTRACT] **Tactical Sweep**
> Quick identification of the attack surface.
> ```bash
> sudo nmap -sn -n 10.10.10.0/24 -oN discovery.nmap
> 
``

> [!CHECK] **The ICMP Trifecta**
> Bypasses firewalls that only block standard Echo requests by using Timestamp and Address Mask queries.
> ```bash
> sudo nmap -sn -PE -PP -PM -n --stats-every 10s 10.10.10.0/24 -oN aggressive_discovery.nmap
> ```

---

## 🕵️ 2. Deep Audit & Service Identification
*Full profiling of identified targets. Essential for finding versions and entry points.*

> [!SUCCESS] **The Full Service Audit**
> Scans all 65,535 ports with service versioning and default scripts.
> ```bash
> sudo nmap -sV -sC -p- -T4 --reason 10.10.10.15 -oA full_audit_report
> ```

> [!ATTENTION] **Surgical Version Detection**
> Use high intensity for service fingerprinting while keeping timing polite to avoid crashing fragile services.
> ```bash
> sudo nmap -sS -sV --version-intensity 9 -T2 10.10.10.15 -oN surgical_audit.nmap
> ```

---

## 🔓 3. Vulnerability Scanning (NSE Engine)
*Automated detection of CVEs and common misconfigurations.*

> [!BUG] **General Vuln Scan**
> Runs the "vuln" category scripts against the target to find known exploits.
> ```bash
> sudo nmap --script vuln 10.10.10.15 -oN vuln_scan.nmap
> ```

> [!WARNING] **Web Vulnerability Check**
> Aggressively enumerates directories and methods on web servers.
> ```bash
> sudo nmap -p 80,443 --script http-enum,http-vhosts,http-methods,http-title 10.10.10.15
> ```

---

## 🏢 4. Active Directory & SMB Special
*Optimized for Windows internal environments and lateral movement prep.*

> [!INFO] **AD Service Audit**
> Targets the specific ports required for Domain Controller interaction (DNS, Kerberos, RPC, LDAP, SMB, RDP).
> ```bash
> sudo nmap -Pn -p 53,88,135,139,389,445,636,3268,3389 10.10.10.15 -oN ad_services.nmap
> ```

> [!EXAMPLE] **SMB OS Discovery**
> Grabs OS version, Domain name, and security mode signatures via SMB.
> ```bash
> sudo nmap -p 139,445 --script smb-os-discovery,smb-security-mode 10.10.10.15
> ```

> [!DANGER] **Unsafe SMB Enumeration**
> Forces the enumeration of users and shares; `unsafe=1` can crash legacy Windows systems.
> ```bash
> sudo nmap -p 445 --script smb-enum-shares,smb-enum-users --script-args unsafe=1 10.10.10.15
> ```

---

## 👻 5. Evasion & Stealth
*Tactics to bypass IDS/IPS monitoring and firewall filters.*

> [!CAUTION] **Decoy Noise**
> Generates 10 random IP decoys to make your real IP address harder to identify in logs.
> ```bash
> sudo nmap -sS -Pn -D RND:10 10.10.10.15
> ```

> [!NOTE] **The Fragmented Sneak**
> Fragments packets and spoofs the source port to 53 (DNS) to trick simple packet filters.
> ```bash
> sudo nmap -f -g 53 --data-length 24 10.10.10.15
> ```

---

## ⚡ 6. Speed & Lab Performance
*High-speed commands for CTFs or stable lab networks.*

> [!TLDR] **The Blitz Scan**
> Max speed, zero retries, scanning the top 100 most common ports.
> ```bash
> sudo nmap -T5 --min-rate 5000 --max-retries 0 --open -F 10.10.10.0/24
> ```

---

## 🛠️ 7. Debugging & Analysis

> [!QUESTION] **Packet Trace Analysis**
> Use this when Nmap returns "Filtered" to see exactly why the probe failed.
> ```bash
> nmap -p80 --packet-trace --reason --disable-arp-ping 10.10.10.15
> ```

> [!FAILURE] **Missing Version Info?**
> Forces Nmap to try every single version probe in the database if the standard scan fails.
> ```bash
> sudo nmap -sV --version-all -p <port> <target>


---

### 📂 Quick Reference: Output Flags

| Flag | Description | Use Case |
| :--- | :--- | :--- |
| `-oN` | **Normal** | Direct reading for human notes. |
| `-oG` | **Grepable** | Extracting IPs/Ports via `grep` or `awk`. |
| `-oX` | **XML** | Importing results into Metasploit/Reporting tools. |
| `-oA` | **All** | Saves `.nmap`, `.gnmap`, and `.xml` simultaneously. |

> [!TIP] **Pivot Performance**
> When scanning through a tunnel like **Ligolo-ng**, use **TCP Connect Scans** (`-sT`) as SYN scans often fail through proxied interfaces.
> ```bash
> nmap -sT -Pn -T4 --min-rate 1000 -p 1-10000 10.10.10.15
> ```
```