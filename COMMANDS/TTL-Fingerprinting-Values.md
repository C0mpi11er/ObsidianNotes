# 🔍 Why TTL Matters (For Hackers)

> [!success] Use Case  
> You can:

- **Guess OS of target**
- Estimate **network distance (hops)**
- Detect **firewalls / NAT / spoofing**

---

# ⚡ How to Get TTL

ping <target>

Example:

ttl=57

👉 This is the **remaining TTL**, not the original

---

# 🧠 TTL OS Fingerprinting

> [!tip] Core idea  
> Different OS = different **default TTL**

👉 You estimate original TTL by rounding up

---

# 📊 Quick TTL Table (MOST IMPORTANT)

> [!NOTE]
> |TTL Seen|Likely OS|
> |---|---|
> |~64|Linux / Unix / Mac|
> |~128|Windows|
> |~255|Network devices (Cisco, routers)|
> 
> 📌 Common defaults:
> 
> - Linux/macOS → **64**
> - Windows → **128**
> - Cisco → **254–255**

ping</target>




---

# 🌐 TTL Reference Table — Full Breakdown

---

> [!info] How to Use This Table

- Compare **observed TTL** from `ping`
    
- Match with closest **default TTL**
    
- Infer:
    
    - OS type
        
    - Network distance
        

---

## 🐧 Unix / Linux / BSD Family

> [!success] Most important group (you’ll see this A LOT)

| OS / Device | Version     | Protocol     | TTL      |
| ----------- | ----------- | ------------ | -------- |
| Linux       | Red Hat 9   | ICMP/TCP     | 64       |
| Linux       | 2.x kernels | ICMP         | 64 / 255 |
| FreeBSD     | Various     | TCP/UDP      | 64       |
| FreeBSD     | 3.x–4.x     | ICMP         | 255      |
| OpenBSD     | 2.6–2.7     | ICMP         | 255      |
| NetBSD      | —           | ICMP         | 255      |
| MacOS       | Modern (X)  | ICMP/TCP/UDP | 64       |
| MacOS       | Older       | TCP/UDP      | 60       |

---

> [!tip] Quick takeaway  
> 👉 Most Unix/Linux systems → **TTL ≈ 64**

---

## 🪟 Windows Family

> [!success] Easiest to fingerprint

|OS|Version|Protocol|TTL|
|---|---|---|---|
|Windows 95/98|—|TCP/UDP|32|
|Windows 98|ICMP|32 / 128||
|Windows NT|Various|TCP/UDP|32 / 128|
|Windows 2000|—|ICMP/TCP/UDP|128|
|Windows XP|—|ICMP/TCP/UDP|128|
|Windows Vista|—|ICMP/TCP/UDP|128|
|Windows 7|—|ICMP/TCP/UDP|128|
|Windows 10|—|ICMP/TCP/UDP|128|
|Windows Server (2003–2008)|—|ICMP/TCP/UDP|128|

---

> [!tip] Quick takeaway  
> 👉 Modern Windows → **TTL ≈ 128**

---

## 🛜 Network Devices / Routers

> [!warning] Often used in labs & real infra

|Device|Protocol|TTL|
|---|---|---|
|Cisco|ICMP|254|
|Juniper|ICMP|64|
|Netgear|ICMP/UDP|64|
|Foundry|ICMP|64|

---

> [!tip] Quick takeaway  
> 👉 Network gear → **TTL ≈ 254–255**

---

## 🖥️ Enterprise / Legacy Unix Systems

> [!info] Less common but exam-worthy

|OS|Version|Protocol|TTL|
|---|---|---|---|
|AIX|—|TCP|60|
|AIX|—|UDP|30|
|AIX|3.x–4.x|ICMP|255|
|Solaris|2.x|ICMP|255|
|Solaris|2.8|TCP|64|
|HP-UX|10.x|TCP/UDP|64|
|HP-UX|11|ICMP|255|
|Irix|5.x–6.x|TCP/UDP|60|
|Irix|6.5|ICMP|255|

---

## 🧬 Misc / Rare Systems

> [!note] Mostly for completeness

|OS|Protocol|TTL|
|---|---|---|
|MPE/IX (HP)|ICMP|200|
|Stratus|TCP/UDP|30 / 64|
|SunOS|TCP/UDP|60|
|Ultrix|TCP|60|
|Ultrix|UDP|30|
|Ultrix|ICMP|255|
|OpenVMS|ICMP|255|

---

## 🧪 VMS Variants

|OS|Protocol|TTL|
|---|---|---|
|VMS/Multinet|TCP/UDP|64|
|VMS/TCPware|TCP|60|
|VMS/TCPware|UDP|64|
|VMS/Wollongong|TCP|128|
|VMS/Wollongong|UDP|30|
|VMS/UCX|TCP/UDP|128|

---

# ⚡ SHORT VERSION (WHAT YOU ACTUALLY USE)

> [!success] Memorize this only

|OS Type|Default TTL|
|---|---|
|Linux / Unix|64|
|Windows|128|
|Network Devices|254–255|

---

# 🧠 Real Pentest Shortcut

> [!example]

Observed:

```text
ttl=61
```

👉 Likely:

- Linux (64 base)
    
- 3 hops away
    

---

> [!example]

Observed:

```text
ttl=127
```

👉 Likely:

- Windows (128 base)
    
- 1 hop away
    

---

# ⚠️ Important Notes

> [!warning]

- TTL can be modified
    
- Firewalls may rewrite it
    
- Always combine with:
    
    - Nmap
        
    - Banner grabbing
        
    - Service detection
        

---

# 🧠 Memory Trick

> [!quote]  
> 64 → Linux  
> 128 → Windows  
> 255 → Routers

---

# 🔥 Final Takeaway

> [!summary]  
> TTL is not exact — it’s a **fast OS guessing trick** that gives you an edge during recon.

---

