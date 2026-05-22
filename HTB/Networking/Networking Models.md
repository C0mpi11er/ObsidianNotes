# 

> [!INFO]  
> Two main models describe how data moves between hosts:
> 
> - **OSI Model (7 layers)**
>     
> - **TCP/IP Model (4 layers)**
>     

---

# Core Idea

> [!SUMMARY]  
> Both models explain **how data is broken down, transmitted, and rebuilt across a network**.

They represent:

- Data flow between systems
    
- Layered processing of packets
    
- Standardized communication structure
    

---

# OSI Model (Open Systems Interconnection)

> [!SUMMARY]  
> A **7-layer conceptual reference model** used for detailed network analysis.

### Origin

- Developed by **ISO / ITU**
    
- Used for learning, troubleshooting, and security analysis
    

---

## OSI Layers (Top → Bottom)

|Layer|Name|Role|
|---|---|---|
|7|Application|User services (HTTP, FTP, DNS)|
|6|Presentation|Encryption, encoding, compression|
|5|Session|Manages sessions/connections|
|4|Transport|Reliable delivery (TCP/UDP)|
|3|Network|Routing (IP addressing)|
|2|Data Link|MAC addressing, switching|
|1|Physical|Raw bits transmission|

---

> [!TIP]  
> OSI is mainly used for:
> 
> - Network troubleshooting
>     
> - Security mapping
>     
> - Understanding packet flow in detail
>     

---

# TCP/IP Model

> [!SUMMARY]  
> A **practical 4-layer model used by real-world Internet systems**.

### Key Idea

- Represents actual Internet communication
    
- Based on protocol families, not strict layers
    

### Includes protocols like:

- TCP
    
- IP
    
- UDP
    
- ICMP
    

---

## TCP/IP Layers (Top → Bottom)

|Layer|OSI Equivalent|Function|
|---|---|---|
|Application|OSI 5–7|Web, DNS, SSH|
|Transport|OSI 4|TCP/UDP communication|
|Internet|OSI 3|IP routing|
|Link|OSI 1–2|Physical + MAC transmission|

---

# OSI vs TCP/IP

> [!INFO]  
> OSI = theoretical + structured  
> TCP/IP = practical + real-world implementation

|Feature|OSI|TCP/IP|
|---|---|---|
|Layers|7|4|
|Purpose|Reference model|Internet communication|
|Strictness|High|Flexible|
|Usage|Analysis|Real networks|

---

# Packet Transfer (How Data Moves)

> [!SUMMARY]  
> Data moves through layers using a process called **encapsulation and decapsulation**.

---

## Encapsulation (Sender Side)

> [!INFO]  
> Each layer adds its own header before passing data down.

### Flow

```text
Data
→ Transport adds TCP/UDP header = Segment
→ Network adds IP header = Packet
→ Data Link adds MAC header = Frame
→ Physical layer sends Bits
```

---

## Decapsulation (Receiver Side)

> [!INFO]  
> The receiver removes headers layer by layer.

### Flow

```text
Bits → Frame → Packet → Segment → Data
```

---

# PDU (Protocol Data Unit)

> [!SUMMARY]  
> Each layer has a specific data format.

|Layer|PDU Type|
|---|---|
|Application|Data|
|Transport|Segment / Datagram|
|Network|Packet|
|Data Link|Frame|
|Physical|Bits|

---

# Why This Matters (Security View)

> [!TIP]  
> Both models are essential for penetration testing and traffic analysis.

---

## TCP/IP Use

- Quick mapping of real-world communication
    
- Understanding how connections are established
    

---

## OSI Use

- Deep packet inspection
    
- Layer-by-layer attack analysis
    
- Traffic interception (e.g., Wireshark, Burp Suite)
    

---

## Pentesting Mapping

> [!INFO]  
> Attacks often target specific layers:

- **L7 (Application):** XSS, SQLi, SSRF
    
- **L4 (Transport):** port scanning, TCP hijacking
    
- **L3 (Network):** IP spoofing, routing attacks
    
- **L2 (Data Link):** ARP spoofing, sniffing
    

---

# Key Takeaways

> [!SUCCESS]
> 
> - OSI = 7-layer conceptual model for deep analysis
>     
> - TCP/IP = real-world Internet architecture
>     
> - Data moves via **encapsulation → transmission → decapsulation**
>     
> - Each layer adds/removes headers (PDU transformation)
>     
> - OSI helps break problems down; TCP/IP explains real behavior
>     
> - Both are critical for network troubleshooting and penetration testing
>