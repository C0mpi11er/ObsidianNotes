

---

# 🪟 Active Directory Core Protocols

Active Directory (AD) relies on four key protocols for authentication, communication, and directory services:

- **Kerberos** – Authentication
    
- **DNS** – Service & DC discovery
    
- **LDAP** – Directory queries & auth
    
- **MSRPC** – Windows remote management
    

---

## 🔐 Kerberos (Port 88 TCP/UDP)

Default AD authentication protocol since Windows 2000.

**Key Concepts**

- Ticket-based, not password transmission
    
- Mutual authentication (client & server verify each other)
    
- KDC runs on Domain Controllers
    
- Stateless: relies on valid tickets
    

**Authentication Flow**

1. Client → KDC: encrypted timestamp (**AS-REQ**)
    
2. KDC → Client: Ticket Granting Ticket (**TGT**)
    
3. Client → DC: request service ticket (**TGS-REQ**)
    
4. DC → Client: TGS encrypted with service hash (**TGS-REP**)
    
5. Client → Service: presents ticket (**AP-REQ**)
    

**Use in Enumeration**

- DCs often expose **port 88**
    
- Discoverable via Nmap scans
    

---

## 🌐 DNS (Port 53 TCP/UDP)

Used by AD to locate Domain Controllers and services.

**Functions**

- Resolves hostnames ↔ IPs
    
- Stores **SRV records** for AD services
    
- Supports **Dynamic DNS** updates
    

**Common Lookups**

```powershell
nslookup DOMAIN.LOCAL        # Find DC IPs
nslookup 172.16.6.5          # Reverse lookup
nslookup DC01               # Host → IP
```

---

## 📁 LDAP / LDAPS (389 / 636)

Directory query protocol used by AD.

**Purpose**

- Query users, groups, computers
    
- Authenticate credentials
    
- Applications "talk" to AD via LDAP
    

**Authentication Types**

- **Simple Bind** – username/password
    
- **SASL** – uses Kerberos for stronger auth
    

⚠️ LDAP is plaintext by default → use **TLS/LDAPS**.

**Analogy**

> AD : LDAP :: Apache : HTTP

---

## 🧩 MSRPC (Windows RPC)

Used for domain management and internal AD operations.

### Important Interfaces

| Interface    | Purpose                             |
| ------------ | ----------------------------------- |
| **lsarpc**   | Security policy & authentication    |
| **netlogon** | Domain logon service                |
| **samr**     | User/group enumeration & management |
| **drsuapi**  | AD replication (NTDS.dit dumping)   |

**Security Notes**

- `samr` abused for recon (e.g., BloodHound)
    
- `drsuapi` abused for full domain credential dumping → Pass-the-Hash
    

---

## 📝 Quick Ports Reference

| Protocol | Port |
| -------- | ---- |
| Kerberos | 88   |
| DNS      | 53   |
| LDAP     | 389  |
| LDAPS    | 636  |

---
