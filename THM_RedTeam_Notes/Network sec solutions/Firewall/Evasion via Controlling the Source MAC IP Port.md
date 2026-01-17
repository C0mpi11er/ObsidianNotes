

## Overview

- Firewalls often detect and block port scans.
    
- Nmap provides techniques to **evade firewall detection** during network and port scanning.
    

## Evasion Technique Categories

1. **Control Source MAC / IP / Port**
    
2. **Fragmentation, MTU, and Data Length** _(covered later)_
    
3. **Header Field Manipulation** _(covered later)_
    

---

## Default Nmap Stealth (SYN) Scan

**Command**

`nmap -sS -Pn -F MACHINE_IP`

**Options Explained**

- `-sS` → Stealth (SYN) scan
    
- `-Pn` → Skip host discovery (assume host is up)
    
- `-F` → Scan top 100 common ports
    

**Observed Packet Characteristics**

- ~200 packets sent
    
    - 100 ports × ~2 SYN packets per port
        
- Source port: Random (e.g., 37710)
    
- IP packet size: **44 bytes**
    
    - IP header: 20 bytes
        
    - TCP header: 24 bytes
        
- TCP data payload: **0 bytes**
    
- TTL: **42**
    
- No checksum errors
    

---

## Evasion via Source Manipulation

### 1. Decoy Scanning

- Mix real IP with fake source IPs to hide the true scanner.
    
- Increases blue team workload.
    

**Command Example**

`nmap -sS -Pn -D 10.10.10.1,10.10.10.2,ME -F MACHINE_IP`

- `ME` = real IP address
    
- If `ME` is omitted, Nmap inserts it randomly.
    

**Random Decoys**

`nmap -sS -Pn -D RND,RND,ME -F MACHINE_IP`

**Effect**

- Each decoy generates packets.
    
- 4 sources ≈ **~800 packets** (nearest 100)
    

---

### 2. Proxy Scanning

- Routes scans through an HTTP or SOCKS4 proxy.
    
- Target sees **proxy IP**, not attacker IP.
    

**Command**

`nmap -sS -Pn --proxies PROXY_IP -F MACHINE_IP`

- Multiple proxies can be chained using commas.
    

---

### 3. Spoofed MAC Address

- Spoofs Layer 2 identity.
    
- Works **only on the same network segment**.
    

**Command**

`nmap --spoof-mac MAC_ADDRESS`

**Use Cases**

- Exploit MAC-based trust
    
- Hide scan source (e.g., impersonate printer)
    

---

### 4. Spoofed Source IP Address

- Fakes source IP address.
    
- Useful only if:
    
    - On the same subnet, or
        
    - You control the spoofed IP host
        

**Command**

`nmap -S IP_ADDRESS`

**Example**

- Make scans appear from `10.10.0.254`
    

`-S 10.10.0.254`

---

### 5. Fixed Source Port Number

- Bypass firewall rules that trust certain ports.
    
- Common trusted ports:
    
    - TCP 80 / 443 (web)
        
    - UDP 53 (DNS)
        

**Command**

`nmap -sS -Pn -g PORT -F MACHINE_IP`

**Example**

`nmap -sS -Pn -g 8080 -F MACHINE_IP`

**UDP Scan with DNS Source Port**

`nmap -sU -F -g 53 MACHINE_IP`

---

## Quick Reference Table

| Evasion Technique | Nmap Option                      |
| ----------------- | -------------------------------- |
| Decoy scanning    | `-D DECOY1,DECOY2,ME`            |
| Random decoys     | `-D RND,RND,ME`                  |
| Proxy relay       | `--proxies PROXY_URL`            |
| Spoof MAC address | `--spoof-mac MAC`                |
| Spoof IP address  | `-S IP`                          |
| Fixed source port | `-g PORT` / `--source-port PORT` |