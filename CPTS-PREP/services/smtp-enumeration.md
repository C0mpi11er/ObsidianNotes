# 🛰️ SMTP Service Enumeration Techniques

## Initial Discovery

### Port Scan
[!INFO] Use `nmap` to scan for open ports commonly associated with SMTP services.

```bash
nmap -p 25,465,587 target
```

### Banner Grabbing and Version Identification
[!CHECK] Check the server's banner to identify the version of the service running.
```bash
telnet target 25
# or
nc target 25
```
Example Response:
```plaintext
220 InFreight ESMTP v2.11
```

### Capability Enumeration
[!CHECK] Use EHLO/HELO to see what commands the server supports.
```bash
telnet target 25
EHLO test.com
```

## User Enumeration

### VRFY Command Testing
[!WARNING] The `VRFY` command checks if a user exists on the SMTP service.

#### Manual Method:
```bash
telnet target 25
VRFY admin
VRFY user
```
Example Response (if valid):
```plaintext
250 admin <admin@example.com>
```

#### Automated Method:
[!CHECK] Use `smtp-user-enum` to automate the process.
```bash
smtp-user-enum -M VRFY -U /usr/share/wordlists/seclists/Usernames/top-usernames.txt -t target
```

### EXPN Command Testing
[!WARNING] The `EXPN` command expands mailing lists.

#### Manual Method:
```bash
telnet target 25
EXPN listname
```
Example Response (if valid):
```plaintext
250-User1@example.com
250-User2@example.com
250 User3@example.com
```

### RCPT TO Method Testing
[!WARNING] The `RCPT TO` command can be used to check if an address exists.
```bash
telnet target 25
MAIL FROM: user@example.com
RCPT TO: admin@example.com
```
Example Response (if valid):
```plaintext
250 2.1.5 admin@example.com... Recipient ok
```

## Security Testing

### Open Relay Testing
[!WARNING] An open relay allows unauthorized sending of emails through the server.

#### Nmap Method:
```bash
nmap -p25 --script smtp-open-relay target
```
Example Response (if vulnerable):
```plaintext
PORT   STATE SERVICE REASON         SCRIPT-OUTPUT
25/tcp open  smtp   syn-ack ttl 64 smtp-open-relay: The remote mail server appears to be an open relay.
```

#### Manual Method:
```bash
telnet target 25
MAIL FROM: user@example.com
RCPT TO: admin@external.com
DATA
Subject: Open Relay Test
This is a test email.
.
QUIT
```
If the message gets through, it indicates an open relay.

### Authentication Bypass Testing
[!WARNING] Check if SMTP service allows sending emails without authentication.
```bash
telnet target 25
MAIL FROM: user@example.com
RCPT TO: admin@external.com
DATA
Subject: Test Email
This is a test email.
.
QUIT
```
If the message gets through, it means no authentication is required.

### Information Disclosure Assessment
[!WARNING] Look for verbose error messages or banners that may contain sensitive information.
```bash
telnet target 25
HELP
VRFY root
```

## Enumeration Checklist

### Initial Discovery
- [ ] Port scan for 25, 465, 587
- [ ] Banner grabbing and version identification
- [ ] EHLO/HELO command testing
- [ ] Available command enumeration

### User Enumeration
- [ ] VRFY command testing
- [ ] EXPN command testing
- [ ] RCPT TO method testing
- [ ] Automated user enumeration with wordlists

### Security Testing
- [ ] Open relay testing
- [ ] Authentication bypass attempts
- [ ] Information disclosure assessment
- [ ] Error message analysis

---

## Tools and Techniques

### Essential SMTP Tools
```bash
# Manual testing
telnet               # Basic SMTP interaction
nc                   # Banner grabbing

# Automated enumeration
smtp-user-enum       # User enumeration tool
nmap                 # Service detection and scripts

# Specialized tools
swaks                # SMTP testing toolkit
sendemail            # Command-line email sending
```

