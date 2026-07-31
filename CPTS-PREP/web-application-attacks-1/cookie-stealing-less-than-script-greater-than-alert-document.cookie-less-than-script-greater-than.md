```markdown
# 🚦 Cookie Stealing - `<script>alert(document.cookie)</script>`

---

## 📝 Overview

> [!ABSTRACT] 
> This document details the process of stealing cookies using JavaScript injection in a web browser.

### Methodology

1. **Intrusion Point**
2. **Exploitation**
3. **Data Exfiltration**

---

## 💡 Injection Technique

The `<script>alert(document.cookie)</script>` payload is used to display all the current session's cookies within an alert box when injected into a web page.

### Steps Involved

1. Identify Vulnerable Input Field
2. Inject Payload
3. Observe Response

> [!SUCCESS] 
> If the page alerts with the user's cookie data, the injection is successful.

---

## 🚦 Example Payload

The payload `<script>alert(document.cookie)</script>` can be injected into various parts of a web application such as search forms or comment fields to steal session cookies.

### Injection Points

- Search Form
- Comment Section
- Login Fields

> [!EXAMPLE] 
> Inject the following script into an input field:
>
> ```html
<iframe srcdoc="<script>alert(document.cookie)</script>" width="0" height="0"></iframe>
```

---

## ⚠️ Potential Risks

Using `<script>alert(document.cookie)</script>` can expose sensitive information such as session IDs, tokens, and other authentication data. This method poses a significant risk to user privacy and security.

### Mitigation Strategies

- Input Validation
- Output Encoding
- Security Headers

> [!WARNING] 
> Never use this payload in production environments or against live systems without explicit permission from the system owners.

---

## 📝 Further Research

Understanding how JavaScript can be used maliciously is crucial for developing secure web applications. Investigate additional methods such as cross-site scripting (XSS) and session hijacking techniques.

### Tools to Use

- Burp Suite
- OWASP ZAP
- ModSecurity

> [!NOTE] 
> Always test security vulnerabilities in a controlled lab environment before applying findings to real-world systems.
```