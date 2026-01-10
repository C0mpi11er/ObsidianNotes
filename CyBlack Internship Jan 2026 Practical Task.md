---

---
---

| **Field** | **Detail**            |
| --------- | --------------------- |
| **Name**  | Matthew Ogbu          |
| email     | c0mpi11er47@gmail.com |

---
Part 1: Investigation
---
**Question:** Investigate the hash. Which type of hash is it?
==============

**Procedure:**
1. I began by downloading the practical file inside a **sandboxed environment** within a **QEMU virtual machine** running Linux. This reflects good cybersecurity practice, as one should never download unknown files without proper isolation.

2. I then investigated the hash type using the tool **`hash-identifier`** on my sandboxed Kali instance. It returned two potential results:![[cyblackhash.png]]
    
    
    - **SHA256**
        
    - **Haval-256**
        
3. To ensure precision and draw a clear conclusion, I cross-referenced the hash using the online service `https://hashes.com/en/tools/hash_identifier`.![[hashes.com.png]]
    

**Conclusion:** The findings were consistent. The hash type was conclusively identified as **SHA256**.

---



**Question:** Is this file malicious? Why? What's the malware family?
==========
**Procedure:** I approached this strategically, initially uncertain whether the question referred to the file itself or the hash provided as an Indicator of Compromise (IoC).

1. I uploaded the file to **VirusTotal** (a standard cybersecurity procedure), which yielded a positive result.
    
2. Further attempts to scan the file using **`clamscan`** were unsuccessful.
    
3. I then calculated the file's hash using the Bash command:
    
    echo file | sha256


4. The resulting file hash was: `7b35a25dff06b7e95953fe4b8a7b7ab2c773b8085583172a88376259e5fb516d`

    it was different from the provided hash so i concluded
    

**Conclusion:** Upon reviewing the overall context, I concluded that the question referred to the hash provided from question 1.

- **File Maliciousness:** No, the specific file I tested was not malicious.
    
- **Hash Maliciousness:** Yes, the given **hash is malicious**.
    
- **Malware Family:** The malware family associated with this hash is **"Trojan.Bonb"**.

---

**Question:** How can this file be detected?
==============
.**Detection Method:** The file's malicious nature can be easily detected using **online web services** that provide malware detection based on **hash lookups**, such as **VirusTotal**.![[virustotal.png]]

**Question:** What does this malware do to its target?
=============
**Malware Functionality:** This is a **staged malware** or **loader**. The initial part of staged malware is often harmless but downloads the remaining, more harmful components to complete the infection. When executed, its functions include:

- **Process Termination:** It kills processes with hardcoded names (e.g., `/tmp/condi`, `/var/condibot`, `/bin/busybox`) to eliminate competition from other malware.
    
- **Stage Downloading:** It uses commands like **`wget`** or **`curl`** to fetch and download the full payload from a Command and Control (C2) server.
    
- **Network Scanning:** It scans networks to identify other potential victims within the same connection or network (e.g., **Active Directory enumeration**).

**Question:** What type of attack can this malware be used to execute? And how can it be mitigated?
======================
**Attack Execution:** This type of malware is commonly used to conduct the following attacks:

- **DDoS Attack (Distributed Denial of Service):** The infected machine becomes a bot used to overwhelm a target server.
    
- **Backdoor Creation:** It creates a covert access point for adversaries to maintain persistence on the network.
    
- **Staged Exploitation:** It establishes the initial foothold needed to download and execute further malicious agents.


**Mitigation:** Effective mitigation requires a multi-step approach:
1. **Isolation:** **Disconnect** the affected devices from the network immediately to prevent further infection spread.
    
2. **Antivirus/EDR:** Run updated antivirus solutions (e.g., Norton, Avast) to scan and remove the malware.
    
3. **Credential Reset:** **Change all system passwords** that may have been compromised.
    
4. **Cleanup:** Wipe browser history and reset cookies to eliminate any potentially stolen session information.
    