### Custom Scripts
```bash
# Simple SMTP user checker
#!/bin/bash
target=$1
userlist=$2

while read user; do
    echo "VRFY $user" | nc $target 25 | grep -E "(250|252)"
done < $userlist

# SMTP banner grabber
#!/bin/bash
echo "QUIT" | nc $1 25 | head -1
```

## Defensive Measures

### Secure SMTP Configuration
Disable VRFY and EXPN commands in your configuration files.

#### Postfix Configuration:
```bash
disable_vrfy_command = yes
smtpd_discard_ehlo_keyword_address_maps = hash:/etc/postfix/discard_ehlo
```

#### Sendmail Configuration:
Edit `sendmail.mc` file:
```bash
define(`confPRIVACY_FLAGS', `authwarnings,novrfy,noexpn,restrictqrun')
make
service sendmail restart
```

### Best Practices
- Disable unnecessary commands like VRFY and EXPN.
- Use generic banners to hide version information.
- Implement rate limiting to prevent brute force attacks.
- Require authentication for all outgoing mail.

### Detection and Monitoring

Monitor SMTP logs for suspicious activities:
```bash
tail -f /var/log/maillog
```

Check for enumeration attempts:
```bash
grep -i "vrfy\|expn" /var/log/maillog
grep "User unknown" /var/log/maillog
```

## Common Vulnerabilities

### CVE Examples
- **CVE-2020-7247**: OpenSMTPD remote code execution
- **CVE-2016-10009**: Postfix denial of service
- **CVE-2014-3956**: Exim privilege escalation

### Mitigation Strategies
- Keep your software up to date.
- Disable unnecessary features and services.
- Restrict SMTP access appropriately.
- Use encryption (TLS) for mail transmission.

---

# 🔍 Practical Examples

## HTB Academy Style Enumeration

### Step-by-step Guide

#### Initial Discovery
```bash
telnet target 25
```
Look for banner like: "220 InFreight ESMTP v2.11"

#### Capability Testing
```bash
telnet target 25
EHLO test.com
```

#### User Enumeration with Wordlist
```bash
smtp-user-enum -M VRFY -U /path/to/wordlist.txt -t target -m 60 -w 20
```
Look for: "target: username exists"

### HTB Academy Lab Questions Examples

**Question 1:** "Enumerate the SMTP service and submit the banner"
```bash
telnet target 25
# Extract banner: "220 InFreight ESMTP v2.11"
# Answer: InFreight ESMTP v2.11

# Question 2: "Find the username that exists on the system"
smtp-user-enum -M VRFY -U /usr/share/wordlists/seclists/Usernames/Names/names.txt -t target
telnet target 25
VRFY discovered_username
# Answer: discovered_username
```

## Wordlist-based User Enumeration

### Create Custom Wordlist
```bash
cat > smtp_users.txt << EOF
admin
administrator
root
test
guest
mail
postmaster
webmaster
EOF
smtp-user-enum -M VRFY -U smtp_users.txt -t target
```

---

# 🔍 Summary

This guide provides a comprehensive approach to SMTP service enumeration, including initial discovery, user enumeration techniques, security testing for vulnerabilities such as open relay and information disclosure. It also covers defensive measures and common vulnerabilities related to SMTP services.

## Tools:
- **nmap**
- **smtp-user-enum**
- **telnet**
- **nc**

## Practical Steps:
1. Port scanning
2. Banner grabbing
3. Capability enumeration
4. User enumeration (VRFY, EXPN)
5. Open relay testing

---

# 🛡️ Defensive Measures and Best Practices

Implement security best practices such as disabling unnecessary commands, using generic banners, implementing rate limiting, requiring authentication, and monitoring SMTP logs for suspicious activities.

## Example Configurations:
- **Postfix**: `disable_vrfy_command = yes`
- **Sendmail**: `confPRIVACY_FLAGS` settings to restrict capabilities

By following these steps, you can effectively enumerate and secure your SMTP services against common vulnerabilities.