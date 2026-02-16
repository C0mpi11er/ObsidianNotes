Got it — you want a **more detailed**, **updated**, and **brief** version of the Nmap target specification **+** some **popular scanning techniques** — nicely organized in **tables**.

Here’s the improved version 👇:

---

# 📜 **Nmap Target Specification and Scanning Techniques Cheat Sheet (2025 Updated)**

## 🎯 Target Specification

| Method                | Syntax                                          | Description                            |
| :-------------------- | :---------------------------------------------- | :------------------------------------- |
| **Single IP**         | `nmap 192.168.1.1`                              | Scan one IP address.                   |
| **IP Range**          | `nmap 192.168.1.1-10`                           | Scan from 192.168.1.1 to 192.168.1.10. |
| **Subnet (CIDR)**     | `nmap 192.168.1.0/24`                           | Scan an entire subnet (256 addresses). |
| **Multiple IPs**      | `nmap 192.168.1.1 192.168.1.5`                  | Scan multiple specific IPs.            |
| **List from file**    | `nmap -iL targets.txt`                          | Read list of targets from a file.      |
| **Exclude Targets**   | `nmap 192.168.1.0/24 --exclude 192.168.1.5`     | Skip specific IPs.                     |
| **Exclude from file** | `nmap 192.168.1.0/24 --excludefile exclude.txt` | Exclude IPs listed in a file.          |
| **Hostname**          | `nmap example.com`                              | Scan a domain name (resolves to IP).   |
| **IPv6**              | `nmap -6 2606:4700:4700::1111`                  | Scan IPv6 addresses.                   |

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

| Technique                        | Syntax                                 | Use Case                                               |
| :------------------------------- | :------------------------------------- | :----------------------------------------------------- |
| **Scan Specific Ports**          | `nmap -p 22,80,443`                    | Only scan selected ports.                              |
| **Aggressive with Safe Scripts** | `nmap -sC`                             | Run default scripts (safe scanning).                   |
| **Aggressive with All Scripts**  | `nmap --script vuln`                   | Scan with scripts targeting vulnerabilities.           |
| **Spoof MAC Address**            | `nmap --spoof-mac 00:11:22:33:44:55`   | Hide your real MAC address.                            |
| **IP Spoofing (Experimental)**   | `nmap -S <spoofed-IP>`                 | Spoof source IP address (needs special network setup). |
| **Fragment Packets**             | `nmap -f`                              | Break packets into smaller pieces to evade firewalls.  |
| **Randomize Target Order**       | `nmap --randomize-hosts`               | Harder to detect patterns during scanning.             |
| **Timing Control**               | `nmap --min-rate 1000 --max-rate 5000` | Force faster scan rates.                               |
| **IPv6 Fast Scan**               | `nmap -6 -T4 -F`                       | Scan IPv6 networks quickly.                            |

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
    





| **Timing Template** | **Total Duration** |
| ------------------- | ------------------ |
| **T0 (Paranoid)**   | 9.8 hours          |
| **T1 (Sneaky)**     | 27.53 minutes      |
| **T2 (Polite)**     | 40.56 seconds      |
| **T3 (Normal)**     | 0.15 seconds       |
| **T4 (Aggressive)** | 0.13 seconds       |

---

# 🎯 Summary

✅ **Target types**: Single, Range, Subnet, Hostname, File  
✅ **Modes**: Fast, Stealth, UDP, Version, OS Detection  
✅ **Advanced tricks**: Firewall evasion, spoofing, fragmentation

---


### 🔍 Nmap Firewall Detection Notes

#### 🛡️ `-sW` (Window Scan)

- The `-sW` flag helps **detect firewall rules**.
    
- It uses the **TCP window size** to infer port state.
    
- If a port responds, it may **not be behind a firewall**.
    
- If **no response**, the port is likely **filtered by a firewall**.
    

#### 🔥 `-sA` (ACK Scan)

- The `-sA` scan is useful to **determine if ports are filtered**.
    
- It sends ACK packets to probe firewall behavior.
    
- If the port is **guarded by a firewall**, it typically **does not show up at all**.
    
- If the port is **not guarded**, it **shows up as "unfiltered"**, even if it's closed.
    

#### ✅ Summary

- Use `-sW` to **observe firewall responses** via TCP window behavior.
    
- Use `-sA` to **map out which ports are filtered** by checking if ACKs are blocked or allowed.


