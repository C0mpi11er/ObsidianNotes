---
# 📬 SMTP - Port 25, User Enumeration, Open Relay Testing

---

## 🛠️ Overview & Setup

> [!ABSTRACT]
> This note covers the process of testing for user enumeration and open relay functionality on an SMTP server listening on port 25. Techniques include VRFY (Verify) command usage and MAIL FROM tests to assess security.

---

## 🔍 Initial Testing

### VRFY Command Test
The `VRFY` command is used to verify if a specific user account exists on the SMTP server by attempting to send an email to that address.

```text
VRFY user@example.com
```

> [!SUCCESS]
> If the response includes a "250" code, it indicates the user exists.

### EXPN Command Test
The `EXPN` command can be used to expand mailing list names or aliases into individual email addresses. However, this command is often disabled due to security concerns.

```text
EXPN list-name@example.com
```

> [!WARNING]
> Using `EXPN` could expose sensitive information about user groups and lists on the server.

---

## 📊 Open Relay Testing

### MAIL FROM Test
Checking if an SMTP server acts as an open relay involves sending email from any sender address to a recipient domain outside of the server's own domain. This can be tested with the following steps:

```text
MAIL FROM: user@example.com
RCPT TO: external_user@external_domain.com
DATA
Subject: Test Message
Hello, this is a test message.
.
```

> [!SUCCESS]
> If the SMTP server accepts and processes the email without restrictions, it indicates an open relay condition.

### Relay Checker Tools
Use tools like [[RelayChecker]] or manual tests via Telnet to automate checking for open relays on multiple domains. 

---

## 🔑 User Enumeration Techniques

### Enumerating Users with VRFY & EXPN
To enumerate users effectively:

1. Start by listing common usernames such as `admin`, `root`, and `support`.
2. Use the `VRFY` command to check if these accounts exist.
3. If `EXPN` is enabled, use it to discover more users or groups.

```bash
telnet example.com 25
```

> [!NOTE]
> Some SMTP servers may have rate limiting or other protections against bulk user enumeration attempts.

---

## 🚦 Security Implications

### Risks of Open Relays & User Enumeration
- **Open Relays**: Exposing an open relay can lead to spam and abuse.
- **User Enumeration**: Revealing valid usernames allows attackers to target specific accounts with social engineering or brute force attacks.

> [!DANGER]
> Exploiting user enumeration and open relays is illegal and unethical outside of authorized testing scenarios.

---

## 📄 SMTP Protocol Commands

| Command | Description |
|---|---|
| `VRFY` | Verify if a specific email address exists on the server. |
| `EXPN` | Expand mailing list names into individual addresses (may be disabled). |
| `MAIL FROM` | Specifies the sender's email address for the message being sent. |
| `RCPT TO` | Specifies the recipient's email address for the message being sent. |

---

## 🧠 Exam Mental Model

```text
SMTP Open?
 ├─ VRFY user@example.com → Check if User Exists
 │
 ├─ EXPN list-name@example.com → Get List of Users (if enabled)
 │
 ├─ MAIL FROM: user@example.com, RCPT TO: external_user@external_domain.com → Test for Open Relay
 └─ Use Tools: [[RelayChecker]], Telnet
```

> [!SUCCESS] SMTP Rule of Thumb
> **Whenever you see port 25 open**, immediately think:
>
> ```text
> VRFY user@example.com, EXPN list-name@example.com, MAIL FROM and RCPT TO test for Open Relay.
> ```
> These steps help quickly identify security vulnerabilities in the SMTP service.