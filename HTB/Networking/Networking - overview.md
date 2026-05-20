# 🌐 Networking Overview

---

> [!INFO] 🧠 What is Networking?
> 
> A network allows computers and devices to communicate with each other.
> 
> Networking consists of:
> 
> - **Topologies** → how devices are arranged
>     
> - **Mediums** → how data travels
>     
> - **Protocols** → rules for communication
>     
> 
> Examples:
> 
> |Category|Examples|
> |---|---|
> |Topology|Star, Mesh, Tree|
> |Medium|Ethernet, Fiber, Wireless|
> |Protocol|TCP, UDP, IP|
> 
> 💡 As pentesters, networking matters because:
> 
> - Network issues are often **silent**
>     
> - Misunderstanding routing/subnets can cause you to:
>     
>     - miss hosts
>         
>     - fail pivoting
>         
>     - break shells/tunnels
>         
>     - incorrectly report systems as offline
>         

---

# 🏠 Flat Networks vs Segmented Networks

---

## 🟢 Flat Network

> [!NOTE]
> 
> A flat network is when everything exists on the same subnet.
> 
> Example:
> 
> ```text
> 192.168.1.0/24
> ```
> 
> All devices communicate freely.

---

## 🔴 Why Flat Networks Are Dangerous

> [!WARNING]
> 
> Flat networks are easy to attack because:
> 
> - workstations can talk to servers
>     
> - printers can talk to clients
>     
> - attackers can move laterally easily
>     
> - spoofing/MITM attacks become easier
>     
> 
> Once an attacker compromises ONE device,  
> they may reach EVERYTHING.

---

# 🛡️ Network Segmentation

---

> [!TIP]
> 
> Segmentation creates smaller isolated networks.
> 
> Devices communicate through controlled paths.
> 
> This slows attackers significantly.

---

# 🏡 House Analogy

---

## Example 1 — Fences = ACLs

> [!EXAMPLE]
> 
> ACLs (Access Control Lists) are like fences around a property.
> 
> They:
> 
> - create controlled entry points
>     
> - restrict movement
>     
> - make suspicious activity visible
>     
> 
> Example:
> 
> ```text
> Why is the printer network talking to servers over HTTP?
> ```

---

## Example 2 — Lights = Monitoring

> [!EXAMPLE]
> 
> Monitoring/documentation is like putting lights around a property.
> 
> Everything becomes visible.
> 
> Example:
> 
> ```text
> Why is the printer network talking to the internet?
> ```

---

## Example 3 — Bushes = IDS/IPS

> [!EXAMPLE]
> 
> IDS systems like:
> 
> - Suricata
>     
> - Snort
>     
> 
> act as deterrents.
> 
> Example:
> 
> ```text
> Why did a port scan originate from the printer VLAN?
> ```

---

# 🧠 Important Pentesting Lesson

---

> [!DANGER]
> 
> Never assume the subnet mask.
> 
> Many pentesters automatically assume:
> 
> ```text
> /24 = 255.255.255.0
> ```
> 
> without verifying.

---

# 📡 Understanding `/24`

---

> [!INFO]
> 
> `/24` means:
> 
> ```text
> 255.255.255.0
> ```
> 
> All devices sharing the first 3 octets are local.
> 
> Example:
> 
> ```text
> 192.168.1.xxx
> ```

---

# ✂️ Understanding `/25`

---

> [!INFO]
> 
> `/25` means:
> 
> ```text
> 255.255.255.128
> ```
> 
> This SPLITS the network into TWO halves.

---

## Network Split Example

|Network|Range|
|---|---|
|10.20.0.0/25|10.20.0.1 → 10.20.0.126|
|10.20.0.128/25|10.20.0.129 → 10.20.0.254|

---

# 📖 Story Time — Pentester Oversight

---

## Actual Environment

|Device|Address|
|---|---|
|Server Gateway|10.20.0.1/25|
|Domain Controller|10.20.0.10/25|
|Client Gateway|10.20.0.129/25|
|Client Workstation|10.20.0.200/25|
|Pentester|10.20.0.252/24 ❌|

---

# ❌ What The Pentester Did Wrong

> [!FAILURE]
> 
> The pentester configured:
> 
> ```text
> 10.20.0.252/24
> ```
> 
> instead of:
> 
> ```text
> 10.20.0.252/25
> ```

---

# 🧠 What The Pentester THOUGHT

---

> [!NOTE]
> 
> Their machine believed:
> 
> ```text
> All 10.20.0.X hosts are local
> ```
> 
> So it attempted DIRECT communication.

