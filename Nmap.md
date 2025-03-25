Below is an ultra-comprehensive Nmap guide that covers nearly every trick and technique—from basic scanning to the most advanced tactics.

---

# **Nmap Comprehensive Guide**

## 🔥 Overview

**Nmap** ("Network Mapper") is an open-source tool for network exploration, security auditing, and vulnerability detection. It’s renowned for its versatility and can be used for:

- **Host Discovery**
- **Port Scanning**
- **Service & Version Detection**
- **Operating System Fingerprinting**
- **Nmap Scripting Engine (NSE) Exploitation**
- **Stealth & Decoy Scanning**
- **Advanced Output and Reporting**

Because Nmap is highly modular, there are countless options and techniques available to tailor your scans. Below is a detailed guide covering many of these techniques.

---

## 🛠️ Basic Scanning

### 📌 Simple Ping Sweep (Host Discovery)

```bash
nmap -sn 192.168.1.0/24
```

- **`-sn`**: Disable port scan; only discover live hosts.

### 📌 Default Scan

```bash
nmap 192.168.1.10
```

- Scans the most common 1,000 TCP ports and reports open ports, services, and basic version info.

---

## 🔎 Port Scanning Techniques

### 📌 Full TCP Port Scan (All Ports)

```bash
nmap -p- 192.168.1.10
```

- **`-p-`**: Scans all 65,535 ports.

### 📌 SYN Scan (Stealth / Half-Open Scan)

```bash
nmap -sS 192.168.1.10
```

- **`-sS`**: Sends a SYN packet and waits for a response. Requires root privileges.
- Stealthy because the TCP connection is never fully established.

### 📌 TCP Connect Scan

```bash
nmap -sT 192.168.1.10
```

- **`-sT`**: Uses the operating system’s connect() call. Does not require root but is less stealthy.

### 📌 UDP Scan

```bash
nmap -sU 192.168.1.10
```

- **`-sU`**: Scans UDP ports; note that UDP scans are slower because UDP is connectionless.

### 📌 Idle Scan (Zombie Scan)

```bash
nmap -sI <zombie_host> 192.168.1.10
```

- **`-sI`**: Uses a “zombie” host to perform a scan stealthily. The zombie must have a predictable IP ID sequence.
- One of the stealthiest techniques because it hides the real source of the scan.

### 📌 IP Protocol Scan

```bash
nmap -sO 192.168.1.10
```

- **`-sO`**: Scans for supported IP protocols (ICMP, IGMP, TCP, UDP, etc.) beyond just TCP/UDP ports.

---

## ⚙️ Service & Version Detection

### 📌 Service Version Detection

```bash
nmap -sV 192.168.1.10
```

- **`-sV`**: Probes open ports to determine the service (and version) running on each port.

### 📌 Aggressive Scan (Combined Options)

```bash
nmap -A 192.168.1.10
```

- **`-A`**: Enables OS detection, version detection, script scanning, and traceroute.
- Provides a comprehensive view but is noisy and may be detected by IDS.

### 📌 OS Detection

```bash
nmap -O 192.168.1.10
```

- **`-O`**: Attempts to determine the target's operating system based on TCP/IP stack behavior.

---

## 📝 Output Options

### 📌 Normal Output

```bash
nmap -oN output.txt 192.168.1.10
```

- **`-oN`**: Saves scan results in a human-readable format.

### 📌 XML Output

```bash
nmap -oX output.xml 192.168.1.10
```

- **`-oX`**: Saves output in XML format for further processing.

### 📌 Grepable Output

```bash
nmap -oG output.txt 192.168.1.10
```

- **`-oG`**: Creates output that’s easily parsed with grep.

### 📌 All Formats Simultaneously

```bash
nmap -oA myscan 192.168.1.10
```

- **`-oA`**: Saves the output in normal, XML, and grepable formats using the base name "myscan".

---

## 🐍 Nmap Scripting Engine (NSE)

### 📌 Running Specific Scripts

