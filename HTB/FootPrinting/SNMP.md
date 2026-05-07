---

# 🛰️ SNMP (Simple Network Management Protocol) Cheat Sheet

---

> [!info] 🧠 What is SNMP?
>
> ```bash
> Protocol for monitoring and managing network-attached devices
> ```
>
> 💡 Allows administrators to:
>
> * Monitor device status (CPU, RAM, Interface traffic)
> * Handle remote configuration and settings changes
> * Receive automated alerts via **Traps**
>
> ✔ Common in:
>
> * Routers, Switches, Servers, and IoT devices

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Manager (Client) ↔ Agent (Server/Device)
> ```
>
> 💡 Key Ports:
>
> * **UDP 161** → SNMP queries (Get/Set)
> * **UDP 162** → SNMP Traps (Unsolicited device alerts)
>
> ✔ Think of it as **a universal health and control dashboard**

---

# 🌳 Structure: MIBs & OIDs

---

> [!info] 📚 MIB (Management Information Base)
> 
>
> ```bash
> Hierarchical database/text file describing queryable objects
> ```
>
> 💡 Function:
>
> * Format for storing device info across different manufacturers
> * Written in **ASN.1** ASCII text format
> * Explains *where* to find info, not the data itself

---

> [!tip] 📍 OID (Object Identifier)
>
> ```bash
> Numerical address representing a specific node in the MIB tree
> ```
>
> 💡 Logic:
>
> * `1.3.6.1.2.1.1.1.0` → System Description
> * Longer chains indicate more specific information
> * Nodes are concatenated by dot notation

---

# 🔐 Protocol Versions

---

| Version | Security Mechanism | Encryption | Notes |
| :--- | :--- | :--- | :--- |
| **SNMPv1** | Community String (Plaintext) | None | No authentication; insecure |
| **SNMPv2c** | Community String (Plaintext) | None | Community-based; standard today |
| **SNMPv3** | User/Password (USM) | **Pre-shared Key** | High security; complex config |

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Key Enumeration Tools
>
> ```bash
> # 1. Brute-force Community Strings
> onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt <target>
> 
> # 2. Walk the OID tree (v2c)
> snmpwalk -v2c -c <community> <target>
> 
> # 3. Targeted OID Brute-forcing
> braa <community>@<target>:.1.3.6.*
> ```
>
> 💡 Common strings: `public`, `private`, `manager`.

---

> [!info] 🔍 Information Revealed
>
> * OS/Kernel versions
> * System uptime & Contact info
> * Installed software/Python packages
> * Running processes and network paths

---

# ⚠️ Common Misconfigurations

---

> [!danger] 🔓 Read/Write Community Strings
>
> ```bash
> rwcommunity <string> <address>
> ```
>
> 💡 Impact:
>
> * Full access to the OID tree
> * Ability to modify device settings remotely

---

> [!warning] ⚙️ Unauthenticated Access
>
> ```bash
> rwuser noauth
> ```
>
> 💡 Risk:
>
> * Provides full OID tree access without any credentials.

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Attack Flow
>
> ```bash
> 1. Scan UDP 161 for SNMP service
> 2. Brute-force community strings (onesixtyone)
> 3. Enumerate system info (snmpwalk)
> 4. Identify sensitive software or local paths
> 5. Use info for further lateral movement
> ```
>
> 💡 SNMP often exposes internal details that TCP scans miss.

---

# 🧩 Mental Model

---

> [!quote] 🎯 Protocol Hierarchy
>
> ```bash
> MIB → The Dictionary
> OID → The Definition
> Community String → The Library Card
> ```
>
> 💡 You need the "Library Card" (String) to look up the "Definition" (OID) in the "Dictionary" (MIB).
```