

> [!INFO]  
> Network types classify networks based on:
> 
> - Geographic scope
>     
> - Communication method
>     
> - Intended usage
>     
> 
> Common real-world terms:
> 
> - WAN
>     
> - LAN
>     
> - WLAN
>     
> - VPN
>     
> 
> Academic/book terms:
> 
> - GAN
>     
> - MAN
>     
> - PAN
>     
> - WPAN
>     

---

# Common Network Types

## WAN (Wide Area Network)

> [!SUMMARY]  
> A WAN connects multiple LANs across large geographical areas.
> 
> The Internet is the most common WAN.

### Characteristics

- Uses public infrastructure
    
- Connects multiple LANs
    
- Often uses public routable IP addresses
    
- Typically uses BGP routing
    

### Important Concepts

- WAN ≠ only Internet
    
- Large organizations can have:
    
    - Internal WANs
        
    - Intranets
        
    - Air-gapped WAN environments
        

### Identifying a WAN

- Uses public IP addressing
    
- Uses WAN routing protocols like:
    
    - BGP
        

### RFC1918 Private IP Ranges

```txt
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

> [!NOTE]  
> Networks not using RFC1918 ranges may be publicly routable.

---

## LAN (Local Area Network)

> [!SUMMARY]  
> A LAN is a local internal network covering a limited physical area.

### Examples

- Homes
    
- Offices
    
- Schools
    
- Buildings
    

### Characteristics

- Usually Ethernet-based
    
- High speed
    
- Low latency
    
- Uses private IP ranges
    

### Common Devices

- Switches
    
- Routers
    
- PCs
    
- Printers
    
- Servers
    

---

## WLAN (Wireless Local Area Network)

> [!SUMMARY]  
> WLAN is simply a wireless LAN using Wi-Fi.

### Key Difference

|LAN|WLAN|
|---|---|
|Wired|Wireless|

### Security Implications

> [!WARNING]  
> WLANs introduce additional attack surfaces:
> 
> - Rogue APs
>     
> - Evil Twin attacks
>     
> - Wireless sniffing
>     
> - WPA/WPA2 cracking
>     

### Important Note

Functionally:

- LAN and WLAN are almost identical
    

Main distinction:

- Wireless communication medium
    

---

# VPN (Virtual Private Network)

> [!SUMMARY]  
> A VPN makes a device appear as if it were directly connected to another network.

### Common Uses

- Remote work
    
- Secure communication
    
- Internal resource access
    
- Pentesting labs
    

---

# Types of VPNs

## Site-to-Site VPN

> [!INFO]  
> Connects entire networks together.

### Common Devices

- Routers
    
- Firewalls
    

### Use Case

- Connecting branch offices
    
- Enterprise network linking
    

### Characteristics

- Shares entire network ranges
    
- Transparent communication between sites
    

---

## Remote Access VPN

> [!SUMMARY]  
> A user device connects directly into a remote network.

### Example

Hack The Box uses OpenVPN for lab connectivity.

### Important Components

- Virtual adapters
    
- TUN/TAP interfaces
    
- Routing tables
    

---

### Split-Tunnel VPN

> [!INFO]  
> Only specific traffic goes through the VPN.

### Example

```txt
10.10.10.0/24 -> VPN
Internet traffic -> Local ISP
```

### Advantages

- Better speed
    
- Lower VPN bandwidth usage
    

### Security Risks

> [!WARNING]  
> Malware traffic may bypass corporate monitoring systems if Internet traffic does not traverse the VPN.

---

## SSL VPN

> [!SUMMARY]  
> Browser-based VPN access.

### Characteristics

- Runs in browser
    
- No dedicated client needed
    
- Streams:
    
    - Applications
        
    - Desktops
        
    - Sessions
        

### Example

Hack The Box Pwnbox.

---

# Academic / Book Network Types

> [!NOTE]  
> These terms are more common in:
> 
> - Networking exams
>     
> - Certifications
>     
> - Academic literature
>     

---

## GAN (Global Area Network)

> [!SUMMARY]  
> A worldwide interconnected network.

### Example

- Internet
    

### Infrastructure

- Fiber optic backbones
    
- Undersea cables
    
- Satellite links
    

### Enterprise Usage

Large corporations may maintain private global WAN infrastructures.

---

## MAN (Metropolitan Area Network)

> [!SUMMARY]  
> Connects multiple LANs across a city or regional area.

### Characteristics

- High-speed fiber connections
    
- Regional scope
    
- Very high throughput
    

### Common Uses

- University campuses
    
- ISP infrastructure
    
- Multi-building enterprises
    

---

## PAN (Personal Area Network)

> [!SUMMARY]  
> A short-range personal device network.

### Examples

- USB tethering
    
- Direct cable connection between devices
    

### Range

- Usually a few meters
    

---

## WPAN (Wireless Personal Area Network)

> [!SUMMARY]  
> Wireless version of PAN.

### Technologies

- Bluetooth
    
- Wireless USB
    

### Bluetooth Networks

> [!INFO]  
> Bluetooth-based WPANs are called:
> 
> **Piconets**

---

### IoT Relevance

> [!NOTE]  
> WPAN technologies are heavily used in:
> 
> - Smart homes
>     
> - Home automation
>     
> - IoT ecosystems
>     

### Common Protocols

- ZigBee
    
- Z-Wave
    
- Insteon
    

---

# Quick Comparison Table

|Type|Scope|Medium|Example|
|---|---|---|---|
|WAN|Large/global|Wired/Wireless|Internet|
|LAN|Local|Wired|Office network|
|WLAN|Local|Wireless|Home Wi-Fi|
|VPN|Virtual overlay|Both|Remote access|
|GAN|Worldwide|Both|Global enterprise network|
|MAN|City/Regional|Fiber|Campus backbone|
|PAN|Personal|Wired|USB tethering|
|WPAN|Personal|Wireless|Bluetooth|

---

# Pentesting Relevance

## WAN

> [!TIP]  
> Focus Areas:
> 
> - Internet-facing services
>     
> - BGP attacks
>     
> - DDoS exposure
>     

---

## LAN

> [!TIP]  
> Focus Areas:
> 
> - Internal enumeration
>     
> - SMB exploitation
>     
> - Lateral movement
>     
> - Active Directory attacks
>     

---

## WLAN

> [!TIP]  
> Focus Areas:
> 
> - Wi-Fi cracking
>     
> - Evil Twin attacks
>     
> - Rogue AP deployment
>     

---

## VPN

> [!TIP]  
> Focus Areas:
> 
> - Credential attacks
>     
> - VPN appliance vulnerabilities
>     
> - Split tunnel weaknesses
>     

---

## WPAN / IoT

> [!TIP]  
> Focus Areas:
> 
> - Bluetooth exploitation
>     
> - Weak IoT protocols
>     
> - Smart device attacks
>     

---

# Key Takeaways

> [!SUCCESS]
> 
> - WAN = large interconnected networks
>     
> - LAN = local wired network
>     
> - WLAN = wireless LAN
>     
> - VPN = virtual remote network access
>     
> - MAN/GAN = regional/global network concepts
>     
> - PAN/WPAN = short-range personal networking
>     
> - Wireless networks introduce additional security risks
>     
> - VPN routing behavior matters heavily during pentesting and detection analysis
>