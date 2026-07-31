# 🛰️ External Information Gathering

## 📚 Scanning for Open Ports and Services

### 🔍 Initial Scan with Nmap
```bash
[!INFO] Perform an initial port discovery using nmap.
```
```bash
sudo nmap --open -oA quick_scan TARGET_IP
```

### 🔍 Detailed Service Enumeration
```bash
# Conduct a detailed service enumeration including version detection and scripts.
[!SUCCESS] Ensure services are mapped with their respective versions for further exploitation.

sudo nmap --open -p- -A -oA full_scan TARGET_IP
```

## 🌐 DNS Zone Transfer

### 🔍 Identify Vulnerable DNS Servers
```bash
# Check if the target is running a DNS service and its banner.
[!INFO] Use Nmap for initial identification of open ports.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Look for unknown or suspicious banners indicating non-standard services, e.g., `1337_HTB_DNS`.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS
```

### 🌐 Attempt Zone Transfer
```bash
# Execute zone transfer to gather additional DNS information.
[!SUCCESS] Use `dig` for authoritative DNS zone transfers.

dig AXFR inlanefreight.local @TARGET_IP
```
```bash
# Extract flags and subdomains from the response.
flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"
```

## 📦 Host File Configuration

### 🔧 Add Discovered Hosts to `/etc/hosts`
```bash
# Ensure all discovered subdomains are added to /etc/hosts.
[!SUCCESS] Configure your hosts file for ease of access.

sudo tee -a /etc/hosts > /dev/null <<EOT

## inlanefreight hosts 
TARGET_IP inlanefreight.local blog.inlanefreight.local careers.inlanefreight.local dev.inlanefreight.local gitlab.inlanefreight.local ir.inlanefreight.local status.inlanefreight.local support.inlanefreight.local tracking.inlanefreight.local vpn.inlanefreight.local monitoring.inlanefreight.local
EOT

# Verify the hosts file configuration.
cat /etc/hosts | grep inlanefreight
```

## 🎯 HTB Academy Lab Solutions

### 🔍 Question 1: Banner Grab Non-Standard Service
```bash
# Identify non-standard services through banners.
[!SUCCESS] The DNS banner provides critical information.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Extract the specific version from the output.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS

Answer: 1337_HTB_DNS
```

### 🌐 Question 2: DNS Zone Transfer Flag
```bash
# Perform a zone transfer to find the flag.
[!SUCCESS] The flag is found in TXT records.

dig AXFR inlanefreight.local @TARGET_IP

flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"

Answer: HTB{DNs_ZOn3_Tr@nsf3r}
```

### 📍 Question 7: Flag Subdomain FQDN
```bash
# Identify the flag subdomain's fully qualified domain name.
[!SUCCESS] The zone transfer output provides the necessary information.

flag.inlanefreight.local. 86400 IN TXT "HTB{...}"

Answer: flag.inlanefreight.local
```

### 🔍 Question 3: Additional VHost Discovery
```bash
# Use ffuf to discover additional virtual hosts.
[!SUCCESS] Fuzzing for virtual hosts reveals hidden services.

curl -sI http://TARGET_IP/ -H "Host: defnotvalid.inlanefreight.local" | grep "Content-Length:"
# Result: Content-Length: 15157

ffuf -w /opt/useful/seclists/Discovery/DNS/namelist.txt:FUZZ -u http://TARGET_IP/ -H 'Host:FUZZ.inlanefreight.local' -fs 15157
```
```bash
# Analyze the results for additional vhosts.
Results:
blog                    [Status: 200, Size: 8708]
careers                 [Status: 200, Size: 51810]
dev                     [Status: 200, Size: 2048]
gitlab                  [Status: 302, Size: 113]
ir                      [Status: 200, Size: 28545]
monitoring              [Status: 200, Size: 56]    # ← Additional vhost not in DNS
status                  [Status: 200, Size: 917]
support                 [Status: 200, Size: 26635]
tracking                [Status: 200, Size: 35185]
vpn                     [Status: 200, Size: 1578]

Answer: monitoring
```

