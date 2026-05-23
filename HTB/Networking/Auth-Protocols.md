# Authentication Protocols

> [!info] Overview  
> Authentication protocols verify the identity of:
> 
> - Users
>     
> - Devices
>     
> - Services
>     
> 
> They help prevent:
> 
> - Unauthorized access
>     
> - MITM attacks
>     
> - Credential theft
>     

---

# Common Authentication Protocols

|Protocol|Purpose|
|---|---|
|Kerberos|Ticket-based authentication|
|SSL/TLS|Secure encrypted communication|
|OAuth|Third-party authorization|
|OpenID|Single identity login|
|SAML|Exchange authentication data|
|PAP|Plaintext password authentication|
|CHAP|Challenge-response authentication|
|EAP|Authentication framework|
|SSH|Secure remote authentication|
|HTTPS|Secure web authentication|
|LEAP|Cisco wireless authentication|
|PEAP|Secure tunneled authentication|
|PKI|Public/private key infrastructure|
|SSO|Single sign-on|
|MFA/2FA|Multi-factor authentication|
|FIDO|Passwordless authentication|

---

# Kerberos

> [!important] Kerberos  
> Kerberos uses a:
> 
> ```bash
> Key Distribution Center (KDC)
> ```
> 
> and authentication tickets for secure domain authentication.

## Characteristics

- Common in Active Directory
    
- Uses tickets instead of passwords
    
- Mutual authentication
    

---

# SSL & TLS

> [!important] TLS  
> TLS is the successor to SSL and provides secure encrypted communication.

---

# Functions

|Feature|Purpose|
|---|---|
|Encryption|Protect data confidentiality|
|Authentication|Verify identities|
|Integrity|Prevent tampering|

---

# Common Usage

|Protocol|Uses TLS/SSL|
|---|---|
|HTTPS|Secure web traffic|
|SSH|Secure remote access|
|VPNs|Secure tunnels|

---

> [!warning] SSL  
> SSL is outdated and has largely been replaced by TLS.

---

# OAuth

> [!info] OAuth  
> Allows users to grant third-party access without sharing passwords.

### Example

```bash
Login with Google
```

---

# OpenID

> [!info] OpenID  
> Allows one identity to authenticate across multiple websites.

---

# SAML

> [!important] SAML  
> XML-based authentication protocol commonly used for:
> 
> - Enterprise SSO
>     
> - Identity federation
>     

---

# PAP vs CHAP

## PAP (Password Authentication Protocol)

> [!danger] PAP  
> Sends passwords in:
> 
> ```bash
> Cleartext
> ```

Very insecure.

---

## CHAP (Challenge Handshake Authentication Protocol)

> [!important] CHAP  
> Uses a:
> 
> ```bash
> Three-Way Handshake
> ```
> 
> to authenticate users securely.

More secure than PAP.

---

# EAP (Extensible Authentication Protocol)

> [!info] EAP  
> Authentication framework supporting multiple authentication methods.

Used heavily in:

- WiFi authentication
    
- Enterprise networks
    
- VPNs
    

---

# LEAP

> [!warning] LEAP  
> Cisco wireless authentication protocol using:
> 
> ```bash
> RC4 Encryption
> ```

## Weaknesses

- Vulnerable to dictionary attacks
    
- Weak encryption
    
- Largely deprecated
    

---

# PEAP

> [!important] PEAP  
> Uses:
> 
> - TLS tunnel
>     
> - Server-side certificates
>     
> - Stronger encryption
>     

More secure than LEAP.

---

# PEAP Security

|Feature|Benefit|
|---|---|
|TLS Tunnel|Encrypts authentication|
|Certificates|Server verification|
|AES/3DES|Strong encryption|

---

# EAP-TLS

> [!important] EAP-TLS  
> Strong authentication protocol using:
> 
> - Digital certificates
>     
> - PKI
>     
> - Mutual authentication
>     

Considered one of the most secure EAP methods.

---

# PKI (Public Key Infrastructure)

> [!info] PKI  
> Uses:
> 
> - Public keys
>     
> - Private keys
>     
> - Digital certificates
>     

For:

- Encryption
    
- Authentication
    
- Digital signatures
    

---

# MFA & 2FA

## 2FA

Uses two authentication factors.

Example:

- Password + OTP
    

---

## MFA

> [!important] MFA  
> Uses multiple authentication factors:
> 
> - Something you know
>     
> - Something you have
>     
> - Something you are
>     

---

# SSO (Single Sign-On)

> [!info] SSO  
> Allows users to authenticate once and access multiple applications.

---

# FIDO

> [!important] FIDO  
> Open standard for strong/passwordless authentication.

Supports:

- Security keys
    
- Biometrics
    
- Hardware authenticators
    

---

# SSH

> [!important] SSH  
> Secure protocol for:
> 
> - Remote login
>     
> - Command execution
>     
> - Secure file transfer
>     

Uses strong encryption to protect communication.

---

# HTTPS

> [!important] HTTPS  
> Secure version of HTTP using:
> 
> ```bash
> SSL/TLS
> ```

Protects:

- Authentication
    
- Web traffic
    
- Sensitive data
    

---

# LEAP vs PEAP

|Feature|LEAP|PEAP|
|---|---|---|
|Security|Weak|Strong|
|Encryption|RC4|TLS/AES|
|Certificates|No|Yes|
|Status|Deprecated|Widely used|

---

# Authentication Security Goals

|Goal|Purpose|
|---|---|
|Authentication|Verify identity|
|Confidentiality|Protect data|
|Integrity|Prevent tampering|
|Non-repudiation|Verify origin|

---

# Key Takeaways

> [!summary]
> 
> - Authentication protocols verify identities securely
>     
> - TLS replaced SSL
>     
> - Kerberos uses ticket-based authentication
>     
> - OAuth enables third-party authorization
>     
> - PAP is insecure
>     
> - CHAP uses challenge-response authentication
>     
> - PEAP is more secure than LEAP
>     
> - EAP-TLS provides strong certificate-based security
>     
> - MFA significantly improves security
>     
> - HTTPS and SSH protect authentication traffic
>