---

# 🌍 Reality

---

> [!IMPORTANT]
> 
> The Domain Controller existed on a DIFFERENT subnet.
> 
> Communication should have gone through the gateway/router.
> 
> Because the subnet mask was wrong:
> 
> - ARP requests failed
>     
> - routing failed
>     
> - DC appeared "offline"
>     

---

# 💥 Result

---

> [!DANGER]
> 
> The pentester:
> 
> ✅ Compromised workstations
> 
> ❌ Missed:
> 
> - Domain Controllers
>     
> - Database servers
>     
> - High-value targets
>     
> 
> All because of ONE incorrect subnet mask.

---

# 📬 Networking Explained Like Mail Delivery

---

> [!INFO]
> 
> Networking is similar to mail delivery.

---

## 🏠 Home Network

Your:

- laptop
    
- phone
    
- desktop
    

connect to your:

- router
    

---

## 🏢 Company Network

Company devices connect to:

- switches
    
- routers
    
- servers
    

---

# 🌐 Internet Flow

---

## Visiting a Website

Example:

```text
https://www.hackthebox.com
```

Your computer:

1. asks DNS for the IP
    
2. gets the server address
    
3. sends packets to that IP
    
4. receives website data back
    

---

# 🔤 FQDN vs URL

---

## FQDN

```text
www.hackthebox.com
```

Specifies ONLY:

- the building address
    

---

## URL

```text
https://www.hackthebox.com/login?id=5
```

Specifies:

- protocol
    
- path
    
- parameters
    
- exact resource
    

---

# 📦 Router Analogy

---

> [!TIP]
> 
> Your router is like your local post office.
> 
> It forwards traffic to:
> 
> - ISP
>     
> - internet
>     
> - other networks
>     

---

# 🧱 Recommended Secure Network Design

---

# 🌐 DMZ (Demilitarized Zone)

---

> [!IMPORTANT]
> 
> Public-facing servers should exist in a DMZ.
> 
> Example:
> 
> - web servers
>     
> - mail servers
>     
> 
> This isolates them from internal networks.

---

# 💻 Workstation Network

---

> [!IMPORTANT]
> 
> Workstations should:
> 
> - exist on separate VLANs
>     
> - avoid workstation-to-workstation communication
>     
> 
> Prevents:
> 
> - lateral movement
>     
> - spoofing
>     
> - MITM attacks
>     

---

# ⚙️ Administration Network

---

> [!IMPORTANT]
> 
> Routers/switches should exist on management-only networks.
> 
> Prevents attackers from:
> 
> - sniffing routing protocols
>     
> - attacking infrastructure devices
>     

---

# ☎️ Voice Network

---

> [!IMPORTANT]
> 
> IP Phones should exist on separate VLANs.
> 
> Benefits:
> 
> - prevents eavesdropping
>     
> - prioritizes voice traffic
>     
> - reduces latency
>     

---

# 🖨️ Printer Network

---

> [!DANGER]
> 
> Printers are EXTREMELY dangerous.
> 
> Reasons:
> 
> - difficult to secure
>     
> - store sensitive documents
>     
> - frequently support SMB/NTLM
>     
> - useful for persistence
>     
> 
> NTLM authentication theft is common.

---

# 🕵️ Fun Story — Malicious Printer Attack

---

> [!EXAMPLE]
> 
> During a physical pentest:
> 
> 1. A tester modified a printer
>     
> 2. Embedded a reverse shell
>     
> 3. Shipped it to the target company
>     
> 4. Employees connected it
>     
> 5. Printer captured Domain Admin credentials
>     
> 
> This worked because:
> 
> - printer could access internet
>     
> - workstations could communicate with printer
>     
> - printer could initiate SMB connections
>     

---

# 🔥 Core Networking Lessons

---

> [!SUCCESS]
> 
> Good networking security:
> 
> - slows attackers
>     
> - limits pivoting
>     
> - improves detection
>     
> - reduces blast radius
>     
> 
> Bad networking:
> 
> - creates flat environments
>     
> - enables lateral movement
>     
> - hides attacker activity
>     
> - turns one compromise into full compromise
>     

---

# 🧠 Pentester Mental Models

---

> [!TIP]
> 
> Always verify:
> 
> - subnet masks
>     
> - routes
>     
> - gateways
>     
> - reachable networks
>     
> - interface bindings
>     
> 
> NEVER assume:
> 
> ```text
> /24
> ```
> 
> without checking.