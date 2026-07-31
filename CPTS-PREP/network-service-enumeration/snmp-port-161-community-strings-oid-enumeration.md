# 🛰️ SNMP - Port 161, community strings, OID enumeration

---

> [!ABSTRACT] Overview of SNMP Enumeration
>
> Simple Network Management Protocol (SNMP) is a protocol used to manage and monitor network devices by collecting and organizing information about managed devices. This note covers the process of enumerating SNMP on port 161 using community strings and Object Identifiers (OIDs).

---

## 📚 General Information

> [!INFO] What is SNMP?
>
> SNMP stands for Simple Network Management Protocol, a standard protocol for network management used to monitor and manage network devices. It collects information from, and configures the managed devices.

### Common Uses
- Monitoring device metrics (CPU load, memory usage)
- Configuration of network devices
- Collecting data about security events

---

## 🔍 SNMP Enumeration Workflow

```text
161 Open?
   ↓
Discover Community Strings
   ↓
Enumerate OIDs
```

---

# 🎯 Discovering Community Strings

### snmp-check

Anonymous:

```bash
snmp-check -c public <IP>
```

Authenticated:

```bash
snmp-check -c private -u user <IP>
```

### SNMP Enumeration Tools
Use tools like [[snmpwalk]], [[nmap]], and [[OpenVAS]] for detailed enumeration.

---

# 📂 Enumerating OIDs

### snmpwalk

Anonymous:

```bash
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1
```

Authenticated:

```bash
snmpwalk -v2c -c private -u user <IP> 1.3.6.1.2.1
```

### Common OIDs

| OID | Description |
|---|---|
| `1.3.6.1.2.1.1` | System Information (sysDescr, sysObjectID) |
| `1.3.6.1.2.1.25` | Interfaces and Interface Types |
| `1.3.6.1.2.1.47` | Traffic Statistics |

---

# 📌 High-Value OIDs to Hunt

```text
1.3.6.1.2.1.1.1.0 (sysDescr)
1.3.6.1.2.1.1.5.0 (sysName)
1.3.6.1.2.1.47.1.1.1.0 (Traffic Statistics)
```

---

# ⚠️ Common Findings

| Finding | Impact |
|---|---|
| Weak Community Strings | Eavesdropping, Unauthorized Access |
| Default OIDs | Information Disclosure |

---

# 🧠 Exam Mental Model

```text
SNMP Port 161 Open → Check Community String → Enumerate OIDs → Analyze Data
```

> [!SUCCESS] SNMP Rule of Thumb
>
> **Whenever you see port 161 open, immediately think:**
>
> ```text
> Check community strings → Enumerate common OIDs → Identify critical information
> ```
>
> Properly understanding the OID structure and knowing how to use tools like `snmpwalk` is crucial for successful SNMP enumeration.

---