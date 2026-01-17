
## Packet Fragmentation Techniques

### Fragment into 8-Byte Chunks (`-f`)

- Limits **IP data** to **8 bytes per fragment**
    
- TCP header = 24 bytes → split into **3 IP fragments**
    

**Command**

`nmap -sS -Pn -f -F MACHINE_IP`

**IP Packet Size**

- IP header: 20 bytes
    
- Data: 8 bytes
    
- **Total: 28 bytes**
    

---

### Fragment into 16-Byte Chunks (`-ff`)

- Limits **IP data** to **16 bytes**
    
- TCP header split into **16 + 8 bytes**
    

**Command**

`nmap -sS -Pn -ff -F MACHINE_IP`

**Maximum IP Packet Size**

- IP header: 20 bytes
    
- Data: 16 bytes
    
- **Total: 36 bytes**
    

---

### Fragment Using Custom MTU (`--mtu`)

- `--mtu VALUE` sets **IP data size only**
    
- Must be a **multiple of 8**
    
- IP header **not included** in MTU value
    

**Command**

`nmap -sS -Pn --mtu 36 -F MACHINE_IP`

**Maximum IP Packet Size**

- IP header: 20 bytes
    
- Data (MTU): 36 bytes
    
- **Total: 56 bytes**
    

> Note: `--mtu 8` behaves exactly like `-f`

---

## Packet Length Manipulation

### Add Padding Data (`--data-length`)

- Pads TCP segment with random data
    
- Helps evade size-based detection
    
- Value must be a **multiple of 8**
    

**Command**

`nmap -sS -Pn --data-length 128 -F MACHINE_IP`

**IP Packet Size**

- IP header: 20 bytes
    
- TCP header: 24 bytes
    
- TCP data: 128 bytes
    
- **Total: 172 bytes**
    

---

## Quick Reference Table

| Evasion Technique              | Nmap Option         |
| ------------------------------ | ------------------- |
| Fragment IP data into 8 bytes  | `-f`                |
| Fragment IP data into 16 bytes | `-ff`               |
| Fragment using MTU             | `--mtu VALUE`       |
| Specify packet length          | `--data-length NUM` |