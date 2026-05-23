
---

# 🌐 TCP/IP Model

> [!info] Overview  
> The **TCP/IP model** (Internet Protocol Suite) is a layered networking model used for communication over networks like the Internet.  
> It is built around two core protocols:
> 
> - **TCP (Transmission Control Protocol)** → reliability and connection handling
>     
> - **IP (Internet Protocol)** → addressing and routing
>     

---

## 🧱 TCP/IP Layers

> [!layer] TCP/IP Layer Structure

|Layer|Name|Function|
|---|---|---|
|4|Application|Provides services to applications and defines communication protocols|
|3|Transport|Handles end-to-end communication using TCP or UDP|
|2|Internet|Handles logical addressing and routing of packets|
|1|Link|Handles physical transmission over network medium|

---

## 📡 Layer Breakdown

### 🧑‍💻 Application Layer

> [!note]  
> This layer allows applications to communicate over the network.

Protocols include:

- HTTP / HTTPS
    
- DNS
    
- SSH
    
- FTP
    

Purpose:

- Defines how data is formatted and interpreted by applications
    

---

### 🚚 Transport Layer

> [!important]  
> Responsible for **end-to-end communication between hosts**

Protocols:

- TCP → reliable, connection-oriented
    
- UDP → fast, connectionless
    

Functions:

- segmentation & reassembly
    
- flow control
    
- error handling (TCP)
    
- port-based communication
    

---

### 🌍 Internet Layer

> [!info]  
> Handles routing of packets across networks using IP.

Core protocol:

- IP (Internet Protocol)
    

Functions:

- logical addressing (IP addresses)
    
- routing between networks
    
- packet forwarding
    

Key concepts:

- subnetting
    
- CIDR
    
- routing tables
    

---

### 🔗 Link Layer

> [!note]  
> Handles communication on the local network segment.

Functions:

- MAC addressing
    
- framing data
    
- sending/receiving frames over physical medium
    
- Ethernet / Wi-Fi transmission
    

---

## 🧠 Core TCP/IP Functions

### 🧭 Logical Addressing (IP)

> [!info]  
> IP provides structured addressing across networks.

- Uses IPv4 / IPv6 addresses
    
- Ensures packets reach correct network
    
- Supports subnetting and CIDR
    

---

### 📦 Routing

> [!important]  
> Routers determine the next hop for packets.

- Each router reads destination IP
    
- Forwards packet toward destination
    
- Path is dynamic, not fixed
    

---

### 🔁 Error & Flow Control (TCP)

> [!warning]  
> TCP ensures reliable delivery of data.

- Connection establishment (3-way handshake)
    
- Packet ordering
    
- Retransmission of lost packets
    
- Connection maintenance
    

---

### 🔌 Application Support (Ports)

> [!note]  
> Ports identify specific services on a host.

Examples:

- HTTP → 80
    
- HTTPS → 443
    
- SSH → 22
    

---

### 🌐 Name Resolution (DNS)

> [!info]  
> DNS converts domain names into IP addresses.

Example:

```
google.com → 142.250.x.x
```

---

## ⚖️ OSI vs TCP/IP

> [!compare]  
> OSI is a 7-layer theoretical model, while TCP/IP is a 4-layer practical implementation.

|OSI|TCP/IP|
|---|---|
|7 layers|4 layers|
|Theoretical|Practical|
|More detailed separation|Layers combined|

---

## 🧠 Key Idea Summary

> [!summary]
> 
> - IP → routes packets across networks
>     
> - TCP → ensures reliable delivery
>     
> - Ports → map traffic to applications
>     
> - DNS → resolves names to IPs
>     
> - Link layer → handles physical transmission
>     

---

