# Service Enumeration and Detection

Service enumeration is the process of identifying exactly which applications and versions are running on open ports. This is critical for vulnerability research, as it allows you to look up specific CVEs (Common Vulnerabilities and Exposures) and exploits tailored to the target’s software and OS.

---

## 🔍 Service Version Detection (`-sV`)

Nmap determines service information by interacting with the open port. It primarily uses **Banner Grabbing**—reading the initial identification string sent by the service—and falls back on a **Signature-based matching system** if no banner is present.

### Efficiency Strategies

- **Quick Overview First:** Perform a standard port scan to find open ports before running `-sV`. Running version detection on all 65,535 ports (`-p-`) is extremely slow and generates heavy traffic.
    
- **Background Scanning:** You can start a full `-p-` scan in the background while you manually investigate the results of a quicker top-ports scan.
    

### Monitoring Progress

Full scans take time. Nmap provides two ways to stay updated:

1. **Manual:** Press the **[Space Bar]** to see a status line showing % completion and Estimated Time of Completion (ETC).
    
2. **Automatic:** Use `--stats-every=5s` (or minutes `m`) to get periodic updates.
    

---

## 🚩 Banner Grabbing & The PSH Flag

A "Banner" is a message sent by a service after a connection is established. At the network level, this usually involves a TCP packet with the **PSH (Push)** and **ACK** flags.

### Why Nmap Might Miss Details

Nmap automates the process, but it sometimes filters or simplifies the banner for the final report. Manual verification is often necessary.

**Example Comparison (SMTP Port 25):**

- **Nmap Output:** `Postfix smtpd`
    
- **Manual Connection (`nc`):** `220 inlane ESMTP Postfix (Ubuntu)`
    

The manual connection revealed the OS (**Ubuntu**), which Nmap did not explicitly highlight in the service column.

---

## 💻 Command Reference (Copy & Paste)

### Version Scanning & Monitoring

Bash

```
# Basic version detection on all ports with verbosity
sudo nmap <IP> -p- -sV -v

# Version detection with status updates every 10 seconds
sudo nmap <IP> -sV --stats-every=10s

# Comprehensive version scan with packet tracing (for debugging)
sudo nmap <IP> -p 25 -sV --packet-trace
```

### Manual Banner Grabbing

If Nmap's `-sV` output seems incomplete, use Netcat (`nc`) or Telnet to see the raw banner:

Bash

```
# Connect to a specific port manually
nc -nv <IP> <port>
```

### Traffic Interception (Verification)

To see the PSH/ACK flags and the raw data exchange yourself:

Bash

```
# Listen for traffic between you and the target
sudo tcpdump -i eth0 host <your_IP> and host <target_IP>
```

---

**Tags:** #nmap #service-enumeration #banner-grabbing #nc #tcpdump #pentesting-notes

> [!TIP] **Verbosity (`-v` or `-vv`)** is your friend. It tells Nmap to report open ports the moment it finds them, rather than waiting for the entire scan to finish.

