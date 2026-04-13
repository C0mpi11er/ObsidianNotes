# Network Mapper (Nmap)

> [!ABSTRACT] **Nmap** is an open-source tool written in C, C++, Python, and Lua used for network discovery, security auditing, and vulnerability assessment. It functions by sending raw packets to a target to analyze responses.

---

## 📋 Core Functions

- **Host Discovery:** Identifying live hosts on a network.
    
- **Port Scanning:** Identifying open, closed, or filtered ports.
    
- **Service Detection:** Determining application names and versions.
    
- **OS Detection:** Identifying the operating system and version of the remote host.
    
- **NSE (Nmap Scripting Engine):** Scriptable interaction with targets for advanced discovery or vulnerability detection.
    

## 🛠️ Use Cases

- **Security Auditing:** Evaluating network security posture.
    
- **Penetration Testing:** Simulating attacks to find weaknesses.
    
- **Network Mapping:** Visualizing network topology.
    
- **Firewall/IDS Testing:** Verifying if security rules are correctly configured.
    

---

## ⚙️ Architecture & Syntax

The basic syntax follows a simple structure:

Bash

```
nmap <scan_types> <options> <target>
```

### Key Scanning Techniques

|Flag|Description|
|---|---|
|`-sS`|**TCP SYN Scan** (Default, Stealthy/Half-open)|
|`-sT`|TCP Connect Scan|
|`-sU`|UDP Scan|
|`-sA`|TCP ACK Scan|
|`-sV`|Service Version Detection|
|`-O`|OS Fingerprinting|

Export to Sheets

---

## 🔍 Deep Dive: TCP SYN Scan (`-sS`)

The **SYN Scan** is the most popular method because it is fast (scanning thousands of ports per second) and relatively stealthy. It performs a "half-open" connection by never completing the TCP three-way handshake.

### Response Analysis

Nmap determines the port state based on the packet returned by the target:

1. **Open:** Target returns `SYN-ACK`.
    
2. **Closed:** Target returns `RST` (Reset).
    
3. **Filtered:** No response received (indicates a firewall dropped or ignored the packet).
    

### Example Output

Bash

```
# Example: sudo nmap -sS localhost
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5432/tcp open  postgresql
5901/tcp open  vnc-1
```