---


**Question:** In the healthcare industry, what kind of impact can this type of attack have?
=====================
**Healthcare Impact:** Attacks based on this type of malware, which targets system availability and data integrity, can have severe impacts in the healthcare industry, including:

1. **Service Disruption:** **Ambulance diversion** due to critical systems being offline.
    
2. **Privacy Breach:** Poor patient data confidentiality, leading to regulatory penalties.
    
3. **Patient Safety:** **Delayed treatment** and a potential increase in **mortality rates** due to lack of access to patient data or control over critical care systems.


---
# 🗺️ Part 2: WAF Bypass & Domain Analysis
## Target Domain: fezzant.com
---

## **Analyse the DNS records. What do you find out about the domain?
--
My Approach (Passive OSINT):**
* **Security Posture:** Used a **double-layer VPN** to spoof/obscure client IP, ensuring safe, non-attributable reconnaissance.
* **Primary Tool:** **DNS Dumpster** for passive information gathering.![[dnsmap.png]] 
* **Observation:** The domain is protected by **Cloudflare![[waffw00f.png]] WAF**.

> Passive DNS analysis is key to bypassing active defenses.
---
# 2. DNS Analysis & Security Gaps
## **A. Finding Traffic Leakage (MX Records)**
--
| Record Type | Host | Observed IP | Finding |
| :--- | :--- | :--- | :--- |
| **MX (Mail)** | mx.zoho.com | 204.141.33.44 | Non-Cloudflare IP (Zoho Mail). |
| **TXT (SPF)** | fezzant.com | n/a | Confirms Zoho Mail service inclusion. |![[dnsrecord.png]]

## **B. Security Gap**

  after intense auditing using tools like burpsuite![[burp.png]]

sqlmap![[sql.png]]
favi ico hash generation to fingerprint ip
![[favico.png]]
* The **MX records** reveal traffic routes that **bypass the WAF**.
* This is a common **IP disclosure vector**: an attacker can use this non-proxied network range to search for the hidden web server IP, entirely bypassing Cloudflare's protection.
---
# 3. TLS Certificate Investigation
## **A. Extracting the Public Key**
* **Command:** `openssl s_client -connect Fezzant.com:443 | openssl x509 -pubkey -noout`
* **Connection Result:** The connection resolves to a known **Cloudflare IP** (`2a06:98c1:58::60`).

>The extracted key is the **Cloudflare Edge Certificate**

-----BEGIN PUBLIC KEY-----
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEgbi7BQYrydTfWIwSIgOkK1HSJ9RO
kSf8V5bd3xrQLcf0DcMdbkhjLXKd5HtdghubRJOaaxG8wzlEQPnPl2qS8Q==
-----END PUBLIC KEY-----


 Next Step for Attackers**
* The **Origin Server’s** unique certificate key is still hidden.
* The Cloudflare key hash would be used in **Certificate Fingerprint Attacks** on global scanners (Shodan/Censys) to find the non-proxied origin IP.
---

## **TLS 1.3: The Required Standard**
--
## **Benefits:**
* **Security:** Eliminates weak cryptographic features (e.g., reduces attack surface).
* **Performance:** Accelerates connection setup (1-RTT handshake).
* **Protocol:** Streamlines the handshake process for greater security.
---

## **Finding:** Unconfirmed Vulnerability in App Layer
--
* During active reconnaissance, a preliminary indicator of a vulnerability was found in the underlying web framework (e.g., Next.js v14+).![[cvevuln.png]]
* This specific vulnerability involves minor header manipulation that could expose an endpoint.
* **Action Taken:** Due to lack of time and explicit permission for deep exploitation, further testing was halted.
* **Recommendation:** A screenshot of the **Proof-of-Concept (PoC) request/response** was captured and is provided to management for internal, authorized review by development teams.![[githubpoc.png]]
* **Mitigation Need:** This requires immediate attention to apply all recommended security headers and framework patches.

---


