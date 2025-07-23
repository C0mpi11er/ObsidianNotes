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
    





|**Timing Template**|**Total Duration**|
|---|---|
|**T0 (Paranoid)**|9.8 hours|
|**T1 (Sneaky)**|27.53 minutes|
|**T2 (Polite)**|40.56 seconds|
|**T3 (Normal)**|0.15 seconds|
|**T4 (Aggressive)**|0.13 seconds|

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