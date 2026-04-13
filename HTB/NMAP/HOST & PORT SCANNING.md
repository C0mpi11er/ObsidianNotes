## 🚦 Port States Explained

|State|Description|Technical Trigger|
|---|---|---|
|**Open**|Connection established.|Accepted TCP/UDP/SCTP request.|
|**Closed**|Port is accessible but no service is listening.|Target sent an `RST` (Reset) flag.|
|**Filtered**|Nmap cannot determine state.|No response received or ICMP error code (packet dropped).|
|**Unfiltered**|Port is accessible; state unknown.|Only seen in `TCP-ACK` scans.|
|**Open\|Filtered**|Likely protected by a firewall.|No response received for specific probes.|

Export to Sheets

---

## ⚡ Scanning Methodologies

### 1. TCP SYN Scan (`-sS`)

- **Default** for root/sudo users.
    
- Sends a `SYN` packet and waits for `SYN-ACK` (Open) or `RST` (Closed).
    
- Does **not** complete the three-way handshake, making it faster and quieter.
    

### 2. TCP Connect Scan (`-sT`)

- **Default** for non-privileged users.
    
- Completes the full three-way handshake.
    
- **Pros:** Highly accurate; "polite" to services.
    
- **Cons:** Easily logged by firewalls and IDS because a full connection is made.
    

### 3. UDP Scan (`-sU`)

- **Stateless:** No handshake exists.
    
- **Slow:** Nmap often has to wait for timeouts.
    
- **Results:** If an ICMP "Port Unreachable" (Type 3, Code 3) is returned, the port is **Closed**. If no response, it is marked **Open|Filtered**.
    

### 4. Service Version Detection (`-sV`)

- Goes beyond port states to identify **Version Numbers** and **Service Names**.
    
- Works by sending specific "probes" (like an SMB request) to see how the service identifies itself.
    

---

## 💻 Command Reference (Copy & Paste)

### Target Selection & Port Scoping

Bash

```
# Scan a single port
sudo nmap <IP> -p 22

# Scan a range of ports
sudo nmap <IP> -p 22-445

# Scan specific multiple ports
sudo nmap <IP> -p 22,80,443

# Scan all 65,535 ports
sudo nmap <IP> -p-

# Scan top 100 ports (Fast mode)
sudo nmap <IP> -F

# Scan top X most frequent ports
sudo nmap <IP> --top-ports=10
```

### Scan Types & Discovery

Bash

```
# TCP SYN Scan (Default/Half-open)
sudo nmap -sS <IP>

# TCP Connect Scan (Full handshake)
sudo nmap -sT <IP>

# UDP Scan
sudo nmap -sU <IP>

# Service Version Detection
sudo nmap -sV <IP>
```

### Debugging & Stealth Options

Bash

```
# Stealth: Disable ICMP ping, DNS resolution, and ARP pings
sudo nmap <IP> -Pn -n --disable-arp-ping

# Debugging: Show all packets and reasons for port states
sudo nmap <IP> --packet-trace --reason

# Get the hostname via SMB
sudo nmap -p 445 --script smb-os-discovery <IP>


### 📋 Obsidian Command Summary

|Goal|Command|
|---|---|
|**Basic DNS Lookup**|`nmap -R <IP>`|
|**NetBIOS/Workgroup Name**|
`nmap --script nbstat.nse <IP>`|
|**Full SMB Detail (Hostname)**|`nmap -p 445 --script smb-os-discovery <IP>`|
|**Everything (Aggressive)**|`nmap -A <IP>`|


```



| Operating System                      | Default TTL | Description                                              |
| ------------------------------------- | ----------- | -------------------------------------------------------- |
| **Linux / Kernel 2.4+**               | **64**      | Includes Ubuntu, Debian, CentOS, and most IoT devices.   |
| **Google / Android**                  | **64**      | Based on the Linux kernel.                               |
| **macOS / iOS**                       | **64**      | Based on the Darwin/BSD kernel.                          |
| **Windows (NT, 2000, XP, 7, 10, 11)** | **128**     | The standard for all modern Microsoft desktop/server OS. |
| **Solaris / AIX**                     | **255**     | Traditional Unix systems.                                |
| **Cisco / Network Gear**              | **255**     | Most enterprise-grade routers and switches.              |

Export to Sheets

---

### 🧠 How to calculate the distance

When you see a TTL in an Nmap trace, it is almost never the exact number above because the packet has passed through routers. To find the "Starting TTL," round the number you see **up** to the nearest milestone (64, 128, or 255).

**Example Calculation:** If your Nmap trace says `ttl=52`:

1. The nearest milestone is **64**.
    
2. Calculation: 64−52=12.
    
3. **Conclusion:** The target is a **Linux** machine located **12 hops** away.
    

If your Nmap trace says `ttl=117`:

1. The nearest milestone is **128**.
    
2. Calculation: 128−117=11.
    
3. **Conclusion:** The target is a **Windows** machine located **11 hops** away.
    

---

### 📝 Obsidian Note Snippet

> **TTL Fingerprinting Cheat Sheet:**
> 
> - **64** = Linux / Mac / Android / FreeBSD
>     
> - **128** = Windows
>     
> - **255** = Cisco / Solaris / Network Infrastructure
>     
> 
> _Formula: `Hops = Default TTL - Captured TTL`_