
# Nmap Firewall Evasion — Header Field Manipulation

## Overview

Nmap allows manipulation of **IP and TCP/UDP header fields** to evade firewalls and IDS/IPS systems. These techniques exploit how security devices inspect or mishandle abnormal headers.

You can evade detection by:

- Modifying **IP Time-To-Live (TTL)**
    
- Injecting **IP Options**
    
- Sending packets with **invalid checksums**
    

---

## Set IP Time-To-Live (TTL)

### Concept

- **TTL** limits how many hops a packet can traverse.
    
- Firewalls may be positioned multiple hops away.
    
- Low TTL values can cause packets to expire **before reaching the firewall or target**.
    

### Nmap Option

`--ttl VALUE`

### Example

`nmap -sS -Pn --ttl 81 -F TARGET_IP`

### Practical Notes

- `--ttl 1` → Packet expires very quickly (may not reach target)
    
- `--ttl 2` → Reaches slightly further into the network
    
- Results (open ports) vary depending on:
    
    - Network topology
        
    - Whether using AttackBox or VPN
        

---

## Set IP Options

### Concept

- IP header contains an **Options field** (rarely used in normal traffic)
    
- Some security devices mishandle or ignore packets with IP options
    
- Can be used to influence routing or evade inspection
    

### Nmap Option

`--ip-options HEX_STRING`

### Byte Format

- Each byte written as: `\xHH` (hexadecimal)
    

### Shortcut Flags

|Flag|Meaning|
|---|---|
|`R`|Record Route|
|`T`|Record Timestamp|
|`U`|Record Route + Timestamp|
|`L`|Loose Source Routing (followed by IP list)|
|`S`|Strict Source Routing (followed by IP list)|

### Red Team Use Case

- Loose/strict routing can attempt to **bypass specific security devices** by forcing alternate paths
    

---

## Send Packets with Wrong Checksum

### Concept

- TCP/UDP checksum ensures packet integrity
    
- Some systems:
    
    - Drop packets with bad checksums
        
    - IDS/IPS may still process them
        
- Creates a **detection mismatch** between security device and target
    

### Nmap Option

`--badsum`

### Example

`nmap -sS -Pn --badsum -F TARGET_IP`

### Observed Behavior

- Target drops packets
    
- Nmap reports:
    

`All scanned ports are filtered`

### Wireshark Insight

- Incorrect checksums are highlighted
    
- Useful for fingerprinting firewall and OS behavior
    

---

## Summary Table

|Evasion Technique|Nmap Option|
|---|---|
|Set IP TTL|`--ttl VALUE`|
|Specify IP Options|`--ip-options OPTIONS`|
|Send invalid checksums|`--badsum`|