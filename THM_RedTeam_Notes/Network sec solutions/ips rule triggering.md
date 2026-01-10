
- Each **IDS/IPS** uses a specific **rule syntax** to define what traffic to detect or block.
    
- **Snort rule format:**  
    **Rule Header (Rule Options)**
    

---

### Snort Rule Header Components

- **Action:** What to do when a rule matches  
    _(alert, log, pass, drop, reject)_
    
- **Protocol:** `TCP`, `UDP`, `ICMP`, or `IP`
    
- **Source IP / Port:** Can include negation (`!`) and ranges
    
- **Direction:**
    
    - `->` one-way traffic
        
    - `<>` bi-directional traffic
        
- **Destination IP / Port:** Target address and service
    

---

### Example: Drop ICMP Traffic

`drop icmp any any -> any any (msg:"ICMP Ping Scan"; dsize:0; sid:1000020; rev:1;)`

- Drops all ICMP packets
    
- Logs the message **“ICMP Ping Scan”**
    
- Used in **IPS (inline) mode**
    

---

### Signature-Based Detection Example (HTTP Exploitation)

#### Basic Content Match

`alert tcp any any <> any 80 (msg:"Netcat Exploitation"; content:"ncat"; sid:1000030; rev:1;)`

- Alerts when `ncat` appears in traffic to port 80
    

#### Hexadecimal Content Match

`alert tcp any any <> any 80 (msg:"Netcat Exploitation"; content:"|6e 63 61 74|"; sid:1000031; rev:1;)`

- Same detection using hex representation of `ncat`
    

---

### Refined Rule (HTTP POST Only)

`alert tcp any any <> any 80 ( msg:"Netcat Exploitation"; flow:established,to_server; content:"POST"; nocase; http_method; content:"ncat"; nocase; sid:1000032; rev:1; )`

- Triggers only on:
    
    - Established TCP connections
        
    - HTTP POST requests
        
    - Payloads containing `ncat`
        

---

### Log Output Insight

- Alerts include:
    
    - Source and destination IP/port
        
    - Timestamp
        
    - Protocol and packet details
        
- Useful for **forensics and incident analysis**
    

---

### Key Limitations of Signature-Based IDS

- Highly dependent on:
    
    - Signature quality
        
    - Rule updates
        
- Minor payload changes can bypass detection
    
- Ineffective against:
    
    - Obfuscation
        
    - Unknown or modified attack techniques
        

---

### Core Takeaway

> Signature-based IDS/IPS is effective only as far as its rules are accurate, current, and well-written; attackers can evade detection by slightly altering known signatures.