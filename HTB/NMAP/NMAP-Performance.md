
---

## 🕒 Timing Templates (`-T <0-5>`)

Nmap provides six templates that adjust various internal parameters (timeouts, parallelism, etc.) automatically.

|Template|Name|Use Case|
|---|---|---|
|`-T 0`|`paranoid`|Evasion: extremely slow to avoid IDS/IPS.|
|`-T 1`|`sneaky`|Evasion: slightly faster than T0.|
|`-T 2`|`polite`|Resource Saving: avoids crashing fragile services.|
|`-T 3`|**`normal`**|**The Default setting.**|
|`-T 4`|`aggressive`|**Recommended:** Speed boost for modern networks.|
|`-T 5`|`insane`|Maximum Speed: high risk of packet loss or being blocked.|

Export to Sheets

### 💻 Example: Timing Impact

Bash

```
# Default Scan (~32.44 seconds)
sudo nmap 10.129.2.0/24 -F -oN tnet.default

# Insane Scan (~18.07 seconds)
sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5
```

---

## ⏱️ RTT (Round-Trip-Time) Timeouts

RTT defines how long Nmap waits for a response before giving up on a packet.

- `--initial-rtt-timeout`: The starting wait time (default is usually 100ms).
    
- `--max-rtt-timeout`: The maximum wait time.
    

> [!DANGER] Accuracy Warning Setting these too low can result in "ghost" results where hosts appear down because they didn't respond fast enough.

### 💻 Example: Optimized RTT

Bash

```
# Standard Scan: 39s (Found 10 hosts)
# Optimized Scan: 12s (Found 8 hosts)
sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms
```

---

## 🔄 Retries (`--max-retries`)

Nmap defaults to **10 retries** for unresponsive ports. Reducing this can save immense time on filtered ports.

> [!ABSTRACT] Lab Result Reducing retries to `0` dropped the scan time but resulted in finding **21** open ports instead of the actual **23**.

### 💻 Example: Speed over Accuracy

Bash

```
# Skip a port if it doesn't respond the first time
sudo nmap 10.129.2.0/24 -F --max-retries 0
```

---

## 📈 Packet Rates (`--min-rate`)

In **White-Box** testing where you are whitelisted, you can force Nmap to blast packets at a specific speed regardless of network conditions.

|Option|Function|
|---|---|
|`--min-rate <number>`|Sends at least X packets per second.|
|`-oN <file>`|Saves results to a file for comparison.|

Export to Sheets

### 💻 Example: High-Speed Rate Control

Bash

```
# Forces Nmap to maintain 300 packets per second
sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300
```

- **Result:** Reduced scan time from **29.83s** to **8.67s** without losing any open ports in a stable environment.
    

---

## 🛠️ Summary of Flags

> [!SUCCESS] Pro-Tip Use `-F` (Top 100 ports) in combination with `--min-rate` and `-T4` for the fastest possible initial reconnaissance of a large network.

- `--min-parallelism`: Adjusts the number of parallel scan groups.
    
- `--max-parallelism`: Prevents Nmap from overwhelming a network.
    
- `--host-timeout`: Gives up on a slow host after a specific time.
    

---

**References:**

- [Nmap Timing Templates Documentation](https://nmap.org/book/performance-timing-templates.html)
    
- [Nmap Performance Man Page](https://nmap.org/book/man-performance.html)
    

How does this more detailed version look for your Obsidian vault