```bash
nmap --script http-title 192.168.1.10
```

- Runs the **http-title** script to retrieve web page titles from HTTP services.

### 📌 Running a Category of Scripts

```bash
nmap --script vuln 192.168.1.10
```

- **`vuln`**: Runs vulnerability detection scripts to check for known security issues.

### 📌 Custom Script Execution

```bash
nmap --script /path/to/your_script.nse 192.168.1.10
```

- Allows you to run a custom NSE script.

### 📌 Combining Scripts and Options

```bash
nmap -sV --script "default or safe" 192.168.1.10
```

- You can combine service detection with a selection of scripts to get both version info and additional vulnerability insights.

---

## ⏱️ Timing and Performance

### 📌 Adjusting Timing Templates

```bash
nmap -T4 192.168.1.10
```

- **`-T4`**: Aggressive timing; use higher values (T4 or T5) for faster scans, but risk detection.
- **`-T0`** (paranoid) to **`-T5`** (insane) for various environments.

### 📌 Parallelism and Performance

- Nmap automatically adjusts parallelism based on target response times, but you can tweak options like **`--min-rate`** or **`--max-retries`** for fine-tuning.

---

## 🎭 Stealth and Evasion Techniques

### 📌 Decoy Scans

```bash
nmap -D RND:10 192.168.1.10
```

- **`-D RND:10`**: Uses 10 random decoys to mask the true source of the scan.

### 📌 Spoofing Source IP

```bash
nmap -S 192.168.1.100 192.168.1.10
```

- **`-S`**: Spoofs the source IP address (requires raw socket privileges).

### 📌 No-Ping Scan

```bash
nmap -Pn 192.168.1.10
```

- **`-Pn`**: Disables host discovery. Useful when ping is blocked by firewalls.

---

## 🚀 Advanced Techniques

### 📌 Idle Scan (Zombie Scan)

- Uses a “zombie” host to conduct scans without revealing your own IP. See **`-sI`** above.

### 📌 IP Protocol Scanning

- Detects which IP protocols are supported by a host (besides TCP/UDP) using **`-sO`**.

### 📌 NSE for Vulnerability Exploitation

- Explore specialized scripts for exploits, brute forcing, and vulnerability detection. For instance:
    
    ```bash
    nmap --script smb-vuln-ms17-010 192.168.1.10
    ```
    
    - Checks for the EternalBlue vulnerability in SMB services.

### 📌 Using Nmap with a Proxy

```bash
nmap --proxies http://127.0.0.1:8080 192.168.1.10
```

- Routes scan traffic through an HTTP proxy, which can help bypass network filters.

### 📌 Output Customization for Automated Analysis

- Combining multiple output formats and using **grep** to extract key information from scans can be integrated into automated workflows.

---

## 📌 Tips & Best Practices

- **Run as Root/Administrator:** Many advanced scans (like SYN scans) require elevated privileges.
- **Combine Scanning Techniques:** Use multiple options together (e.g., `-sS -sV -O`) for more comprehensive results.
- **Test in a Lab Environment:** Practice and tune your options on controlled networks to understand how timing and decoy techniques affect detection.
- **Keep Updated:** Nmap is continuously updated; refer to the [Nmap Book](https://nmap.org/book/) and [NSE documentation](https://nmap.org/nsedoc/) for the latest features.
- **Legal Considerations:** Always have permission before scanning networks.

---

## ⚠️ Legal Disclaimer

💀 **Only scan networks you have explicit permission to test. Unauthorized scanning is illegal.**

🚀 **Happy Scanning!**

---

This guide should provide you with nearly every trick and technique available in Nmap—from basic host discovery and port scanning to advanced scripting, timing adjustments, and stealth scanning methods. Let me know if you need further details on any particular section!

---

_Sources:_

- Nmap Official Documentation: [https://nmap.org/book/](https://nmap.org/book/)
- Nmap Scripting Engine: [https://nmap.org/nsedoc/](https://nmap.org/nsedoc/)