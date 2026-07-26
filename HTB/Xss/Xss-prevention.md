Here is the **XSS Prevention & Defense** module distilled into the exact same clean, Obsidian-ready format. 

While this is a "defense" module, it is framed through an **attacker-aware lens**: knowing exactly how defenses are implemented tells you exactly where and how they fail.

---

```md
## 🛡️ XSS Prevention & Defense Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **The Golden Rule:** XSS occurs at the intersection of a **Source** (user input) and a **Sink** (where data is rendered/executed). 
> **The Fix:** Secure both. Validate/Sanitize at the Source, Encode at the Sink.

---

### 🧪 2. Front-End Defenses (and Attacker Bypasses)

Front-end defenses are **UI conveniences, not security boundaries**. Attackers bypass them easily via proxy tools (Burp Suite), but knowing them helps identify the intended data format.

#### 🔹 Input Validation (Regex)
*Example:* Enforcing email formats before submission.
> [!ATTENTION]  
> **Attacker Note:** Trivially bypassed by intercepting the request and modifying the payload post-validation.

#### 🔹 Input Sanitization (DOMPurify)
```javascript
import DOMPurify from 'dompurify';
let clean = DOMPurify.sanitize(dirtyInput);
```
> [!CHECK]  
> DOMPurify is highly effective *if* configured correctly. Attackers look for outdated versions or misconfigured allowlists (e.g., allowing `svg` or `math` tags).

#### 🔹 Dangerous Sinks to Avoid (Direct Input)
Never pass unsanitized user input into these contexts:
- **Tags:** `<script>`, `<style>`, HTML comments `<!-- -->`, or dynamic attributes `<div name="INPUT">`.
- **Dangerous JS Properties:** `innerHTML`, `outerHTML`, `document.write()`, `document.writeln()`, `document.domain`.
- **Dangerous jQuery Functions:** `html()`, `parseHTML()`, `add()`, `append()`, `prepend()`, `after()`, `insertAfter()`, `before()`, `insertBefore()`, `replaceAll()`, `replaceWith()`.

---

### ⚙️ 3. Back-End Defenses (The Real Fix)

Back-end controls are mandatory, as front-end controls are easily bypassed.

#### 🔹 Input Validation
Ensure data matches expected types *before* processing.
```php
// PHP Example
if (filter_var($_GET['email'], FILTER_VALIDATE_EMAIL)) {
    // Process
} else {
    // Reject
}
```

#### 🔹 Input Sanitization
Escape special characters before storing or processing.
```php
// PHP (Basic)
$clean = addslashes($_GET['input']); 

// NodeJS (Robust)
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(dirtyInput);
```
> [!ATTENTION]  
> **Attacker Note:** `addslashes()` is **not** sufficient for HTML contexts. It escapes quotes, but not `<` or `>`.

#### 🔹 Output HTML Encoding (The MVP Defense)
Convert special characters into HTML entities *right before rendering*. The browser displays them safely without executing them.
```php
// PHP (Gold Standard)
echo htmlentities($_GET['input'], ENT_QUOTES, 'UTF-8');
// OR
echo htmlspecialchars($_GET['input'], ENT_QUOTES, 'UTF-8');
```
```javascript
// NodeJS
import { encode } from 'html-entities';
console.log(encode('<script>')); // Outputs: &lt;script&gt;
```

---

### 🔒 4. Server Configuration & Defense in Depth

Even with perfect coding, server-level controls mitigate the *impact* of a successful XSS injection.

#### 🔹 Essential HTTP Headers
```http
# Prevent MIME-type sniffing (stops JS execution in uploaded images/text files)
X-Content-Type-Options: nosniff

# Restrict script execution to same-origin only (Blocks remote script loading)
Content-Security-Policy: script-src 'self'; object-src 'none'; base-uri 'self';

# Prevent clickjacking (Bonus)
X-Frame-Options: DENY
```

#### 🔹 Secure Cookie Flags
```http
Set-Cookie: session_id=abc123; HttpOnly; Secure; SameSite=Strict
```
- **`HttpOnly`**: Prevents JavaScript (`document.cookie`) from reading the cookie. *(Nullifies standard session hijacking)*.
- **`Secure`**: Ensures the cookie is only transmitted over HTTPS.
- **`SameSite`**: Mitigates CSRF, which is often chained with XSS.

#### 🔹 Infrastructure Controls
- **HTTPS Everywhere:** Prevents MITM injection.
- **WAF (Web Application Firewall):** Detects and blocks common XSS signatures (e.g., `<script>`, `onerror=`). 
- **Framework Protections:** Leverage built-in auto-escaping (e.g., React, Angular, ASP.NET Razor, Django templates).

---

### 🔍 5. Attacker Perspective: Where Defenses Fail

> [!FAILURE] **Common Defense Gaps to Exploit**
> 1. **Front-end only validation:** Bypassed via Burp Repeater.
> 2. **Incomplete Output Encoding:** Encoding `<` but not `"` allows attribute breakout (`" onmouseover=alert(1)`).
> 3. **CSP Misconfigurations:** `script-src 'self' 'unsafe-inline'` or allowing trusted CDNs with known JSONP endpoints.
> 4. **DOM XSS Sinks:** Back-end encoding doesn't help if the vulnerability is purely client-side (e.g., reading `location.hash` and passing it to `innerHTML`).
> 5. **Missing HttpOnly:** Makes cookie stealing via XSS trivial.

---

## ⚠️ Operator Notes

> [!ATTENTION]

**For Defenders:**  
Output encoding (`htmlspecialchars`) + `HttpOnly` cookies + strict CSP is the holy trinity of XSS prevention.

**For Attackers:**  
When you hit a wall, ask:
1. *Is my payload being HTML-encoded?* (If yes, look for a different sink or DOM XSS).
2. *Is there a CSP?* (If yes, look for CSP bypasses or JSONP endpoints).
3. *Is the cookie HttpOnly?* (If yes, pivot from cookie stealing to phishing, keylogging, or internal network port scanning via the victim's browser).
```

---

### 🔥 Module Complete.
We have now covered:
1. **Core XSS & Contexts**
2. **XSS Phishing & Credential Harvesting**
3. **Blind XSS & Session Hijacking**
4. **XSS Prevention & Defense Mechanics**

This completes a full, end-to-end, Obsidian-ready XSS operational playbook. 

Want to pivot to a new vulnerability class (e.g., **SSRF**, **SSTI**, **IDOR**, **SQLi**) using this exact same high-yield format? Just drop the text or name the topic. 🎯