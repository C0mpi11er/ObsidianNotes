```markdown
# 📝 Basic Payloads - \<script>alert(1)\</script>, \<img src=x onerror=alert(1)>

> [!ABSTRACT] Overview of Common XSS Payloads
>
> This note covers basic examples of Cross-Site Scripting (XSS) payloads including inline JavaScript alerts and event handler-based alerts.

---

## 📈 Understanding Basic XSS Payloads

### Inline JavaScript Alert

The following payload executes an alert box when injected into a vulnerable web application.

```text
<script>alert(1)</script>
```

> [!NOTE] Injection Point
> This script must be placed within a context that allows JavaScript execution, such as inside HTML tags or attributes.

---

## 📊 Event Handler-Based Alert

Another approach to triggering an alert is using the `onerror` event on an `<img>` tag. This method bypasses certain content security policies by leveraging DOM events instead of inline script blocks.

```text
<img src=x onerror=alert(1)>
```

> [!NOTE] Bypass Techniques
> Using `onerror` can help evade filters that specifically block or sanitize `<script>` tags.

---

## 📝 Example Scenarios

### Scenario 1: Inline Script Execution

Inject the following code into a vulnerable input field on a web page:

```text
<script>alert(1)</script>
```

If successful, an alert box will display `1` once the user submits or navigates through that form.

---

## 📝 Scenario 2: DOM-Based Execution via Image Tag

Trigger an alert by injecting this payload into another part of a web page where `<img>` tags are allowed:

```text
<img src=x onerror=alert(1)>
```

When the image fails to load, due to a non-existent source (`src=x`), the `onerror` event fires, executing the JavaScript inside.

---

## ⚠️ Common Findings

| Payload | Impact |
|---|---|
| `<script>alert(1)</script>` | Immediate client-side interaction via alert box |
| `<img src=x onerror=alert(1)>` | Bypasses certain CSP rules and executes in DOM |

---

# 🔍 Additional XSS Techniques to Explore

```text
- Stored XSS
- Reflected XSS
- DOM-Based XSS
```

> [!SUCCESS] General Rule of Thumb for XSS
>
> Whenever you encounter an input field or user-controlled area within a web application, test it with these basic payloads before moving on. If successful, proceed to analyze the impact and explore further vulnerabilities like session hijacking or data exfiltration.

---
```