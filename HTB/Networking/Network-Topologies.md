

> [!INFO]  
> A network topology defines:
> 
> - How devices are connected
>     
> - How data flows through the network
>     
> - The physical or logical structure of communication
>     

---

# Core Concepts

## Physical Topology

> [!SUMMARY]  
> The actual physical layout of devices and cables.

### Includes

- Cabling
    
- Device placement
    
- Physical connections
    

### Examples

- Ethernet cables
    
- Fiber optics
    
- Wi-Fi placement
    

---

## Logical Topology

> [!SUMMARY]  
> Describes how data moves through the network regardless of physical layout.

### Focuses On

- Signal flow
    
- Packet transmission
    
- Communication behavior
    

> [!NOTE]  
> Physical and logical topology may differ.

Example:

- Physically star
    
- Logically ring
    

---

# Network Topology Components

## Connections

### Wired

- Coaxial
    
- Fiber optic
    
- Twisted pair
    

### Wireless

- Wi-Fi
    
- Cellular
    
- Satellite
    

---

## Network Nodes

> [!INFO]  
> Nodes are connection points that send, receive, or forward data.

### Examples

- NICs
    
- Repeaters
    
- Hubs
    
- Switches
    
- Routers
    
- Firewalls
    
- Gateways
    

---

# Main Network Topologies

|Topology|Key Idea|
|---|---|
|Point-to-Point|Direct connection between two devices|
|Bus|All devices share one medium|
|Star|Central device connects all hosts|
|Ring|Devices form a circular path|
|Mesh|Multiple redundant connections|
|Tree|Hierarchical star structure|
|Hybrid|Combination of topologies|
|Daisy Chain|Devices connected sequentially|

---

# Point-to-Point Topology

> [!SUMMARY]  
> Simplest topology with a direct connection between two hosts.

### Characteristics

- Dedicated link
    
- Simple communication
    
- Fast and reliable
    

### Common Uses

- Traditional telephony
    
- Direct device communication
    

> [!NOTE]  
> Point-to-Point ≠ Peer-to-Peer (P2P)

---

# Bus Topology

> [!SUMMARY]  
> All hosts share a single communication medium.

### Characteristics

- No central device
    
- Shared cable
    
- Only one host transmits at a time
    

### Advantages

- Cheap
    
- Simple setup
    

### Disadvantages

> [!WARNING]
> 
> - Single cable failure affects entire network
>     
> - Performance drops with more devices
>     
> - Difficult troubleshooting
>     

### Common Medium

- Coaxial cable
    

---

# Star Topology

> [!SUMMARY]  
> All devices connect to a central device.

### Central Devices

- Switch
    
- Hub
    
- Router
    

### Advantages

- Easy management
    
- Easy troubleshooting
    
- Most common modern topology
    

### Disadvantages

> [!WARNING]  
> Central device failure can disrupt the network.

### Important Note

> [!TIP]  
> Most modern Ethernet LANs use star topology.

---

# Ring Topology

> [!SUMMARY]  
> Devices form a circular communication path.

### Characteristics

- Data travels in one direction
    
- Each device forwards traffic
    

### Important Concept: Token

> [!INFO]  
> A token controls which device can transmit data.

### Advantages

- Predictable traffic flow
    
- No packet collisions
    

### Disadvantages

> [!WARNING]  
> A single break can disrupt communication unless redundancy exists.

---

# Mesh Topology

> [!SUMMARY]  
> Devices maintain multiple interconnections.

---

## Full Mesh

> [!INFO]  
> Every node connects to every other node.

### Advantages

- Extremely reliable
    
- High redundancy
    
- Fault tolerant
    

### Disadvantages

- Expensive
    
- Complex cabling
    

---

## Partial Mesh

> [!INFO]  
> Only some devices have multiple connections.

### Advantages

- More scalable
    
- Cheaper than full mesh
    

### Common Usage

- WANs
    
- Enterprise routing
    

---

# Tree Topology

> [!SUMMARY]  
> Hierarchical topology built from multiple star networks.

### Characteristics

- Scalable
    
- Structured hierarchy
    
- Common in enterprise environments
    

### Common Uses

- Corporate buildings
    
- Campus networks
    
- MAN infrastructures
    

> [!TIP]  
> Modern enterprise networks commonly use tree topology.

---

# Hybrid Topology

> [!SUMMARY]  
> Combination of two or more topologies.

### Examples

- Star + Bus
    
- Star + Ring
    

### Advantages

- Flexible
    
- Scalable
    
- Customizable
    

### Disadvantages

> [!WARNING]  
> Can become complex to manage and troubleshoot.

---

# Daisy Chain Topology

> [!SUMMARY]  
> Devices connect sequentially in a chain.

### Characteristics

- Simple layout
    
- Serial-style communication
    

### Common Uses

- Automation systems
    
- CAN bus environments
    
- Industrial systems
    

### Disadvantages

> [!WARNING]  
> Failure of one node may affect downstream devices.

---

# Quick Comparison Table

|Topology|Advantages|Disadvantages|
|---|---|---|
|Point-to-Point|Simple, fast|Limited scalability|
|Bus|Cheap, simple|Single point of failure|
|Star|Easy management|Central device dependency|
|Ring|Predictable traffic|Ring break issues|
|Mesh|High redundancy|Expensive|
|Tree|Scalable|Complex hierarchy|
|Hybrid|Flexible|Complex troubleshooting|
|Daisy Chain|Easy setup|Chain dependency|

---

# Pentesting Relevance

## Star Networks

> [!TIP]  
> Common attack targets:
> 
> - Switches
>     
> - Routers
>     
> - Centralized infrastructure
>     

---

## Mesh Networks

> [!TIP]  
> Important for:
> 
> - Routing analysis
>     
> - Path redundancy
>     
> - Network pivoting
>     

---

## Bus Networks

> [!TIP]  
> Shared medium allows:
> 
> - Traffic sniffing
>     
> - Passive monitoring
>     

---

## Wireless Topologies

> [!TIP]  
> Focus Areas:
> 
> - Rogue AP attacks
>     
> - Signal interception
>     
> - Wireless enumeration
>     

---

# Key Takeaways

> [!SUCCESS]
> 
> - Physical topology = actual layout
>     
> - Logical topology = data flow behavior
>     
> - Star topology is most common today
>     
> - Mesh provides highest redundancy
>     
> - Bus and ring are older but foundational concepts
>     
> - Hybrid networks dominate modern enterprise environments
>     
> - Understanding topology helps during:
>     
>     - Enumeration
>         
>     - Pivoting
>         
>     - Traffic analysis
>         
>     - Network troubleshooting
>