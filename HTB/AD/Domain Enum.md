# Internal Active Directory: Initial Domain Enumeration

> [!info]
> 
> Initial internal domain enumeration is the process of mapping an Active Directory network from a completely unauthenticated standpoint to discover live hosts, core domain services, valid user accounts, and quick exploit vectors.

## 🧩 Core Idea

> [!important]
> 
> **The Blind Perspective Strategy**
> 
> Starting an engagement with zero credentials mimics real-world threat vectors (e.g., rogue employees, physical building breaches, rogue wireless connections, or post-phishing implants). Pentesters map four critical data classes:
> 
> - **AD Users:** Harvesting valid usernames to feed down-funnel password spraying attacks.
>     
> - **AD Computer Objects:** Locating Domain Controllers, Database servers (SQL), Mail structures (Exchange), and File Shares.
>     
> - **Core Services:** Tracking infrastructure anchor ports (Kerberos, LDAP, SMB, DNS).
>     
> - **Vulnerable Entry Points:** Spotting low-hanging fruit (legacy operating systems, unhardened protocols) to establish a baseline foothold.
>     

# ⚙️ Attack Flow (The Unauthenticated Pipeline)

> [!note]
> 
> Internal enumeration relies on a rigorous multi-stage pipeline. Moving directly to heavy scanning triggers defensive traps; attackers must move from passive monitoring to structured active discovery.

### 1. Passive Listening (Layer 2 Broadcasts)

- Sniff the broadcast domain to capture native routing traffic without emitting packets.
    
- Extract immediate IP addresses, asset names, and active network configurations.
    

### 2. Active Verification (Probing)

- Execute low-noise sweeps to validate responsive hosts inside the designated network range.
    
- Perform targeted service fingerprinting against discovered assets.
    

### 3. Account Harvesting

- Query network authentication systems using silent validation protocols to map active directory accounts.
    

# 🗺️ Unauthenticated Initial Enumeration Workflow

> [!tip]
> 
> Use this step-by-step methodology to map the target environment network layer (**172.16.5.0/23**) cleanly:

Plaintext

```
 [1] Passive Sniffing ──> [2] Active ICMP Sweep ──> [3] Targeted Port Scan
 (Wireshark/Responder)         (fping /asgq)             (Nmap AD Profiles)
                                                                 │
 [6] Internal Domain Pivot ◄── [5] Kerberos Harvesting ◄─────────┘
  (NT AUTHORITY\SYSTEM)          (Kerbrute userenum)
```

### Phase 1: Passive Network Sniffing ("Ear to the Wire")

- Run packet analyzers to extract hosts via Address Resolution Protocol (ARP) broadcasts and Multicast DNS (MDNS) traffic without emitting packets.
    

Bash

```
# GUI environments
sudo -E wireshark

# Command-line / Headless environments
sudo tcpdump -i ens224
```

- Run Responder strictly in **Analyze Mode** to passively map Local Link Multicast Name Resolution (LLMNR) and NetBIOS Name Service (NBT-NS) requests without poisoning traffic.
    

Bash

```
sudo responder -I ens224 -A
```

### Phase 2: Active Network Sweeping

- Launch round-robin ICMP requests across the network range (**172.16.5.0/23**) to parse live systems from dead addresses quickly.
    

Bash

```
fping -asgq 172.16.5.0/23
```

### Phase 3: Targeted Active Directory Fingerprinting

- Run detailed service scans exclusively against verified live host lists, targeting anchor ports.
    
- **Key Ports to Monitor:** `53/TCP` (DNS), `88/TCP` (Kerberos), `389/TCP / 636/TCP` (LDAP/S), `445/TCP` (SMB), `3389/TCP` (RDP).
    

Bash

```
sudo nmap -v -A -iL hosts.txt -oA /home/htb-student/Documents/host-enum
```

### Phase 4: Parsing Infrastructure and Legacy Flags

- Identify the Primary Domain Controller by looking for systems hosting port 88/389 alongside domain attributes in SSL certificates (e.g., `ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`).
    
- Spot outdated operating systems (e.g., IIS 7.5 or Windows Server 2008 R2 endpoints) to log immediate exploitation vulnerabilities like **EternalBlue (MS17-010)**.
    
- **Operational Safety Guardrail:** Exploiting legacy assets can destabilize services. Secure explicit **written authorization** from the client before executing live exploits on older infrastructure.
    

### Phase 5: Stealth Username Enumeration via Kerberos

- Leverage the Kerberos protocol design flaw where pre-authentication failures return distinct responses for valid versus invalid usernames without triggering standard failed login events.
    

Bash

```
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

## 🧱 The Power of Local SYSTEM Access

Securing **NT AUTHORITY\SYSTEM** privileges on any single domain-joined asset allows you to query active directory by impersonating the computer account, acting as a functional bridge to the rest of the domain.

### Operational Capabilities of SYSTEM Access:

- **AD Queries:** Execute tools like **BloodHound** or **PowerView** under the context of the computer account (`COMPUTER$`) to map out domain structures.
    
- **Targeted Extraction:** Launch **Kerberoasting** and **ASREPRoasting** routines across the network.
    
- **Traffic Poisoning & Relays:** Initialize tools like `Inveigh` to capture Net-NTLMv2 hashes or execute **SMB Relay** maneuvers.
    
- **Token Hijacking:** Extract and duplicate tokens from active sessions to take over privileged domain accounts.
    

# 🛠️ Assessment Delivery Engagement Styles

The method of enumeration must explicitly match the pre-negotiated rules of engagement documented with the client:

|**Testing Type**|**Evasion Posture**|**Operational Boundary**|
|---|---|---|
|**Non-Evasive (Standard Pentest)**|Out in the open; noise is ignored.|Fast, comprehensive coverage using multi-threaded network sweeps (`Nmap -A`).|
|**Evasive (Red Team / Adversarial)**|High stealth priority; mimics advanced threat TTPs.|Slow, methodical interaction. Avoids large port sweeps; prioritizes passive listening and quiet Kerberos enumeration.|
|**Hybrid Evasive**|Escalate pacing incrementally.|Starts fully passive/silent to map security operations center (SOC) detection thresholds, then transitions to active probing.|

# 🧠 Key Takeaway

> [!summary]
> 
> Initial AD Domain Enumeration = **Target List Generation**
> 
> You begin with zero network visibility. By sniffing network broadcasts (Phase 1), validating live systems (Phase 2), locating the Domain Controller (Phase 4), and exploiting Kerberos pre-authentication behavior (Phase 5), you generate a verified directory of active users and exposed vectors. This sets the stage for immediate **Password Spraying** or **LLMNR/NBT-NS Poisoning** footholds.