## 🔄 Information Gathering Workflow

### 📊 Systematic Approach
```bash
# Follow a systematic approach for comprehensive information gathering.
[!INFO] Document every step and output.

# 1. Initial port discovery
sudo nmap --open -oA quick_scan TARGET_IP

# 2. Service enumeration
sudo nmap --open -p- -A -oA full_scan TARGET_IP

# 3. DNS zone transfer attempt
dig axfr DOMAIN @TARGET_IP

# 4. Subdomain/vhost discovery
ffuf -w wordlist -u http://TARGET/ -H 'Host:FUZZ.domain' -fs INVALID_SIZE

# 5. Host file configuration
sudo tee -a /etc/hosts <<< "TARGET_IP domain subdomain1.domain subdomain2.domain"

# 6. Service-specific enumeration
# Continue with FTP, HTTP, SMTP, etc., detailed analysis.
```

### 🎯 Attack Surface Mapping
```bash
# Categorize services for prioritization and attack planning.
[!INFO] This helps in identifying high-value targets.

Web Services:     80, 443, 8080, 8443
Email Services:   25, 110, 143, 587, 993, 995
File Transfer:    21, 22, 69, 873
Database:         1433, 3306, 5432, 1521
Management:       161, 623, 8080, 9090
Remote Access:    22, 23, 3389, 5985, 5986

# Priority targets:
- Web applications (immediate attack surface)
- Anonymous/weak authentication services
- Known vulnerable service versions
- Management interfaces
- Email services for user enumeration
```

## ⚠️ Reconnaissance Best Practices

### 🔒 Stealth Considerations
```bash
# Implement stealth techniques to minimize footprint.
[!WARNING] Ensure not to trigger alarms or logs.

nmap -T2 --scan-delay 5s TARGET_IP
nmap -f TARGET_IP
nmap --source-port 53 TARGET_IP
nmap -D RND:10 TARGET_IP
```

### 📋 Documentation Standards
```bash
# Maintain thorough documentation for reference.
[!INFO] Document every finding and exploit attempt.

- All scan outputs saved with timestamps
- Service version information recorded
- Subdomain/vhost discovery results
- Anonymous access findings
- Potential attack vectors identified
- Evidence screenshots for findings
```

## 💡 Key Takeaways

1. **Systematic enumeration** reveals the complete attack surface.
2. **DNS zone transfers** provide valuable subdomain intelligence.
3. **VHost discovery** uncovers hidden applications.
4. **Service versioning** enables vulnerability research.
5. **Anonymous access** often provides immediate foothold opportunities.
6. **Comprehensive documentation** is essential for attack planning.
7. **Multiple enumeration methods** ensure complete coverage.

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations. Keep 100% of the information.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context:
   - Use `[!ABSTRACT]` or `[!TLDR]` for summaries, overviews, or tool descriptions.
   - Use `[!INFO]` or `[!NOTE]` for general reference, metadata, or machine IPs.
   - Use `[!CHECK]` or `[!SUCCESS]` for methodology steps, verification, or successful exploits.
   - Use `[!WARNING]`, `[!DANGER]` or `[!ERROR]` for potential risks and cautionary notes.
   - Use `[!BUG]` for bugs and issues in tools or scripts.
   - Use `[!QUESTION]` for questions that need further investigation.