## 👻 IP Spoofing & Decoy Scanning – Explained Like a Baby

---

### 🍼 What is IP Spoofing?

- Pretend to be someone else on the internet by **faking your IP address**.
- 📦 You send a scan to the target using a fake IP.
- 📨 Target sends replies to that **fake IP**, not to you.

> ⚠️ You only benefit if you can **see the replies on the network**, e.g., using Wireshark.

---

### 🧃 Steps in IP Spoofing Scan:

1. Attacker sends packet with **spoofed source IP**.
2. Target replies to **spoofed IP**.
3. Attacker uses packet sniffer to **see replies** and find open ports.

---

### 🛠️ Nmap Command (With Spoofed IP)

Use this format:
```bash
nmap -e [NET_INTERFACE] -Pn -S [SPOOFED_IP] [TARGET_IP]
```

📝 Example:
```bash
nmap -e wlan0 -Pn -S 192.168.1.100 192.168.1.10
```

- `-e`: Select network interface (e.g. `wlan0`, `eth0`)
- `-Pn`: Don’t ping (target won't reply to you)
- `-S`: Spoofed source IP

---

### 🧢 MAC Address Spoofing (Local Network Only)

If you're on the **same LAN/WiFi**, you can spoof MAC address too.

```bash
nmap --spoof-mac 00:11:22:33:44:55 -e wlan0 -Pn -S 192.168.1.100 192.168.1.10
```

---

### 🫥 Decoy Scanning

If spoofing won’t help, use **decoys** to hide your real IP among fake ones.

🧠 Idea: Make the scan appear to come from **many IPs** to confuse the target.

```bash
nmap -D 192.168.1.3,192.168.1.4,ME 192.168.1.10
```

- `-D`: Decoys list
- `ME`: Your real IP (hidden among fake ones)

Target thinks:
```
"Wait, who scanned me? Was it 192.168.1.3? 1.4? ME?? 😵‍💫"
```

---

### ⚠️ Limitations

- IP spoofing won’t work **unless you can see replies** (same subnet or sniffing from a privileged spot).
- MAC spoofing only works if you’re **on the same WiFi/Ethernet**.
- Decoy scans help avoid detection but **don't guarantee anonymity**.

---
### 🧟 What Is a Zombie?

A **zombie** is a quiet machine (like a printer or an idle server) that:

- Doesn’t talk to many people
    
- Increases a small number every time it sends something (called the **IP ID**)
    

This **IP ID** lets you spy on what it did for you.

---

### 🕵️‍♂️ Step-by-Step Trick:

1. **Check the zombie’s current number (IP ID)**
    
    - You ask the zombie something tiny and see its current number.
        
2. **Send a fake knock to the target’s door**
    
    - You tell the target: “Hi! I’m the zombie!” (Spoofed packet with zombie’s IP)
        
3. **Ask the zombie again**
    
    - Check if the number changed.
        

---

### 📊 How to Read the Results:

- If the number went up by **1**:  
    ❌ Port is **closed** or **blocked**.
    
- If the number went up by **2**:  
    ✅ Port is **open**! (Zombie replied to the target silently)
    

---

### ### 🧪 Nmap Command:

nmap -sI ZOMBIE_IP TARGET_IP

    -sI = Idle scan (zombie)

    ZOMBIE_IP = Quiet helper

    TARGET_IP = Target computer

🔒 Why Use It?

    ✅ Super stealthy

    ✅ Target never knows you scanned it

    ✅ Helps bypass firewalls and intrusion detection systems

⚠️ Requirements:

    Zombie must be quiet (idle)

    Zombie must use predictable IP ID

    You must be able to see replies that come back to the zombie






---

# 🔍 Nmap NSE Script Scan Cheat Sheet — Most Used Scripts

---

script dir
=
/usr/share/nmap/scripts/


## 🧠 NSE Script Categories (Core Ones You’ll Use)

|Category|Purpose|
|---|---|
|`default`|Safe, quick recon scripts|
|`safe`|Non-intrusive info gathering|
|`discovery`|Host/service enumeration|
|`version`|Enhance service detection|
|`auth`|Authentication checks|
|`brute`|Credential brute forcing|
|`vuln`|Vulnerability detection|
|`exploit`|Active exploitation|
|`malware`|Backdoor detection|
|`dos`|Denial of Service tests|

---

# 🚀 Baseline Script Scan

### 👉 Standard Enumeration

```
nmap -sC -sV TARGET
```

(`-sC` = default scripts)

---

### 👉 Aggressive Scan

```
nmap -A TARGET
```

Runs scripts + OS detect + traceroute + versions.

---

---

# 🔐 Authentication & Bruteforce

### SMB

```
nmap --script smb-brute -p445 TARGET
nmap --script smb-enum-users -p445 TARGET
nmap --script smb-enum-shares -p445 TARGET
nmap --script smb-enum-domains -p445 TARGET
```

### FTP

```
nmap --script ftp-brute -p21 TARGET
nmap --script ftp-anon -p21 TARGET
```

### SSH

```
nmap --script ssh-brute -p22 TARGET
nmap --script ssh-auth-methods -p22 TARGET
```

### HTTP Auth

```
nmap --script http-brute -p80,443 TARGET
```

---

---

# 🌐 Web Enumeration (Extremely Common)

### Titles / Headers

```
nmap --script http-title,http-headers -p80,443 TARGET
```

### Robots.txt

```
nmap --script http-robots.txt -p80 TARGET
```

### HTTP Methods

```
nmap --script http-methods -p80 TARGET
```

### Directories

```
nmap --script http-enum -p80 TARGET
```

### CMS Detection

```
nmap --script http-wordpress-enum -p80 TARGET
nmap --script http-drupal-enum -p80 TARGET
```

---

---

# 🧬 Vulnerability Detection

### Run All Vuln Scripts

```
nmap --script vuln TARGET
```

---

### Specific High-Value Vuln Scripts

|Script|Detects|
|---|---|
|smb-vuln-ms17-010|EternalBlue|
|smb-vuln-ms08-067|NetAPI|
|http-vuln-cve2017-5638|Apache Struts|
|http-vuln-cve2015-1635|IIS RCE|
|ssl-heartbleed|OpenSSL Heartbleed|
|http-shellshock|Bash CGI|
|ftp-vsftpd-backdoor|VSFTPD 2.3.4|

```
nmap --script smb-vuln-ms17-010 -p445 TARGET
```

---

---

# 🧾 SSL / TLS Enumeration

```
nmap --script ssl-cert,ssl-enum-ciphers -p443 TARGET
```

---

---

# 🗂 SMB / Windows Enumeration (OSCP Gold)

```
nmap --script smb-os-discovery -p445 TARGET
nmap --script smb-security-mode -p445 TARGET
nmap --script smb-protocols -p445 TARGET
nmap --script smb-system-info -p445 TARGET
```

---

---

# 🧪 Database Enumeration

### MySQL

```
nmap --script mysql-info,mysql-enum,mysql-brute -p3306 TARGET
```

### MSSQL

```
nmap --script ms-sql-info,ms-sql-brute -p1433 TARGET
```

### PostgreSQL

```
nmap --script pgsql-brute -p5432 TARGET
```

---

---

# 📡 Network Services

### SNMP

```
nmap --script snmp-info,snmp-brute -p161 TARGET
```

### DNS

```
nmap --script dns-zone-transfer -p53 TARGET
```

### SMTP

```
nmap --script smtp-enum-users -p25 TARGET
```

---

---

# 🕵️ Malware / Backdoor Detection

```
nmap --script malware TARGET
```

---

---

# 🎯 Script Selection Tricks

### Multiple Scripts

```
nmap --script "smb-enum-*,smb-vuln-*" -p445 TARGET
```

### By Category

```
nmap --script discovery TARGET
```

### Exclude Dangerous

```
nmap --script vuln --script-args unsafe=1 TARGET
```

---

---

# ⚡ Performance Tweaks

```
-T4
--min-rate 1000
--max-retries 2
```

---

---

# 🧠 Practical Workflow

```
nmap -p- -T4 TARGET
nmap -sC -sV -pPORTS TARGET
nmap --script vuln -pPORTS TARGET
nmap --script smb-enum-* -p445 TARGET
```

---

---

# 📌 OSCP-Style “Always Run These”

```
-sC -sV
--script vuln
smb-enum-*
ssl-enum-ciphers
http-enum
snmp-info
dns-zone-transfer
```

---

If you want, I can:

✅ Turn this into a **Markdown-ready Obsidian note**  
✅ Expand with **less-used but deadly scripts**  
✅ Add **real attack chains**  
✅ Include **AD-specific NSE workflows**

Just say the word.