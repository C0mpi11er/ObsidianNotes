> [!IMPORTANT] **Goal:** Identify live systems on a network before committing to intensive port scans. This saves time and avoids alerting security systems unnecessarily.

---

## 🔍 Discovery Techniques

### 1. Network Range Scanning

To scan an entire subnet (e.g., a Class C network), use CIDR notation. Disabling port scanning allows for a quick "ping sweep."

- **Command:** `sudo nmap <IP/Mask> -sn -oA <filename>`
    
- **Key Flag:** `-sn` (No port scan).
    

### 2. Targeted Discovery

Nmap allows for flexible target definitions depending on the scope provided by the client:

- **IP List (`-iL`):** Reads targets from a text file (e.g., `hosts.lst`).
    
- **Multiple IPs:** Space-separated addresses (e.g., `10.129.2.18 10.129.2.19`).
    
- **Range Notation:** Using hyphens for specific octets (e.g., `10.129.2.18-20`).
    

---

## 🛰️ Under the Hood: Protocols used for Discovery

Nmap intelligently chooses discovery protocols based on the network context.

|Method|Flag|Description|
|---|---|---|
|**ARP Ping**|(Default on LAN)|Most reliable for local networks; uses MAC addresses.|
|**ICMP Echo**|`-PE`|The standard "ping" request. Often blocked by external firewalls.|
|**Disable ARP**|`--disable-arp-ping`|Forces Nmap to use ICMP/TCP instead of ARP on local segments.|

Export to Sheets

### Diagnostic Flags

To understand _why_ Nmap thinks a host is up, use these debugging tools:

- `--packet-trace`: Displays every packet sent and received in real-time.
    
- `--reason`: Adds a column to the output explaining why a host was marked "Up" (e.g., `received arp-response`).
    

---

## 💾 Best Practices: Output Management

Always use the `-oA` (Output All) flag. This generates three files:

1. **.nmap:** Human-readable text.
    
2. **.gnmap:** Grepable format (ideal for filtering live IPs via command line).
    
3. **.xml:** For importing into other tools or generating reports.
    

> [!TIP] If a host appears "down," it may simply be ignoring ICMP packets due to a firewall. Consider testing different discovery methods in the "Firewall Evasion" phase.