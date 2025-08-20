Absolutely! Here's the same **comprehensive `tcpdump` cheat sheet** organized in clean, structured tables for **easy readability** and **quick reference**.

---

## 🧠 **tcpdump Cheat Sheet — Structured Table Format**

---

### 🔹 Interface & Capture Control

|**Command**|**Description**|
|---|---|
|`tcpdump -i eth0`|Capture on interface `eth0`. Use `tcpdump -D` to list available interfaces.|
|`tcpdump -i any`|Capture on all interfaces (Linux only).|
|`tcpdump -c 100`|Stop after capturing 100 packets.|
|`tcpdump -s 0`|Capture full packet size (no truncation).|
|`tcpdump -G 60 -w '%Y-%m-%d_%H:%M:%S.pcap'`|Rotate captures every 60 seconds with timestamped filenames.|
|`tcpdump -W 5`|Keep only 5 rotated capture files.|
|`tcpdump -K`|Ignore checksum validation (useful with hardware offloading).|
|`tcpdump -U`|Unbuffered output for real-time monitoring.|

---

### 🔹 Output & Display Options

|**Command**|**Description**|
|---|---|
|`tcpdump -v` / `-vv` / `-vvv`|Increase verbosity: packet flags, TTL, options, etc.|
|`tcpdump -n`|Do not resolve hostnames (use raw IPs).|
|`tcpdump -nn`|Do not resolve hostnames or port numbers.|
|`tcpdump -tttt`|Show full, readable timestamps.|
|`tcpdump -X`|Print packet data in hex + ASCII.|
|`tcpdump -A`|Print ASCII packet payload only (useful for HTTP data).|
|`tcpdump -l`|Line-buffered output (better with `grep`, `less`).|

---

### 🔹 File Operations

| **Command**                | **Description**                            |
| -------------------------- | ------------------------------------------ |
| `tcpdump -w capture.pcap`  | Write packets to `.pcap` file.             |
| `tcpdump -r capture.pcap`  | Read from saved `.pcap` file.              |
| `tcpdump -r file.pcap -X`  | Read and show contents in hex + ASCII.     |
| `tcpdump -r file.pcap -nn` | Read without resolving hostnames or ports. |
| tcpdump -q file.pcap       | Quick and quite: brief packet information  |
| tcpdump -e                 | Include MAC addresses                      |
| tcpdump -A                 | Print packets as ASCII encoding            |
|                            |                                            |

---

### 🔹 Common Filter Types (BPF Syntax)

#### 📍 IP & Network Filters

|**Filter**|**Description**|
|---|---|
|`host 192.168.1.1`|Match traffic to/from a host.|
|`src 10.0.0.1`|Match packets from a source IP.|
|`dst 172.16.0.1`|Match packets to a destination IP.|
|`net 192.168.0.0/16`|Match packets within a subnet.|

#### 🌐 Port & Protocol Filters

|**Filter**|**Description**|
|---|---|
|`port 80`|Match traffic to/from port 80.|
|`src port 22`|Match traffic from port 22.|
|`dst port 443`|Match traffic to port 443.|
|`tcp`, `udp`, `icmp`|Match only a specific protocol.|
|`tcp port 443`|Combine protocol and port filter.|

#### 🔐 TCP Flags & Field Filters

|**Command**|**Description**|
|---|---|
|`tcp[13] & 2 != 0`|Match TCP SYN packets (start of handshake).|
|`tcp[13] & 18 == 18`|Match SYN + ACK packets.|
|`icmp[0] == 8`|Match ICMP echo request (ping).|

#### 🔗 Combining Filters

|**Example**|**Description**|
|---|---|
|`src 10.0.0.1 and dst port 22`|Traffic from a host to SSH port.|
|`tcp and port 443 and not src net 192.168.0.0/16`|HTTPS traffic excluding LAN sources.|

---

### 🔹 Real-World Examples

|**Task**|**Command**|
|---|---|
|Capture HTTP traffic|`tcpdump -i eth0 tcp port 80`|
|Capture HTTPS with ASCII view|`tcpdump -i eth0 -A port 443`|
|Monitor DNS queries|`tcpdump -i eth0 port 53`|
|Trace all traffic for a host|`tcpdump host 10.1.1.1`|
|View SSH outgoing traffic|`tcpdump dst port 22`|
|View data payloads|`tcpdump -s 0 -X port 80`|
|Live search for keywords|`tcpdump -l -A|

---

### 🔹 Bonus Tips

|**Tip**|**Description**|
|---|---|
|`tcpdump -D`|List all interfaces on the system.|
|Use `sudo`|Needed for most interfaces due to permission.|
|Combine with `grep`, `less`|Great for filtering and browsing live output.|
|Use in scripts|Combine with `cron`, `systemd`, or custom shell logic for automation.|

---

Would you like me to export this as a **printable PDF**, **Markdown file**, or **HTML web page** version?