
# TCP/UDP Connections

> [!info] Overview  
> TCP and UDP are transport layer protocols used for transmitting data across networks.

---

# TCP vs UDP

|Feature|TCP|UDP|
|---|---|---|
|Connection Type|Connection-oriented|Connectionless|
|Reliability|Reliable|Unreliable|
|Speed|Slower|Faster|
|Error Checking|Yes|Minimal|
|Packet Recovery|Yes|No|
|Common Usage|Web, Email, SSH|Streaming, Gaming, VoIP|

---

# TCP (Transmission Control Protocol)

> [!important] TCP  
> TCP establishes a reliable connection before transmitting data.

---

# TCP Characteristics

|Feature|Description|
|---|---|
|Reliable Delivery|Guarantees data arrives|
|Error Recovery|Retransmits lost packets|
|Ordered Data|Maintains packet sequence|
|Connection-Oriented|Uses session establishment|

---

# TCP Three-Way Handshake

> [!info] TCP Handshake  
> TCP establishes a connection using:
> 
> 1. SYN
>     
> 2. SYN/ACK
>     
> 3. ACK
>     

---

# TCP Common Uses

- HTTP/HTTPS
    
- SSH
    
- FTP
    
- Email
    
- Remote access
    

---

# UDP (User Datagram Protocol)

> [!important] UDP  
> UDP sends data without establishing a connection.

---

# UDP Characteristics

|Feature|Description|
|---|---|
|Fast|Low overhead|
|Connectionless|No session setup|
|No Recovery|Lost packets ignored|
|Lightweight|Minimal processing|

---

# UDP Common Uses

- Video streaming
    
- VoIP
    
- DNS
    
- Online gaming
    
- Live broadcasts
    

---

> [!warning] UDP  
> UDP prioritizes:
> 
> ```bash
> Speed over reliability
> ```

---

# IP Packet

> [!info] IP Packet  
> An IP packet contains:
> 
> - Header
>     
> - Payload
>     

---

# IP Packet Structure

|Component|Purpose|
|---|---|
|Header|Routing & control information|
|Payload|Actual transmitted data|

---

# Important IP Header Fields

|Field|Purpose|
|---|---|
|Version|IPv4 or IPv6|
|TTL|Packet lifetime|
|Protocol|TCP or UDP|
|Source IP|Sender address|
|Destination IP|Receiver address|
|Checksum|Error detection|
|Flags|Fragmentation control|

---

# TTL (Time To Live)

> [!important] TTL  
> Limits how long packets stay on a network.

Each router:

```bash
Decrements TTL by 1
```

When TTL reaches:

```bash
0
```

The router drops the packet and sends:

```bash
ICMP Time Exceeded
```

---

# Default TTL Values

|OS|Default TTL|
|---|---|
|Windows|128|
|Linux/macOS|64|
|Solaris|255|

---

# IP Identification (IP ID)

> [!info] IP ID  
> Used to identify fragmented packets.

---

# IP ID Analysis

Sequential IP IDs may indicate:

- Multiple IPs belong to same host
    
- Shared packet generation source
    

---

# Record-Route Field

> [!info] Record Route  
> Records routers traversed by packets.

Useful for:

- Network mapping
    
- Route tracing
    
- Diagnostics
    

---

# Ping Record Route Example

```bash
ping -c 1 -R <target>
```

Displays:

- Routers traversed
    
- Packet path
    

---

# Traceroute

> [!important] Traceroute  
> Maps packet paths through networks using:
> 
> - TTL manipulation
>     
> - ICMP responses
>     

---

# Traceroute Process

1. Send packet with TTL=1
    
2. Router drops packet
    
3. Router returns ICMP Time Exceeded
    
4. Increase TTL
    
5. Repeat until destination reached
    

---

# TCP Segment Structure

> [!info] TCP Segment  
> TCP data is encapsulated inside IP packets.

---

# Important TCP Header Fields

|Field|Purpose|
|---|---|
|Source Port|Sending application|
|Destination Port|Receiving application|
|Sequence Number|Packet order tracking|
|Acknowledgment Number|Delivery confirmation|
|Flags|Connection control|
|Window Size|Flow control|
|Checksum|Error detection|

---

# TCP Flags

|Flag|Purpose|
|---|---|
|SYN|Start connection|
|ACK|Acknowledge data|
|FIN|Close connection|
|RST|Reset connection|
|PSH|Push data immediately|

---

# UDP Datagrams

> [!info] UDP Datagram  
> UDP sends small independent packets called:
> 
> ```bash
> Datagrams
> ```

No:

- Handshake
    
- Retransmission
    
- Ordering
    

---

# ICMP (Internet Control Message Protocol)

> [!important] ICMP  
> Used for:
> 
> - Error reporting
>     
> - Diagnostics
>     
> - Network troubleshooting
>     

---

# Common ICMP Messages

|Message|Purpose|
|---|---|
|Echo Request|Ping request|
|Echo Reply|Ping response|
|Destination Unreachable|Cannot reach host|
|Time Exceeded|TTL expired|

---

# Blind Spoofing

> [!warning] Blind Spoofing  
> Attack where forged packets are sent without seeing responses.

---

# Blind Spoofing Techniques

Manipulates:

- Source IP
    
- Destination IP
    
- TCP sequence numbers
    
- Ports
    

---

# Blind Spoofing Goals

- Hijack connections
    
- Disrupt communication
    
- MITM attacks
    
- Traffic interception
    

---

# TCP vs UDP Summary

|TCP|UDP|
|---|---|
|Reliable|Fast|
|Ordered delivery|No guarantees|
|Connection-based|Connectionless|
|Error correction|Minimal checks|
|Higher overhead|Lightweight|

---

# Key Takeaways

> [!summary]
> 
> - TCP provides reliable communication
>     
> - UDP prioritizes speed
>     
> - IP packets contain headers and payloads
>     
> - TTL prevents routing loops
>     
> - Traceroute uses TTL expiration
>     
> - TCP uses sequence and acknowledgment numbers
>     
> - ICMP supports diagnostics and error reporting
>     
> - Blind spoofing manipulates packet headers for attacks
>