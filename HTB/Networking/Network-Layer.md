
---

# 
> [!info] Overview  
> The **Network Layer (Layer 3)** is responsible for the transfer of data packets between different networks.  
> Since packets cannot be sent directly to the destination in most cases, they are forwarded through **intermediate routing nodes (routers)** until they reach their target.

---

## 🧭 Core Responsibility

> [!important]  
> The Network Layer ensures that data packets are delivered from source to destination across multiple networks.

It performs:

- Logical addressing
    
- Routing between networks
    
- Packet forwarding via intermediate nodes
    
- Basic traffic management (flow control at network level)
    

---

## 📦 How Packet Delivery Works

> [!note]  
> Packets do not travel directly from sender to receiver.

Instead:

1. Packet is created at source
    
2. Routed to first router (node)
    
3. Each router examines destination IP
    
4. Packet is forwarded hop-by-hop
    
5. Continues until destination is reached
    

> [!warning]  
> Routers do **not process upper-layer data (L4–L7)**.  
> They only inspect and forward based on **Layer 3 information (IP addresses)**.

---

## 🧠 Key Functions

> [!summary]  
> The Network Layer is responsible for:

- Logical addressing (IP addressing)
    
- Routing between networks
    
- Packet forwarding
    
- Path selection via routing tables
    

---

## 🌍 Routing Concept

> [!info]  
> Routing is the process of determining the best path for data packets across networks.

- Each router maintains a **routing table**
    
- The table defines next-hop paths
    
- Routing can be dynamic or static
    

---

## 🔌 Protocols at Network Layer

> [!example]  
> Common Layer 3 protocols include:

- **IPv4 / IPv6** → addressing and packet delivery
    
- **ICMP** → diagnostics (ping, error reporting)
    
- **IGMP** → multicast group management
    
- **IPsec** → secure IP communication
    
- **RIP** → distance-vector routing protocol
    
- **OSPF** → link-state routing protocol
    

---

## 🌐 Inter-Network Communication

> [!note]  
> The Network Layer enables communication between **different networks (subnets)**, even if they use different addressing schemes.

- Handles subnet-to-subnet communication
    
- Bridges incompatible networks via routers
    
- Ensures global connectivity (Internet-scale routing)
    

---

## 🔁 Packet Forwarding Behavior

> [!important]  
> When packets are forwarded:

- They are **not passed up to higher OSI layers** at intermediate routers
    
- Instead, they are **repackaged for the next hop**
    
- Destination IP remains the key routing reference
    

---

## 🧠 Key Takeaway

> [!summary]  
> The Network Layer is the backbone of inter-network communication:
> 
> - IP provides addressing
>     
> - Routers provide forwarding
>     
> - Routing tables define the path
>     
> - Packets move hop-by-hop until delivery
>     

---

