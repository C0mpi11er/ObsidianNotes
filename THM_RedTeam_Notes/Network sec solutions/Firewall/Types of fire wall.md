## 1. Classification by Implementation

### **Hardware Firewall (Appliance)**

- Dedicated hardware device; all traffic passes through it.
    
- Examples:
    
    - Cisco ASA (Adaptive Security Appliance)
        
    - WatchGuard Firebox
        
    - Netgate pfSense Plus
        

### **Software Firewall**

- Installed as part of the OS or as additional software.
    
- Examples:
    
    - Windows Defender Firewall (Windows)
        
    - `iptables` / `firewalld` (Linux)
        

---

## 2. Classification by Usage / Scope

### **Personal Firewall**

- Protects a single system or small network (home environment).
    
- Examples:
    
    - Bitdefender BOX
        
    - Built-in router/access point firewalls (Linksys, D-Link)
        

### **Commercial Firewall**

- Protects medium-to-large networks.
    
- Features: higher reliability, processing power, bandwidth handling.
    
- Typical in universities, enterprises, and corporate networks.
    

---

## 3. Classification by Inspection Abilities (ISO/OSI Layers)

- Firewalls primarily inspect **Layers 3 (Network) & 4 (Transport)**.
    
- Next-Generation Firewalls (NGFW) inspect up to **Layers 5–7 (Session, Presentation, Application)**.
    
- More layers → more sophisticated → higher processing requirements.
    

---

## 4. Types of Firewalls by Function

### **Packet-Filtering Firewall**

- Stateless inspection.
    
- Inspects: Protocol, Source/Destination IP, Source/Destination Port (TCP/UDP).
    

### **Circuit-Level Gateway**

- Adds checks for TCP three-way handshake.
    
- Builds on packet-filtering capabilities.
    

### **Stateful Inspection Firewall**

- Tracks established TCP sessions.
    
- Blocks packets not part of an established session.
    

### **Proxy Firewall / Application Firewall / Web Application Firewall (WAF)**

- Masquerades as the client, forwards requests, inspects packet **payload**.
    
- Typically for web applications.
    
- Limited protocol coverage outside HTTP/S.
    

### **Next-Generation Firewall (NGFW)**

- Monitors Layers 2–7.
    
- Features: Application awareness, control, deep inspection.
    
- Examples: Juniper SRX series, Cisco Firepower.
    

### **Cloud Firewall / Firewall as a Service (FWaaS)**

- Cloud-based firewall solution; replaces hardware in cloud networks.
    
- Benefits: Scalability, NGFW-level features.
    
- Examples:
    
    - Cloudflare Magic Firewall (network-level)
        
    - Juniper vSRX (cloud NGFW)
        
    - AWS WAF (web app protection)
        
    - AWS Shield (DDoS protection)