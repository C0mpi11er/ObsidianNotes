# 🌐 XSS Types - Stored (persistent), Reflected (non-persistent), DOM-based (client-side)

---

## 🔍 Overview

> [!ABSTRACT] 
> Cross-Site Scripting (XSS) is a security vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users. There are three primary types of XSS attacks: Stored, Reflected, and DOM-based.

---

### 📜 Definitions & Examples

> [!INFO]
> **Stored XSS** involves an attacker injecting malicious script into a part of the application where user-supplied data is stored (e.g., database) and subsequently served back to other users when they access that content. This type is also known as Persistent XSS.

> [!NOTE] 
> An example scenario for Stored XSS could be when input validation isn't enforced, allowing attackers to inject scripts into comments or forums which are then stored and executed by all visitors who view the injected data.

---

### Reflected XSS

> [!INFO]
> **Reflected XSS** occurs when a malicious script is injected via a user's interaction with an application. The attacker needs to trick the victim into submitting the payload, usually through URL parameters or form submissions. This type of attack is also called Non-Persistent XSS.

> [!EXAMPLE]
```text
https://example.com/search?q=<script>alert('XSS')</script>
```

---

### DOM-based XSS

> [!INFO] 
> **DOM-based XSS** occurs when the JavaScript on a page manipulates the Document Object Model (DOM) in such a way that it introduces vulnerabilities. This type of attack is client-side and does not rely on external sources like HTTP requests.

> [!NOTE]
> DOM-based XSS can be exploited through various methods, including direct HTML injection or manipulating event handlers. It requires careful handling of user input within JavaScript to prevent attacks.

---

## 📝 Technical Details & Prevention

### Stored XSS

- **Prevention:**
  - Implement strict input validation and sanitization.
  - Use Content Security Policy (CSP) headers to mitigate script execution.
  
> [!WARNING]
> Failing to properly sanitize user inputs can lead to stored XSS vulnerabilities that persist over time.

---

### Reflected XSS

- **Detection:**
  - Regularly audit forms, URLs, and other interactive elements for potential injection vectors.
- **Mitigation:**
  - Encode output before rendering it in the browser.
  
> [!CAUTION]
> Not applying proper encoding techniques can allow attackers to inject scripts through reflected attacks.

---

### DOM-based XSS

- **Detection & Prevention:**
  - Ensure that all user inputs are treated as untrusted data within JavaScript contexts.
  - Utilize frameworks and libraries designed to mitigate client-side vulnerabilities.
  
> [!ATTENTION]
> Ignoring the handling of user-controlled data in the DOM can expose applications to various types of XSS attacks.

---

## 📝 Common Payloads & Mitigation Techniques

### Stored XSS Example

```text
<script>alert('Stored XSS')</script>
```

- **Mitigation:**
  - Sanitize and escape all database inputs.
  
> [!ERROR]
> Incorrect sanitization can lead to persistent injection of malicious scripts.

---

### Reflected XSS Example

```text
https://example.com/search?q=<script>alert('Reflected XSS')</script>
```
 
- **Mitigation:**
  - Validate and sanitize all URL parameters before they are used.
  
> [!FAILURE]
> Inadequate validation can allow attackers to inject scripts via URL parameters.

---

### DOM-based XSS Example

```javascript
document.write(location.hash);
```

- **Mitigation:**
  - Avoid using `innerHTML`, `eval()`, and other dangerous functions that execute HTML or JavaScript directly.
  
> [!DANGER]
> Using unsafe methods like `eval()` can introduce vulnerabilities leading to arbitrary code execution.

---

## 🧠 Mental Model

```text
XSS Open?
 ├─ Stored? → Sanitize Database Inputs
 │   └─ No Output Encoding Needed (If Data is Secure)
 ├─ Reflected? → Encode URL Parameters Before Use
 │   └─ Prevent Injection Vectors in Forms & URLs
 └─ DOM-Based? → Treat All User Input as Untrusted in JavaScript Contexts
```

> [!SUCCESS]
> When dealing with XSS vulnerabilities, always ensure that any user-controlled data is treated securely and sanitized appropriately to prevent script injection.