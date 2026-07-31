# 🛰️ IMAP/POP3 Service Enumeration

## Initial Discovery

### Port Scan for IMAP/POP3 Services
[!INFO] Use `nmap` to detect open ports and services related to IMAP, POP3, and SSL/TLS on standard ports.
```bash
nmap -p110,143,993,995 -sV -sC target
```

### Service Version Detection
[!INFO] Determine the version of IMAP/POP3 services running on the host.

### Banner Grabbing
[!CHECK] Manually connect to the service and retrieve its banner.
```bash
telnet target 143
```
or
```bash
nc target 995
```

## SSL Certificate Analysis

### Extracting Information from Certificates
[!INFO] Use `openssl` to extract certificate details.
```bash
openssl s_client -connect target:993 2>/dev/null | grep -E "commonName|organizationName|stateOrProvinceName|countryName"
```

## Information Gathering

### Enumerate Email Folders and Emails
[!CHECK] After authentication, list folders and retrieve emails.
```bash
# List folders in the mailbox
tag1 LIST "" "*"

# Select a folder (e.g., INBOX)
tag2 SELECT "INBOX"

# Retrieve the first message's body
tag3 FETCH 1 (BODY[])
```

## Authentication Testing

### Test Common Credentials
[!WARNING] Attempt to authenticate with common credentials.
```bash
USER admin
PASS admin

USER root
PASS root
```

### Use Discovered Usernames and Passwords
[!CHECK] Utilize any discovered usernames and passwords for authentication.
```bash
USER discovered_user
PASS common_password
```

## Content Analysis

### Analyze Email Headers
[!INFO] Extract email headers to gather routing information, IP addresses, etc.
```bash
tag3 FETCH 1 (BODY[HEADER])
```

### Document Administrative Contacts
[!NOTE] Look for admin emails and other contact details in the email content or metadata.

## Security Assessment

### Common Vulnerabilities
- **Weak Authentication**: Default passwords.
- **Plaintext Transmission**: Unencrypted connections.
- **Information Disclosure**: Verbose error messages.
- **Certificate Issues**: Self-signed certificates.

### Testing for Certificate Issues
[!WARNING] Check if the server is using self-signed or invalid certificates.

## Enumeration Checklist

### Initial Discovery
- [ ] Port scan for 110, 143, 993, 995.
- [ ] Service version detection.
- [ ] Banner grabbing.
- [ ] SSL certificate analysis.

### Information Gathering
- [ ] Extract organization name from certificates.
- [ ] Identify server FQDN.
- [ ] Analyze custom version strings.
- [ ] Document server capabilities.

### Authentication Testing
- [ ] Test common credential combinations.
- [ ] Use discovered usernames.
- [ ] Check for authentication bypass.
- [ ] Verify account lockout policies.

## Tools and Techniques

### Essential Tools
```bash
telnet               # Basic connection testing
nc                   # Banner grabbing
openssl              # SSL/TLS connection testing
nmap                 # Service detection and scripts
```

### Custom Scripts
[!EXAMPLE] IMAP banner grabber.
```bash
#!/bin/bash
echo "CAPABILITY" | nc $1 143
```
POP3 banner grabber.
```bash
#!/bin/bash
echo "CAPA" | nc $1 110
```

## Defensive Measures

### Secure Configuration
[!INFO] Enforce secure configurations in server settings.
#### Dovecot Configuration Examples:
Disable plaintext authentication:
```plaintext
disable_plaintext_auth = yes
```
Force SSL/TLS encryption:
```plaintext
ssl = required
ssl_cert = </path/to/cert.pem
ssl_key = </path/to/key.pem
```

### Best Practices
- Enforce SSL/TLS.
- Implement strong password policies.
- Rate limiting to prevent brute force attacks.
- Monitor authentication attempts and errors.

## Detection and Monitoring

### Check Mail Server Logs for Authentication Failures
[!INFO] Analyze logs for suspicious activities.
```bash
tail -f /var/log/maillog
grep "authentication failure" /var/log/maillog
```

## Common Attack Vectors

### 1. Credential Brute Force
[!WARNING] Manually test common credentials.
```bash
for user in admin root test; do
    for pass in admin password 123456; do
        # Test credentials
    done
done
```

### 2. Information Disclosure
- Server version information.
- Internal network details.
- Email addresses and contacts.
- Organizational structure.

### 3. Man-in-the-Middle Attacks
- Intercept plaintext connections.
- Certificate validation bypass.
- Credential harvesting.

---

---