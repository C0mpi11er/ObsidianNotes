

> [!INFO]  
> A **proxy** is an intermediary system that sits between a client and a destination server and can **inspect, modify, or forward traffic**.

---

# Core Concept

> [!SUMMARY]  
> A proxy = **mediator in a communication flow**.

### Key Idea

- Client does NOT talk directly to server
    
- Proxy sits in the middle
    
- Proxy can:
    
    - Inspect traffic (Layer 7 focus)
        
    - Filter traffic
        
    - Modify requests/responses
        
    - Log activity
        

> [!NOTE]  
> If a device cannot inspect traffic, it is typically a **gateway**, not a proxy.

---

# OSI Perspective

> [!INFO]  
> Proxies usually operate at **Layer 7 (Application Layer)**

### Why Layer 7 matters

- Works with HTTP/HTTPS, DNS, etc.
    
- Can interpret application data
    
- Enables filtering and inspection
    

---

# Common Misconceptions

> [!WARNING]  
> Not everything that changes your IP is a proxy.

### Common myths

- VPN ≠ proxy (in most cases)
    
- IP masking ≠ proxy by default
    
- “Netflix region change tools” are often VPNs, not proxies
    

---

# Types of Proxies

---

## 1. Forward Proxy

> [!SUMMARY]  
> A proxy that sits **between client and internet**.

### Flow

```
Client → Forward Proxy → Internet → Server
```

### Use Cases

- Corporate internet filtering
    
- Web access control
    
- Malware inspection (defensive environments)
    
- Tools like Burp Suite for HTTP interception
    

### Key Behavior

- Client is aware of proxy (often)
    
- Outbound traffic is controlled
    

> [!TIP]  
> Browsers like Chrome/Edge use system proxy settings by default.

---

## Security Relevance

> [!INFO]  
> Forward proxies are heavily used in enterprise security.

### Why they matter

- Can block malware downloads
    
- Can enforce web policies
    
- Can log all HTTP requests
    

### Malware Considerations

- Proxy-aware malware can bypass filtering
    
- Non-proxy-aware malware may fail in restricted environments
    

---

## 2. Reverse Proxy

> [!SUMMARY]  
> A proxy that sits **in front of servers and handles inbound traffic**.

### Flow

```
Client → Internet → Reverse Proxy → Internal Server
```

### Use Cases

- Load balancing
    
- Security filtering
    
- DDoS protection
    
- Hiding internal infrastructure
    

### Examples

- Cloudflare-style protection layers
    
- Web Application Firewalls (WAFs)
    

---

## Security Relevance

> [!WARNING]  
> Reverse proxies are a major defensive control in enterprise networks.

### Functions

- Filters incoming traffic
    
- Blocks malicious requests
    
- Masks backend servers
    
- Distributes load across servers
    

### Pentesting Context

- Can hide real application endpoints
    
- Can interfere with enumeration
    
- May alter request behavior
    

---

## 3. Transparent Proxy

> [!SUMMARY]  
> A proxy that operates **without client awareness**.

### Key Idea

- Client does NOT know it exists
    
- Traffic is automatically intercepted
    

### Characteristics

- No client configuration needed
    
- Often enforced at network level
    
- Acts as a hidden filter
    

### Example Use Cases

- Corporate web filtering
    
- ISP traffic inspection
    
- School networks
    

> [!WARNING]  
> Often used in restrictive environments where users cannot bypass filtering.

---

## 4. Non-Transparent Proxy

> [!SUMMARY]  
> A proxy that requires explicit configuration on the client.

### Key Idea

- Client must be configured manually or via policy
    

### Characteristics

- Proxy settings required
    
- Without configuration → no internet access
    
- Full control over outbound traffic path
    

---

# Tools & Real-World Examples

## Security Tools

- Burp Suite (HTTP proxy / interception)
    
- SOCKS proxies
    
- SSH tunneling tools (pivoting scenarios)
    

## Enterprise Tools

- Web filters
    
- Firewalls
    
- WAFs (e.g., ModSecurity)
    

## CDN / Security Layers

- Cloudflare-style reverse proxy systems
    

---

# Security / Pentesting Relevance

> [!TIP]  
> Proxies are critical in both attack and defense.

## Forward Proxy Relevance

- Traffic interception
    
- Web request analysis
    
- Malware command-and-control restrictions
    

## Reverse Proxy Relevance

- Hides real infrastructure
    
- Blocks attack traffic
    
- Complicates enumeration
    
- Can terminate TLS and inspect traffic
    

## Transparent Proxy Relevance

- Hard to detect
    
- Used in restrictive environments
    
- Can silently filter or log traffic
    

---

# Key Takeaways

> [!SUCCESS]
> 
> - A proxy is a **mediator that can inspect traffic**
>     
> - Most proxies operate at **Layer 7**
>     
> - Forward proxy = client-side control
>     
> - Reverse proxy = server-side protection
>     
> - Transparent proxy = hidden interception
>     
> - Non-transparent proxy = explicitly configured
>     
> - Proxies are central in **web security, enterprise filtering, and penetration testing**
>