4. Use inline code blocks (`) for commands and output snippets.

---


# 🛰️ External Information Gathering

## 📚 Scanning for Open Ports and Services

### 🔍 Initial Scan with Nmap
```bash
[!INFO] Perform an initial port discovery using nmap.
```
```bash
sudo nmap --open -oA quick_scan TARGET_IP
```

### 🔍 Detailed Service Enumeration
```bash
# Conduct a detailed service enumeration including version detection and scripts.
[!SUCCESS] Ensure services are mapped with their respective versions for further exploitation.

sudo nmap --open -p- -A -oA full_scan TARGET_IP
```

## 🌐 DNS Zone Transfer

### 🔍 Identify Vulnerable DNS Servers
```bash
# Check if the target is running a DNS service and its banner.
[!INFO] Use Nmap for initial identification of open ports.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Look for unknown or suspicious banners indicating non-standard services, e.g., `1337_HTB_DNS`.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS
```

### 🌐 Attempt Zone Transfer
```bash
# Execute zone transfer to gather additional DNS information.
[!SUCCESS] Use `dig` for authoritative DNS zone transfers.

dig AXFR inlanefreight.local @TARGET_IP
```
```bash
# Extract flags and subdomains from the response.
flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"
```

## 📦 Host File Configuration

### 🔧 Add Discovered Hosts to `/etc/hosts`
```bash
# Ensure all discovered subdomains are added to /etc/hosts.
[!SUCCESS] Configure your hosts file for ease of access.

sudo tee -a /etc/hosts > /dev/null <<EOT

## inlanefreight hosts 
TARGET_IP inlanefreight.local blog.inlanefreight.local careers.inlanefreight.local dev.inlanefreight.local gitlab.inlanefreight.local ir.inlanefreight.local status.inlanefreight.local support.inlanefreight.local tracking.inlanefreight.local vpn.inlanefreight.local monitoring.inlanefreight.local
EOT

# Verify the hosts file configuration.
cat /etc/hosts | grep inlanefreight
```

## 🎯 HTB Academy Lab Solutions

### 🔍 Question 1: Banner Grab Non-Standard Service
```bash
# Identify non-standard services through banners.
[!SUCCESS] The DNS banner provides critical information.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Extract the specific version from the output.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS

Answer: 1337_HTB_DNS
```

### 🌐 Question 2: DNS Zone Transfer Flag
```bash
# Perform a zone transfer to find the flag.
[!SUCCESS] The flag is found in TXT records.

dig AXFR inlanefreight.local @TARGET_IP

flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"

Answer: HTB{DNs_ZOn3_Tr@nsf3r}
```

### 📍 Question 7: Flag Subdomain FQDN
```bash
# Identify the flag subdomain's fully qualified domain name.
[!SUCCESS] The zone transfer output provides the necessary information.

flag.inlanefreight.local. 86400 IN TXT "HTB{...}"

Answer: flag.inlanefreight.local
```

### 🔍 Question 3: Additional VHost Discovery
```bash
# Use ffuf to discover additional virtual hosts.
[!SUCCESS] Fuzzing for virtual hosts reveals hidden services.

curl -sI http://TARGET_IP/ -H "Host: defnotvalid.inlanefreight.local" | grep "Content-Length:"
# Result: Content-Length: 15157

ffuf -w /opt/useful/seclists/Discovery/DNS/namelist.txt:FUZZ -u http://TARGET_IP/ -H 'Host:FUZZ.inlanefreight.local' -fs 15157
```
```bash
# Analyze the results for additional vhosts.
Results:
blog                    [Status: 200, Size: 8708]
careers                 [Status: 200, Size: 51810]
dev                     [Status: 200, Size: 2048]
gitlab                  [Status: 302, Size: 113]
ir                      [Status: 200, Size: 28545]
monitoring              [Status: 200, Size: 56]    # ← Additional vhost not in DNS
status                  [Status: 200, Size: 917]
support                 [Status: 200, Size: 26635]
tracking                [Status: 200, Size: 35185]
vpn                     [Status: 200, Size: 1578]

Answer: monitoring
```

## 🔄 Information Gathering Workflow

### 📊 Systematic Approach
```bash
# Follow a systematic approach for comprehensive information gathering.
[!INFO] Document every step and output.

# 1. Initial port discovery
sudo nmap --open -oA quick_scan TARGET_IP

# 2. Service enumeration
sudo nmap --open -p- -A -oA full_scan TARGET_IP

# 3. DNS zone transfer attempt
dig axfr DOMAIN @TARGET_IP

# 4. Subdomain/vhost discovery
ffuf -w wordlist -u http://TARGET/ -H 'Host:FUZZ.domain' -fs INVALID_SIZE

# 5. Host file configuration
sudo tee -a /etc/hosts <<< "TARGET_IP domain subdomain1.domain subdomain2.domain"

# 6. Service-specific enumeration
# Continue with FTP, HTTP, SMTP, etc., detailed analysis.
```

### 🎯 Attack Surface Mapping
```bash
# Categorize services for prioritization and attack planning.
[!INFO] This helps in identifying high-value targets.

Web Services:     80, 443, 8080, 8443
Email Services:   25, 110, 143, 587, 993, 995
File Transfer:    21, 22, 69, 873
Database:         1433, 3306, 5432, 1521
Management:       161, 623, 8080, 9090
Remote Access:    22, 23, 3389, 5985, 5986

# Priority targets:
- Web applications (immediate attack surface)
- Anonymous/weak authentication services
- Known vulnerable service versions
- Management interfaces
- Email services for user enumeration
```

## ⚠️ Reconnaissance Best Practices

### 🔒 Stealth Considerations
```bash
# Implement stealth techniques to minimize footprint.
[!WARNING] Ensure not to trigger alarms or logs.

nmap -T2 --scan-delay 5s TARGET_IP
nmap -f TARGET_IP
nmap --source-port 53 TARGET_IP
nmap -D RND:10 TARGET_IP
```

### 📋 Documentation Standards
```bash
# Maintain thorough documentation for reference.
[!INFO] Document every finding and exploit attempt.

- All scan outputs saved with timestamps
- Service version information recorded
- Subdomain/vhost discovery results
- Anonymous access findings
- Potential attack vectors identified
- Evidence screenshots for findings
```

## 💡 Key Takeaways

1. **Systematic enumeration** reveals the complete attack surface.
2. **DNS zone transfers** provide valuable subdomain intelligence.
3. **VHost discovery** uncovers hidden applications.
4. **Service versioning** enables vulnerability research.
5. **Anonymous access** often provides immediate foothold opportunities.
6. **Comprehensive documentation** is essential for attack planning.
7. **Multiple enumeration methods** ensure complete coverage.

---


# 🛰️ External Information Gathering

## 📚 Scanning for Open Ports and Services

### 🔍 Initial Scan with Nmap
```bash
[!INFO] Perform an initial port discovery using nmap.
```
```bash
sudo nmap --open -oA quick_scan TARGET_IP
```

### 🔍 Detailed Service Enumeration
```bash
# Conduct a detailed service enumeration including version detection and scripts.
[!SUCCESS] Ensure services are mapped with their respective versions for further exploitation.

sudo nmap --open -p- -A -oA full_scan TARGET_IP
```

## 🌐 DNS Zone Transfer

### 🔍 Identify Vulnerable DNS Servers
```bash
# Check if the target is running a DNS service and its banner.
[!INFO] Use Nmap for initial identification of open ports.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Look for unknown or suspicious banners indicating non-standard services, e.g., `1337_HTB_DNS`.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS
```

### 🌐 Attempt Zone Transfer
```bash
# Execute zone transfer to gather additional DNS information.
[!SUCCESS] Use `dig` for authoritative DNS zone transfers.

dig AXFR inlanefreight.local @TARGET_IP
```
```bash
# Extract flags and subdomains from the response.
flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"
```

## 📦 Host File Configuration

### 🔧 Add Discovered Hosts to `/etc/hosts`
```bash
# Ensure all discovered subdomains are added to /etc/hosts.
[!SUCCESS] Configure your hosts file for ease of access.

sudo tee -a /etc/hosts > /dev/null <<EOT

## inlanefreight hosts 
TARGET_IP inlanefreight.local blog.inlanefreight.local careers.inlanefreight.local dev.inlanefreight.local gitlab.inlanefreight.local ir.inlanefreight.local status.inlanefreight.local support.inlanefreight.local tracking.inlanefreight.local vpn.inlanefreight.local monitoring.inlanefreight.local
EOT

# Verify the hosts file configuration.
cat /etc/hosts | grep inlanefreight
```

## 🎯 HTB Academy Lab Solutions

### 🔍 Question 1: Banner Grab Non-Standard Service
```bash
# Identify non-standard services through banners.
[!SUCCESS] The DNS banner provides critical information.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Extract the specific version from the output.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS

Answer: 1337_HTB_DNS
```

### 🌐 Question 2: DNS Zone Transfer Flag
```bash
# Perform a zone transfer to find the flag.
[!SUCCESS] The flag is found in TXT records.

dig AXFR inlanefreight.local @TARGET_IP

flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"

Answer: HTB{DNs_ZOn3_Tr@nsf3r}
```

### 📍 Question 7: Flag Subdomain FQDN
```bash
# Identify the flag subdomain's fully qualified domain name.
[!SUCCESS] The zone transfer output provides the necessary information.

flag.inlanefreight.local. 86400 IN TXT "HTB{...}"

Answer: flag.inlanefreight.local
```

### 🔍 Question 3: Additional VHost Discovery
```bash
# Use ffuf to discover additional virtual hosts.
[!SUCCESS] Fuzzing for virtual hosts reveals hidden services.

curl -sI http://TARGET_IP/ -H "Host: defnotvalid.inlanefreight.local" | grep "Content-Length:"
# Result: Content-Length: 15157

ffuf -w /opt/useful/seclists/Discovery/DNS/namelist.txt:FUZZ -u http://TARGET_IP/ -H 'Host:FUZZ.inlanefreight.local' -fs 15157
```
```bash
# Analyze the results for additional vhosts.
Results:
blog                    [Status: 200, Size: 8708]
careers                 [Status: 200, Size: 51810]
dev                     [Status: 200, Size: 2048]
gitlab                  [Status: 302, Size: 113]
ir                      [Status: 200, Size: 28545]
monitoring              [Status: 200, Size: 56]    # ← Additional vhost not in DNS
status                  [Status: 200, Size: 917]
support                 [Status: 200, Size: 26635]
tracking                [Status: 200, Size: 35185]
vpn                     [Status: 200, Size: 1578]

Answer: monitoring
```

## 🔄 Information Gathering Workflow

### 📊 Systematic Approach
```bash
# Follow a systematic approach for comprehensive information gathering.
[!INFO] Document every step and output.

# 1. Initial port discovery
sudo nmap --open -oA quick_scan TARGET_IP

# 2. Service enumeration
sudo nmap --open -p- -A -oA full_scan TARGET_IP

# 3. DNS zone transfer attempt
dig axfr DOMAIN @TARGET_IP

# 4. Subdomain/vhost discovery
ffuf -w wordlist -u http://TARGET/ -H 'Host:FUZZ.domain' -fs INVALID_SIZE

# 5. Host file configuration
sudo tee -a /etc/hosts <<< "TARGET_IP domain subdomain1.domain subdomain2.domain"

# 6. Service-specific enumeration
# Continue with FTP, HTTP, SMTP, etc., detailed analysis.
```

### 🎯 Attack Surface Mapping
```bash
# Categorize services for prioritization and attack planning.
[!INFO] This helps in identifying high-value targets.

Web Services:     80, 443, 8080, 8443
Email Services:   25, 110, 143, 587, 993, 995
File Transfer:    21, 22, 69, 873
Database:         1433, 3306, 5432, 1521
Management:       161, 623, 8080, 9090
Remote Access:    22, 23, 3389, 5985, 5986

# Priority targets:
- Web applications (immediate attack surface)
- Anonymous/weak authentication services
- Known vulnerable service versions
- Management interfaces
- Email services for user enumeration
```

## ⚠️ Reconnaissance Best Practices

### 🔒 Stealth Considerations
```bash
# Implement stealth techniques to minimize footprint.
[!WARNING] Ensure not to trigger alarms or logs.

nmap -T2 --scan-delay 5s TARGET_IP
nmap -f TARGET_IP
nmap --source-port 53 TARGET_IP
nmap -D RND:10 TARGET_IP
```

### 📋 Documentation Standards
```bash
# Maintain thorough documentation for reference.
[!INFO] Document every finding and exploit attempt.

- All scan outputs saved with timestamps
- Service version information recorded
- Subdomain/vhost discovery results
- Anonymous access findings
- Potential attack vectors identified
- Evidence screenshots for findings
```

## 💡 Key Takeaways

1. **Systematic enumeration** reveals the complete attack surface.
2. **DNS zone transfers** provide valuable subdomain intelligence.
3. **VHost discovery** uncovers hidden applications.
4. **Service versioning** enables vulnerability research.
5. **Anonymous access** often provides immediate foothold opportunities.
6. **Comprehensive documentation** is essential for attack planning.
7. **Multiple enumeration methods** ensure complete coverage.

---


# 🛰️ External Information Gathering

## 📚 Scanning for Open Ports and Services

### 🔍 Initial Scan with Nmap
```bash
[!INFO] Perform an initial port discovery using nmap.
```
```bash
sudo nmap --open -oA quick_scan TARGET_IP
```

### 🔍 Detailed Service Enumeration
```bash
# Conduct a detailed service enumeration including version detection and scripts.
[!SUCCESS] Ensure services are mapped with their respective versions for further exploitation.

sudo nmap --open -p- -A -oA full_scan TARGET_IP
```

## 🌐 DNS Zone Transfer

### 🔍 Identify Vulnerable DNS Servers
```bash
# Check if the target is running a DNS service and its banner.
[!INFO] Use Nmap for initial identification of open ports.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Look for unknown or suspicious banners indicating non-standard services, e.g., `1337_HTB_DNS`.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS
```

### 🌐 Attempt Zone Transfer
```bash
# Execute zone transfer to gather additional DNS information.
[!SUCCESS] Use `dig` for authoritative DNS zone transfers.

dig AXFR inlanefreight.local @TARGET_IP
```
```bash
# Extract flags and subdomains from the response.
flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"
```

## 📦 Host File Configuration

### 🔧 Add Discovered Hosts to `/etc/hosts`
```bash
# Ensure all discovered subdomains are added to /etc/hosts.
[!SUCCESS] Configure your hosts file for ease of access.

sudo tee -a /etc/hosts > /dev/null <<EOT

## inlanefreight hosts 
TARGET_IP inlanefreight.local blog.inlanefreight.local careers.inlanefreight.local dev.inlanefreight.local gitlab.inlanefreight.local ir.inlanefreight.local status.inlanefreight.local support.inlanefreight.local tracking.inlanefreight.local vpn.inlanefreight.local monitoring.inlanefreight.local
EOT

# Verify the hosts file configuration.
cat /etc/hosts | grep inlanefreight
```

## 🎯 HTB Academy Lab Solutions

### 🔍 Question 1: Banner Grab Non-Standard Service
```bash
# Identify non-standard services through banners.
[!SUCCESS] The DNS banner provides critical information.

sudo nmap -sC -sV TARGET_IP
```
```bash
# Extract the specific version from the output.
nmap output:
53/tcp   open  domain   (unknown banner: 1337_HTB_DNS)
| dns-nsid:
|_ bind.version: 1337_HTB_DNS

Answer: 1337_HTB_DNS
```

### 🌐 Question 2: DNS Zone Transfer Flag
```bash
# Perform a zone transfer to find the flag.
[!SUCCESS] The flag is found in TXT records.

dig AXFR inlanefreight.local @TARGET_IP

flag.inlanefreight.local. 86400 IN TXT "HTB{DNs_ZOn3_Tr@nsf3r}"

Answer: HTB{DNs_ZOn3_Tr@nsf3r}
```

### 📍 Question 7: Flag Subdomain FQDN
```bash
# Identify the flag subdomain's fully qualified domain name.
[!SUCCESS] The zone transfer output provides the necessary information.

flag.inlanefreight.local. 86400 IN TXT "HTB{...}"

Answer: flag.inlanefreight.local
```

### 🔍 Question 3: Additional VHost Discovery
```bash
# Use ffuf to discover additional virtual hosts.
[!SUCCESS] Fuzzing for virtual hosts reveals hidden services.

curl -sI http://TARGET_IP/ -H "Host: defnotvalid.inlanefreight.local" | grep "Content-Length:"
# Result: Content-Length: 15157

ffuf -w /opt/useful/seclists/Discovery/DNS/namelist.txt:FUZZ -u http://TARGET_IP/ -H 'Host:FUZZ.inlanefreight.local' -fs 15157
```
```bash
# Analyze the results for additional vhosts.
Results:
blog                    [Status: 200, Size: 8708]
careers                 [Status: 200, Size: 51810]
dev                     [Status: 200, Size: 2048]
gitlab                  [Status: 302, Size: 113]
ir                      [Status: 200, Size: 28545]
monitoring              [Status: 200, Size: 56]    # ← Additional vhost not in DNS
status                  [Status: 200, Size: 917]
support                 [Status: 200, Size: 26635]
tracking                [Status: 200, Size: 35185]
vpn                     [Status: 200, Size: 1578]

Answer: monitoring
```

## 🔄 Information Gathering Workflow

### 📊 Systematic Approach
```bash
# Follow a systematic approach for comprehensive information gathering.
[!INFO] Document every step and output.

# 1. Initial port discovery
sudo nmap --open -oA quick_scan TARGET_IP

# 2. Service enumeration
sudo nmap --open -p- -A -oA full_scan TARGET_IP

# 3. DNS zone transfer attempt
dig axfr DOMAIN @TARGET_IP

# 4. Subdomain/vhost discovery
ffuf -w wordlist -u http://TARGET/ -H 'Host:FUZZ.domain' -fs INVALID_SIZE

# 5. Host file configuration
sudo tee -a /etc/hosts <<< "TARGET_IP domain subdomain1.domain subdomain2.domain"

# 6. Service-specific enumeration
# Continue with FTP, HTTP, SMTP, etc., detailed analysis.
```

### 🎯 Attack Surface Mapping
```bash
# Categorize services for prioritization and attack planning.
[!INFO] This helps in identifying high-value targets.

Web Services:     80, 443, 8080, 8443
Email Services:   25, 110, 143, 587, 993, 995
File Transfer:    21, 22, 69, 873
Database:         1433, 3306, 5432, 1521
Management:       161, 623, 8080, 9090
Remote Access:    22, 23, 3389, 5985, 5986

# Priority targets:
- Web applications (immediate attack surface)
- Anonymous/weak authentication services
- Known vulnerable service versions
- Management interfaces
- Email services for user enumeration
```

## ⚠️ Reconnaissance Best Practices

### 🔒 Stealth Considerations
```bash
# Implement stealth techniques to minimize footprint.
[!WARNING] Ensure not to trigger alarms or logs.

nmap -T2 --scan-delay 5s TARGET_IP
nmap -f TARGET_IP
nmap --source-port 53 TARGET_IP
nmap -D RND:10 TARGET_IP
```

### 📋 Documentation Standards
```bash
# Maintain thorough documentation for reference.
[!INFO] Document every finding and exploit attempt.

- All scan outputs saved with timestamps
- Service version information recorded
- Subdomain/vhost discovery results
- Anonymous access findings
- Potential attack vectors identified
- Evidence screenshots for findings
```

## 💡 Key Takeaways

1. **Systematic enumeration** reveals the complete attack surface.
2. **DNS zone transfers** provide valuable subdomain intelligence.
3. **VHost discovery** uncovers hidden applications.
4. **Service versioning** enables vulnerability research.
5. **Anonymous access** often provides immediate foothold opportunities.
6. **Comprehensive documentation** is essential for attack planning.
7. **Multiple enumeration methods** ensure complete coverage.

---

This guide outlines a comprehensive approach to external information gathering, focusing on systematic techniques and tools like Nmap, DNS zone transfers, and subdomain/virtual host discovery. Proper documentation and stealth are crucial throughout the process.