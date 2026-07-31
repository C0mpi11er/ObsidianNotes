# 🛰️ SNMP (Simple Network Management Protocol) Reconnaissance & Exploitation Guide

## Introduction to SNMP Enumeration

SNMP is a protocol used for monitoring and managing network devices. Proper enumeration of an SNMP server can reveal critical information such as system details, installed services, user accounts, and more. This guide provides step-by-step instructions on how to enumerate an SNMP server using various tools and techniques.

## Prerequisites

Before starting the enumeration process, ensure you have access to the target system or network device that supports SNMP. Additionally, install necessary tools like `snmpwalk`, `nmap`, and `onesixtyone`.

### Essential Tools
```bash
sudo apt-get update && sudo apt-get install snmp snmp-mibs-downloader nmap onesixtyone
```

## Enumeration Process

### Initial Discovery

1. **Port Scan for SNMP**
   ```bash
   nmap -sU --top-ports 25 target_ip
   ```
   
2. **Service Version Detection**
   ```bash
   nmap -sV -p 161 target_ip
   ```

3. **SNMP Version Identification**
   ```bash
   snmpwalk -v2c -c public target_ip
   ```

4. **Community String Brute Forcing**
   ```bash
   onesixtyone target_ip
   ```

### Information Gathering

#### System and Network Details

1. **System Information**
   ```bash
   snmpwalk -v2c -c public target_ip 1.3.6.1.2.1.1.1.0
   ```
   
2. **Network Interfaces**
   ```bash
   snmpwalk -v2c -c public target_ip 1.3.6.1.2.1.2.2.1.2
   ```

#### User Accounts

3. **User Account Identification**
   ```bash
   snmpwalk -v2c -c public target_ip .1.3.6.1.4.1.77.1.2.25
   ```
   
#### Custom Scripts and Configurations

4. **Custom Script Paths**
   ```bash
   snmpwalk -v2c -c public target_ip 1.3.6.1.2.1.25.1.7.1.2
   ```

### Detailed Analysis

#### Information Extraction and Parsing

```bash
# Save full SNMP output to a file for detailed analysis
snmpwalk -v2c -c public target_ip > snmp_full.txt

# Extract email addresses
grep -i "@" snmp_full.txt

# Extract IP addresses
grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' snmp_full.txt

# Extract file paths
grep -oE '"/[^"]*"' snmp_full.txt

# Extract process information
grep -i "process\|service" snmp_full.txt
```

## Security Assessment

### Common Vulnerabilities and Weaknesses

- **Default Community Strings**: Using default strings like `public` or `private`.
- **Information Disclosure**: Excessive exposure of sensitive system details.
- **Weak Community Strings**: Guessable community strings.
- **SNMPv1/v2c Usage**: Unencrypted protocols are more susceptible to eavesdropping and tampering.

### Community String Testing

```bash
# Test common community strings
for community in public private community snmp read manager admin; do
    echo "Testing: $community"
    snmpwalk -v2c -c $community target_ip 1.3.6.1.2.1.1.1.0
done
```

### Detailed Security Checks

- **Write Access Testing**: Verify if unauthorized changes can be made to the SNMP configuration.
- **Information Disclosure Assessment**: Check for sensitive data exposure.
- **Weak Community String Identification**: Identify easily guessable community strings.
- **Encryption Status Verification**: Ensure that encryption is enabled for secure communication.

## Enumeration Checklist

### Initial Discovery
- [ ] Port scan for 161/UDP
- [ ] Service version detection
- [ ] SNMP version identification
- [ ] Community string brute force

### Information Gathering
- [ ] System information extraction
- [ ] Network interface enumeration
- [ ] Process and service discovery
- [ ] User account identification

### Detailed Analysis
- [ ] Custom script identification
- [ ] Configuration file discovery
- [ ] Credential extraction
- [ ] Network topology mapping

## Tools and Techniques

### Essential SNMP Tools
```bash
# Basic tools
snmpwalk             # SNMP tree walking
snmpget              # Specific OID queries
snmpset              # SNMP value setting (if write access)

# Enumeration tools
onesixtyone          # Community string brute forcing
braa                 # OID brute forcing and fast SNMP scanner
snmp-check           # Comprehensive SNMP enumeration
nmap                 # NSE script-based enumeration

# Analysis tools
snmptranslate        # OID translation
snmpnetstat          # Network statistics via SNMP
```

### Tool Installation and Usage

```bash
# Install SNMP tools
sudo apt install snmp snmp-mibs-downloader

# Install onesixtyone
sudo apt-get install onesixtyone

# Install braa
sudo apt-get install braa

# Download MIBs
sudo download-mibs
```

### Custom Scripts

#### Community String Tester
```bash
#!/bin/bash
target=$1
wordlist=$2

while read community; do
    result=$(snmpwalk -v2c -c $community $target 1.3.6.1.2.1.1.1.0 2>/dev/null)
    if [ $? -eq 0 ]; then
        echo "Found valid community: $community"
    fi
done < $wordlist
```

#### Information Extractor
```bash
#!/bin/bash
target=$1
community=$2

echo "System Information:"
snmpwalk -v2c -c $community $target 1.3.6.1.2.1.1

echo "Network Interfaces:"
snmpwalk -v2c -c $community $target 1.3.6.1.2.1.2.2.1.2

echo "Process Information:"
snmpwalk -v2c -c $community $target 1.3.6.1.2.1.25.1.6.0
```

## Defensive Measures

### Secure SNMP Configuration

```bash
# Disable SNMP if not needed
systemctl stop snmpd
systemctl disable snmpd

# Configure SNMPv3 with authentication
# In /etc/snmp/snmpd.conf:
createUser myuser MD5 mypassword DES
rouser myuser

# Disable SNMPv1/v2c
# Remove community string configurations
```

### Best Practices

- **Use SNMPv3**: Implement encryption and authentication.
- **Strong Community Strings**: Use complex, unique strings.
- **Access Controls**: Limit SNMP access by IP/network.
- **Minimal Exposure**: Only expose necessary information.
- **Regular Audits**: Monitor SNMP access and configuration.

### Detection and Monitoring

```bash
# Monitor SNMP access
tcpdump -i any port 161

# Check SNMP logs
tail -f /var/log/snmpd.log

# Analyze unusual SNMP queries
grep "snmp" /var/log/syslog
```

## Common Attack Vectors

### Information Gathering
- Network topology discovery.
- System configuration extraction.
- User account enumeration.
- Process and service identification.

### Credential Harvesting
- Extract stored passwords.
- Identify service accounts.
- Discover configuration files.
- Find backup credentials.

### Network Reconnaissance
- ARP table analysis.
- Routing table examination.
- Interface configuration review.
- Network device identification.