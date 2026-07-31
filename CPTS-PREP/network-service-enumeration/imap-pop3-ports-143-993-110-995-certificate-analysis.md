---
# 🛰️ IMAP/POP3 - Ports 143/993/110/995, Certificate Analysis

> [!ABSTRACT] Overview of IMAP and POP3 Protocols & Certificate Analysis
> 
> This document covers the protocols, ports, and steps involved in analyzing certificates for IMAP (Internet Message Access Protocol) and POP3 (Post Office Protocol version 3).

---

## 🌍 Ports Overview

IMAP and POP3 use both standard and SSL/TLS secured ports:

```text
143/tcp   → IMAP
993/tcp   → IMAP over TLS/SSL
110/tcp   → POP3
995/tcp   → POP3 over TLS/SSL
```

---

## 📜 Certificate Analysis

### Steps for Analyzing Certificates

#### Step 1: Identify the Server and Ports

```bash
[[nmap]] -p 143,993,110,995 <IP>
```

- Use `nmap` to scan the server for open ports.

#### Step 2: Retrieve Certificate Information

Use openssl commands to obtain certificate details:

```bash
openssl s_client -connect <IP>:993 </dev/null
```
or
```bash
openssl s_client -connect <IP>:995 </dev/null
```

> [!CHECK] Verify the server's SSL/TLS certificate is properly configured and issued by a trusted authority.

#### Step 3: Analyze Certificate Details

Extract relevant information from the certificates:

- **Common Name (CN)**
- **Subject Alternative Names (SANs)**
- **Issuing Authority**

Example output for `openssl s_client -connect <IP>:993`:

```text
depth=2 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert Global Root CA
verify return:1
depth=1 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = RapidSSL RSA Domain Validation Secure Server CA
verify return:1
...
Subject: C=US, ST=NY, L=New York City, O=Example Corp, CN=mail.examplecorp.com
...
```

> [!WARNING] Ensure the certificate's Common Name matches the server name being accessed to prevent man-in-the-middle attacks.

---

## 📝 Summary of Findings

- **IMAP Ports**: 143 (unsecured), 993 (secure)
- **POP3 Ports**: 110 (unsecured), 995 (secure)
- **Certificate Verification**: Use `openssl` to validate certificates.

> [!NOTE] Ensure that the certificate's Subject Alternative Names (SANs) include the domain name being accessed if a wildcard or multi-domain certificate is used.

---

## 🧭 Examination Strategy

### IMAP Certificate Analysis Workflow

1. Identify open ports for IMAP and POP3.
2. Use `[[openssl]]` to retrieve certificates from secure connections.
3. Verify certificate details, including issuer and subject common name.
4. Check SANs (Subject Alternative Names) if multi-domain or wildcard certificates are used.

---

## 📌 Common Certificate Issues

| Issue | Explanation |
|---|---|
| Invalid SSL/TLS Certificates | Self-signed or expired certificates can indicate insecure configurations. |
| Mismatched Hostnames | Common Name in certificate does not match the accessed server domain. |

> [!DANGER] Do not proceed with any analysis if the certificate is invalid, self-signed, or has a mismatched hostname as it poses significant security risks.

---

## 🧠 Mental Model for Certificate Analysis

```text
Open Ports → Retrieve Certificates → Validate Details → Check SANs (if necessary)
```

> [!SUCCESS] Always ensure that SSL/TLS certificates are properly issued and configured to avoid potential security vulnerabilities.
---