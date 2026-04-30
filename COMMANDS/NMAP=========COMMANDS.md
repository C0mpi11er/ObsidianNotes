

---


### 🔍 1. The Stealthy "Ping Sweep" (Live Host Discovery)

Before scanning ports, map the active landscape of a `/24` without being noisy.

Bash

```
sudo nmap -sn -PE -PP -PM -n --stats-every 10s 10.10.10.0/24 -oN discovery_sweep.nmap
```

> [!NOTE] **Tactical Breakdown**
> 
> - `-sn`: Disable port scanning (Host discovery only).
>     
> - `-PE -PP -PM`: **The ICMP Trifecta.** Uses Echo, Timestamp, and Address Mask queries to bypass firewalls that only block standard Echo (ping) requests.
>     
> - `-n`: No DNS resolution (maximum speed/prevents logging on DNS servers).
>     
> - `--stats-every 10s`: Real-time progress updates for large subnets.
>     

---

### 🕵️ 2. The "Ghost" SYN Scan (IDS/IPS Evasion)

Use this when you suspect a firewall is watching for high-rate connection attempts.

Bash

```
sudo nmap -sS -Pn -T2 -D RND:10 -g 53 --max-retries 1 --initial-rtt-timeout 100ms 10.10.10.15
```

> [!WARNING] **Evasion Profile**
> 
> - `-sS`: Stealth SYN scan (half-open scan, never completes the TCP handshake).
>     
> - `-T2`: **Polite** timing. Slower, but less likely to trigger rate-limiting alerts.
>     
> - `-D RND:10`: Generates **10 random IP decoys** to hide your real IP in the noise.
>     
> - `-g 53`: Sets the **source port to 53** (DNS), often trusted by legacy firewall rules.
>     
> - `--max-retries 1`: Limits retransmissions to prevent "heavy" traffic signatures.
>     

---

### 🛡️ 3. The "Deep-Dive" Service Audit 

Once you find a target, you need to know exactly what is running and if it's vulnerable.

Bash
> [!NOTE] Loud!!!
```
sudo nmap -sV -sC -O -p- --version-intensity 8 --reason --script-updatedb 10.10.10.15 -oA deep_audit
```

>[!CHECK] use netcat for banner grabbing sometimes using 53 as source port {DNS} fire walls weak against it 
BASH
```
  nc -nv -p 53 <target-ip> 50000
```

> [!NOTE] silent....

Bash
```
sudo nmap -sS -sV --version-intensity 0 -p 50000 -T2 --source-port 53 --data-length 28 -D RND:10 --open 10.129.72.39 -oA service_audit
```



> [!TIP] **Analysis Profile**
> 
> - `-sV`: Probes open ports to determine service/version info.
>     
> - `--version-intensity 8`: Highly aggressive service probing (checks more signatures).
>     
> - `-sC`: Runs **Default NSE scripts** (safe but thorough).
>     
> - `--reason`: Displays the specific packet response that caused a port to be marked "open."
>     
> - `-p-`: Scans all **65,535 ports**—essential for finding non-standard backdoors.
>     

---

### ⚡ 4. The "Blitz" Scan (For Fast-Paced Labs/CTFs)

When accuracy is less important than raw speed in a stable network.

Bash

```
sudo nmap -T5 --min-rate 5000 --max-retries 0 --open -F 10.10.10.0/24
```

> [!DANGER] **Aggression Level: High**
> 
> - `-T5`: **Insane** timing. Best used on local/high-bandwidth lab networks.
>     
> - `--min-rate 5000`: Forces Nmap to maintain at least 5,000 packets per second.
>     
> - `--max-retries 0`: If a port doesn't answer instantly, ignore it and move on.
>     
> - `--open`: Only shows ports that responded, cutting down the "noise" in your terminal.
>     
> - `-F`: Fast mode (scans the **Top 100** most common ports).
>     

---

### 🌐 5. The Active Directory / Network Admin Special

Targets specific Windows/AD ports and uses the updated script engine logic.

Bash

```
sudo nmap -Pn -p 88,135,139,389,445,3389 --script=smb-os-discovery,smb-vuln* --script-args=unsafe=1 10.10.10.0/24
```

> [!ABSTRACT] **AD Discovery**
> 
> - `-p 88...3389`: Targets Kerberos, RPC, SMB, LDAP, and RDP.
>     
> - `--script=smb-os-discovery`: Grabs OS version and Domain Name via SMB.
>     
> - `--script=smb-vuln*`: Checks for critical vulnerabilities (like EternalBlue).
>     
> - `--script-args=unsafe=1`: Forces the execution of scripts that might crash older services (use with extreme caution).
>     

---

### 🧪 6. The "Under-the-Hood" Debugger

When Nmap gives weird results and you need to see the raw packet exchange.

Bash

```
nmap -p80 --packet-trace --reason --version-trace --disable-arp-ping 10.10.10.15
```

> [!IMPORTANT] **Developer View**
> 
> - `--packet-trace`: Displays every packet sent and received in the terminal.
>     
> - `--version-trace`: Shows detailed version scan activity (shows which probe "hit").
>     
> - `--disable-arp-ping`: Forces Nmap to use the requested discovery method rather than local ARP pings.
>     

---

### 📂 Quick Reference: Output Formats

|**Flag**|**Name**|**Use Case**|
|---|---|---|
|`-oN`|**Normal**|Direct reading/Human readable notes.|
|`-oG`|**Grepable**|Extract IPs or ports using `grep`, `awk`, or `cut`.|
|`-oX`|**XML**|Professional reporting or importing into **Metasploit**.|
|`-oS`|**s|<rIpt kIddi3**|
|`-oA`|**All**|Saves `.nmap`, `.gnmap`, and `.xml` simultaneously.|

---

### 💡 Pro-Tips for v7.98

> [!QUESTION] **Nmap says the host is down, but I know it's up?**
> 
> **Solution:** Use `-Pn`. Many modern firewalls drop ICMP packets.
> 
> Bash
> 
> ```
> sudo nmap -Pn -p 80,443 <target>
> ```

> [!EXAMPLE] **The scan is taking forever! How do I see what's happening?**
> 
> **Solution:** While running, press **Spacebar** for a status line, **v** to increase verbosity on the fly, or **V** to decrease it.

> [!ATTENTION] **I found an open port but -sV didn't give me a version?**
> 
> **Solution:** Use `--version-all`. This forces Nmap to try every single version probe in its database against that port.
> 
> Bash
> 
> ```
> sudo nmap -sV --version-all -p <port> <target>
> ```



> [!TIP] During Tunelling e.g Ligolo

```
#get life host faster and reduce stress for nmap
>fping -a -g 172.16.0.0/23 
# scan range of ip fping got
> nmap -sT -Pn -T4 --min-rate 1000 <ip1> <ip2> 

```



> [!Tip] Search Scripts
> nmap --script-help http*
> nmap --script-help smb*
> nmap --script-help rdp*

