# 🛡️ Log Poisoning - Session (/var/lib/php/sessions/sess_ID), Apache (/var/log/apache2/access.log + User-Agent), SSH (/var/log/auth.log)

---

## 📄 Overview

> [!ABSTRACT] 
> This document details the process of log poisoning in a Linux environment, focusing on manipulating PHP session files and Apache logs. The goal is to alter user sessions and hide malicious activities within legitimate server logs.

---

### Steps & Verification

1. **Identify Vulnerable Session Files**

   - Look for writable session directories:
     ```bash
     find /var/lib/php/sessions/ -type f -writable 2>/dev/null
     ```

2. **Modify PHP Sessions**

   - Change the contents of a session file to impersonate another user or inject malicious code.

3. **Apache Log Manipulation**

   - Append fake entries to the access log:
     ```bash
     echo "127.0.0.1 - frank [10/Oct/2000:13:55:36] \"GET /apache_pb.gif HTTP/1.0\" 200 2326" >> /var/log/apache2/access.log
     ```

   - Use User-Agent to mask the origin of malicious traffic:
     ```bash
     echo "127.0.0.1 - frank [10/Oct/2000:13:55:36] \"GET /malicious.php HTTP/1.0\" 404 98" >> /var/log/apache2/access.log
     ```

---

## 🛡️ Manipulating SSH Logs

> [!WARNING]
> Modifying `/var/log/auth.log` can lead to detection by system administrators.

- Alter the authentication log:
  ```bash
  echo "Oct 10 13:55:49 host sshd[28675]: Invalid user admin from 127.0.0.1 port 3456" >> /var/log/auth.log
  ```

---

## 📄 Summary of Log Poisoning Steps

> [!INFO]
> The steps outlined here allow for the manipulation of session data and server logs, potentially allowing an attacker to hide their actions from security monitoring systems.

### Table: Potential Targets in Logs

| File Path          | Purpose |
|-------------------|---------|
| `/var/lib/php/sessions/`  | PHP Session Storage      |
| `/var/log/apache2/access.log`    | Apache Access Log       |
| `/var/log/auth.log`        | SSH Authentication Log |

---

## 🛡️ Verification & Next Steps

> [!SUCCESS]
> After performing log poisoning, ensure that logs reflect the desired changes and verify that security measures are bypassed.

### Checklist:

- Confirm session file alterations.
- Verify fake entries in Apache access logs.
- Ensure authentication attempts are masked appropriately.

> [!NOTE] 
> For post-exploitation activities, review the altered logs to confirm successful log poisoning.