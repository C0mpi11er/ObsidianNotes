# 🧠 SMTP (Simple Mail Transfer Protocol)

---

> [!info] 📌 Overview
> SMTP is a **protocol used to send emails over IP networks**.
>
> It works between:
>
> * Email client (MUA) → Mail server
> * Mail server → Mail server
>
> SMTP is responsible for **sending and forwarding emails**, not retrieving them.

---

# 🌐 SMTP Ports

| Port | Service                                |
| ---- | -------------------------------------- |
| 25   | Standard SMTP (server-to-server)       |
| 587  | Submission (authenticated users)       |
| 465  | SMTP over SSL/TLS (legacy secure SMTP) |

---

# 🧠 SMTP Communication Flow

> [!info]
> Email delivery follows this pipeline:

Client (MUA) ➜ MSA ➜ MTA ➜ MDA ➜ Mailbox (POP3/IMAP)

### Flow Breakdown:

* **MUA (Mail User Agent)** → Email client (Outlook, Thunderbird)
* **MSA (Mail Submission Agent)** → Validates email origin
* **MTA (Mail Transfer Agent)** → Transfers email between servers
* **MDA (Mail Delivery Agent)** → Delivers email to mailbox
* **Mailbox** → Stored email (accessed via IMAP/POP3)

---

# 🧠 SMTP Protocol Characteristics

> [!warning]
> SMTP by default is **plaintext (unencrypted)**

Without TLS:

* Emails can be intercepted
* Credentials are exposed
* Commands are readable on the network

With encryption:

* **STARTTLS** upgrades connection to TLS
* **SSL/TLS (port 465)** provides secure transport

---

# 🧠 SMTP Authentication

> [!info]
> Modern SMTP uses authentication via:

* **ESMTP (Extended SMTP)**
* **SMTP-AUTH**

This prevents:

* Spam abuse
* Open relay misuse
* Unauthorized email sending

---

# 🧠 SMTP Commands

| Command     | Description           |
| ----------- | --------------------- |
| HELO / EHLO | Start session         |
| MAIL FROM   | Set sender            |
| RCPT TO     | Set recipient         |
| DATA        | Start email content   |
| RSET        | Reset session         |
| VRFY        | Verify user existence |
| EXPN        | Expand mailing list   |
| NOOP        | Keep connection alive |
| QUIT        | End session           |
| AUTH PLAIN  | Authenticate user     |

---

# 🧠 SMTP Session Example (Telnet)

```id="g9k2mp"
telnet <IP> 25
```

---

### Start Session

```id="q3x8za"
HELO mail.example.com
EHLO mail.example.com
```

---

### Send Email

```id="k1v9ld"
MAIL FROM:<user@domain.com>
RCPT TO:<target@domain.com>
DATA
Subject: Test
Hello world
.
QUIT
```

---

# 🧠 SMTP VRFY Enumeration

> [!warning]
> Can be used to check if users exist

```id="x7m0tr"
VRFY root
VRFY admin
VRFY testuser
```

Possible responses:

* `250` → User exists
* `550` → User not found
* `252` → Cannot confirm (but may still exist)

---

# 🧠 SMTP Server Configuration (Postfix Example)

```bash id="l8d3qp"
/etc/postfix/main.cf
```

---

### Key Settings

| Setting                         | Description          |
| ------------------------------- | -------------------- |
| myhostname                      | Mail server hostname |
| mynetworks                      | Trusted networks     |
| inet_protocols                  | IPv4/IPv6 support    |
| smtp_tls_session_cache_database | TLS caching          |
| mailbox_size_limit              | Mailbox size control |
| smtpd_helo_restrictions         | HELO validation      |

---

# ⚠️ Dangerous SMTP Settings

| Setting                | Risk             |
| ---------------------- | ---------------- |
| mynetworks = 0.0.0.0/0 | Open relay risk  |
| allow anonymous relay  | Spam abuse       |
| VRFY enabled           | User enumeration |
| no authentication      | Mail spoofing    |

---

# 🧠 Open Relay Risk

> [!warning]
> Open relay allows anyone to send emails through the server.

Example misconfiguration:

```id="o2v6cz"
mynetworks = 0.0.0.0/0
```

### Impact:

* Spam distribution
* Email spoofing
* Blacklisting of mail server

---

# 🧠 SMTP Enumeration (Nmap)

```id="t5b9wa"
nmap -sV -sC -p25 <IP>
```

---

### SMTP Command Discovery

```id="p1n8xq"
smtp-commands
```

Shows:

* Supported commands
* VRFY availability
* SMTP capabilities

---

# 🧠 Open Relay Detection

```id="r6c3vt"
nmap --script smtp-open-relay -p25 <IP>
```

---

# 🧠 SMTP Web Proxy Trick

```id="y9k2dl"
CONNECT <IP>:25 HTTP/1.0
```

Used when SMTP is accessed via HTTP proxy.

---

# 🧠 Email Header Information

> [!info]
> Email headers contain:

* Sender & recipient
* Mail route history
* Timestamp data
* Server hops
* SPF/DKIM validation results

---

# 🧠 SMTP Security Mechanisms

| Mechanism | Purpose                         |
| --------- | ------------------------------- |
| SPF       | Validates sending IP            |
| DKIM      | Digital email signing           |
| DMARC     | Policy enforcement for spoofing |
| STARTTLS  | Encrypts communication          |

---

# 🧠 SMTP Security Issues

| Issue             | Impact                  |
| ----------------- | ----------------------- |
| Open relay        | Spam abuse              |
| Email spoofing    | Fake sender identity    |
| Plaintext traffic | Credential interception |
| VRFY enabled      | User enumeration        |
| Weak TLS          | Man-in-the-middle risk  |

---

# 🧠 SMTP Footprinting

## Banner Grabbing

```id="v0m4zn"
telnet <IP> 25
```

---

## Command Enumeration

```id="d8x1pl"
EHLO <domain>
```

---

## Nmap Enumeration

```id="b3n7aq"
nmap --script smtp-commands -p25 <IP>
```

---

# 🧠 Key SMTP Tools

| Tool           | Purpose                 |
| -------------- | ----------------------- |
| telnet         | Manual SMTP interaction |
| nmap           | Service enumeration     |
| swaks          | Email testing           |
| smtp-user-enum | User enumeration        |
| msfconsole     | Exploitation            |

---

# 🧠 SMTP Attack Surface

> [!important]
>
> * User enumeration via VRFY/EXPN
> * Open relay exploitation
> * Email spoofing attacks
> * Credential sniffing (if no TLS)
> * Spam injection

---

# 🧠 Key Takeaways

> [!summary]
>
> * SMTP is used for sending emails, not receiving them.
> * It operates mainly on ports **25, 587, 465**.
> * Without TLS, SMTP traffic is insecure.
> * Misconfigurations can lead to **open relay attacks** and **email spoofing**.
> * Enumeration (VRFY, EXPN, Nmap) is critical in penetration testing.
