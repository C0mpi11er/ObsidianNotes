

> [!info] Overview  
> Internet protocols are standardized communication rules that define how devices exchange data across networks.  
> The two primary transport protocols are:
> 
> - TCP → Reliable & connection-oriented
>     
> - UDP → Fast & connectionless
>     

---

# TCP (Transmission Control Protocol)

> [!important] TCP  
> TCP is a **connection-oriented** protocol that establishes a connection before transmitting data using the:
> 
> ```bash
> Three-Way Handshake
> ```

## Characteristics

- Reliable
    
- Ordered delivery
    
- Error checking
    
- Slower than UDP
    
- Maintains active connection
    

---

## TCP Example

When opening a website:

1. Browser sends HTTP request
    
2. Server responds with webpage data
    
3. TCP keeps the connection active until transfer finishes
    

---

# Common TCP Protocols

|Protocol|Port|Purpose|
|---|---|---|
|SSH|22|Secure remote login|
|Telnet|23|Remote login|
|HTTP|80|Web traffic|
|HTTPS|443|Secure web traffic|
|FTP|20/21|File transfer|
|SMB|445|File/printer sharing|
|SMTP|25|Send emails|
|POP3|110|Retrieve emails|
|IMAP|143|Access emails|
|DNS|53|Resolve domain names|
|SNMP|161/162|Network management|
|LDAP|389|Directory services|
|Kerberos|88|Authentication|
|DHCP|67/68|Dynamic IP assignment|
|RDP|3389|Remote desktop|
|SIP|5060|VoIP signaling|
|PPTP|1723|VPN tunneling|
|MSSQL|1433|Microsoft SQL Server|

---

> [!tip] Important Ports
> 
> - HTTP → `80`
>     
> - HTTPS → `443`
>     
> - SSH → `22`
>     
> - SMB → `445`
>     
> - RDP → `3389`
>     

---

# UDP (User Datagram Protocol)

> [!important] UDP  
> UDP is a **connectionless** protocol that sends packets without verifying delivery.

## Characteristics

- Faster than TCP
    
- No connection setup
    
- No reliability guarantees
    
- Common in streaming & VoIP
    

---

## UDP Example

Video streaming uses UDP because:

- Speed matters more than perfect delivery
    
- Small packet loss is acceptable
    

---

# Common UDP Protocols

|Protocol|Port|Purpose|
|---|---|---|
|DNS|53|Domain resolution|
|DHCP|67/68|IP assignment|
|TFTP|69|File transfer|
|SNMP|161|Network monitoring|
|RIP|520|Routing updates|
|Syslog|514|Log collection|
|VNC|5900|Remote desktop sharing|
|MySQL|3306|Database service|
|PostgreSQL|5432|Database service|
|UPnP|1900|Device discovery|
|IRC|194|Chat protocol|
|IPsec|500|Secure VPN traffic|

---

# TCP vs UDP

|Feature|TCP|UDP|
|---|---|---|
|Connection|Connection-oriented|Connectionless|
|Reliability|Reliable|Unreliable|
|Speed|Slower|Faster|
|Error Checking|Yes|Minimal|
|Use Cases|Web, SSH, Email|Streaming, VoIP, Gaming|

---

# ICMP (Internet Control Message Protocol)

> [!info] ICMP  
> ICMP is used for:
> 
> - Error reporting
>     
> - Network diagnostics
>     
> - Connectivity testing
>     

---

# Common ICMP Functions

|Type|Purpose|
|---|---|
|Echo Request|Ping request|
|Echo Reply|Ping response|
|Destination Unreachable|Host/network unreachable|
|Redirect|Better router available|
|Time Exceeded|TTL expired|

---

# TTL (Time To Live)

> [!important] TTL  
> TTL limits how long a packet can travel across a network.

Each router:

```bash
TTL = TTL - 1
```

When TTL reaches `0`:

- Packet is discarded
    
- ICMP Time Exceeded message is sent back
    

---

# Default TTL Values

|Operating System|Default TTL|
|---|---|
|Linux/macOS|64|
|Windows|128|
|Solaris|255|

---

> [!tip] OS Fingerprinting  
> TTL values can help estimate:
> 
> - Operating system
>     
> - Number of network hops
>     

Example:

```bash
TTL = 122
```

Likely:

- Windows host
    
- ~6 hops away
    

---

# VoIP (Voice over IP)

> [!info] VoIP  
> VoIP allows voice/video communication over the internet instead of traditional phone lines.

Examples:

- Skype
    
- WhatsApp
    
- Zoom
    
- Slack
    

---

# SIP (Session Initiation Protocol)

## Common Ports

|Port|Purpose|
|---|---|
|5060|SIP|
|5061|Secure SIP|

---

# Common SIP Methods

|Method|Purpose|
|---|---|
|INVITE|Start session|
|ACK|Confirm request|
|BYE|End session|
|CANCEL|Cancel request|
|REGISTER|Register user|
|OPTIONS|Query capabilities|

---

> [!warning] SIP Enumeration  
> Attackers can use:
> 
> ```bash
> SIP OPTIONS
> ```
> 
> to:
> 
> - Discover users
>     
> - Identify services
>     
> - Gather VoIP information
>     

---

# Important VoIP File

```bash
SEPxxxx.cnf
```

Cisco IP phone configuration file containing:

- Phone model
    
- Firmware
    
- Network settings
    
- Device parameters
    

---

# Key Takeaways

> [!summary]
> 
> - TCP = reliable but slower
>     
> - UDP = faster but less reliable
>     
> - ICMP is used for diagnostics and error handling
>     
> - TTL helps prevent routing loops
>     
> - SIP powers many VoIP systems
>     
> - Common ports:
>     
>     - SSH → 22
>         
>     - HTTP → 80
>         
>     - HTTPS → 443
>         
>     - SMB → 445
>         
>     - RDP → 3389
>         
> - SIP enumeration can leak information
>