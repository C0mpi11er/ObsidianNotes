---

# 🛠️ Nmap Master Cheat Sheet (Tactical Edition)

---
This guide is structured to align with **OSCP** methodology and **Hack The Box (HTB)** standards, formatted specifically for your **Obsidian** technical notes.

---

# 🕵️ Nmap Tactical Guide: OSCP & HTB Edition

---

## 🛰️ 1. Phase 1: Rapid Host Discovery
*Crucial for network pivoting or mapping a /24 subnet in a lab environment.*

> [!ABSTRACT] **Standard Ping Sweep**
> Identifies live hosts quickly without the overhead of a port scan.
> ```bash
> sudo nmap -sn -n 10.10.10.0/24 -oN discovery.nmap
> ```

> [!CHECK] **The ICMP Trifecta**
> Bypasses firewalls that block standard `ICMP Echo`. Uses Timestamp and Address Mask requests.
> ```bash
> sudo nmap -sn -PE -PP -PM -n --stats-every 10s 10.10.10.0/24 -oN aggressive_discovery.nmap
> ```

---

## 🔍 2. Phase 2: Comprehensive Enumeration
*The OSCP "Golden Standard" for initial machine identification.*

> [!SUCCESS] **The Initial "All-Port" Sweep**
> HTB/OSCP best practice: Scan all 65,535 ports first to ensure no non-standard services are missed.
> ```bash
> sudo nmap -p- --min-rate 5000 10.10.10.15 -oN all_ports.nmap
> ```

> [!INFO] **Deep Service Audit**
> Run this once you have a list of open ports. Grabs versions and runs default scripts.
> ```bash
> sudo nmap -sV -sC -p 21,22,80,445,8080 10.10.10.15 -oA service_audit
> ```

---

## 🏢 3. Phase 3: Active Directory & Windows
*Specific to the OSCP AD set and HTB Windows tracks.*

> [!EXAMPLE] **SMB OS Fingerprinting**
> Crucial for identifying the Windows build and checking if it's a Domain Controller.
> ```bash
> sudo nmap -p 445 --script smb-os-discovery,smb-security-mode 10.10.10.15
> ```

> [!DANGER] **Aggressive AD Service Mapping**
> Targets the "Big 6" AD ports: DNS, Kerberos, RPC, LDAP, SMB, and RDP.
> ```bash
> sudo nmap -Pn -p 53,88,135,139,389,445,3389 10.10.10.15 -oN ad_services.nmap
> ```

---

## 🔓 4. Phase 4: Vulnerability Assessment
*Identifying the "Low Hanging Fruit" or known CVEs.*

> [!BUG] **NSE Vuln Scan**
> Automatically checks for vulnerabilities like EternalBlue (MS17-010) or common web misconfigurations.
> ```bash
> sudo nmap --script vuln 10.10.10.15 -oN vuln_scan.nmap
> ```

> [!WARNING] **Web Enumeration (NSE)**
> Quickly identifies common directories and HTTP methods without firing up a separate fuzzer.
> ```bash
> sudo nmap -p 80,443 --script http-enum,http-methods,http-title 10.10.10.15
> ```

---

## 👻 5. Phase 5: Evasion & Pivot
*Required for Pro Labs and hardened HTB machines.*

> [!CAUTION] **The "Ghost" Decoy**
> Hides your real IP address among 10 random decoys to confuse blue-team monitoring.
> ```bash
> sudo nmap -sS -Pn -D RND:10 10.10.10.15
> ```

> [!NOTE] **Source Port Spoofing**
> Attempts to bypass firewalls that allow all traffic originating from port 53 (DNS).
> ```bash
> sudo nmap -sS -g 53 --data-length 24 10.10.10.15
> ```

---

## ⚡ 6. Speed & Performance (CTF Style)
*For fast-paced lab scenarios where you need results immediately.*

> [!TLDR] **The Blitz Scan**
> Maximum aggression (T5) and minimum retries for the top 100 ports.
> ```bash
> sudo nmap -T5 --min-rate 5000 --max-retries 0 --open -F 10.10.10.0/24
> ```

---

## 📂 Quick Reference: Output Management

| Flag | Format | OSCP Use Case |
| :--- | :--- | :--- |
| **-oN** | **Normal** | Direct copy-paste into your final report. |
| **-oG** | **Grepable** | Extracting IP lists for automated tools like `EyeWitness`. |
| **-oA** | **All** | **MANDATORY**: Always save in all formats for your records. |

---

> [!TIP] **Pivoting through Ligolo-ng**
> Since you are using Arch and likely pivoting, remember that **SYN scans (`-sS`) fail over many tunnels.** Always default to a **TCP Connect Scan (`-sT`)** when scanning through a pivot.
> ```bash
> nmap -sT -Pn -T4 --min-rate 1000 -p 1-10000 10.10.10.15
> ```

> [!TIP] **Script Help Search**
> `nmap --script-help "*smb*"`
> `nmap --script-help "*vuln*"`
```