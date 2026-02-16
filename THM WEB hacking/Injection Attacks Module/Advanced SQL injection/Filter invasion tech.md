
# Advanced SQL Injection – Filter Evasion (Summary)

## 📌 Overview
Modern applications often implement keyword filtering and sanitisation to block SQL injection.  
Pentesters bypass these defences using **encoding techniques**, **alternative operators**, and **payload obfuscation**.

---

## 🎯 Goal of Filter Evasion
- Bypass keyword removal (e.g., `OR`, `AND`, `SELECT`)
- Avoid blocked characters (`'`, spaces, `--`)
- Evade client-side validation
- Execute SQL after server-side decoding

---

## 🛠️ Common Filter-Evasion Techniques

### 🔐 Character Encoding

#### ▶ URL Encoding
Encodes characters using `%` + hex ASCII.

Example:

' OR 1=1--
→ %27%20OR%201%3D1--


Used because:
- Filters may not decode before checking
- DB decodes after processing

---

#### ▶ Hex Encoding
Represents strings as hex values.

Example:

'admin' → 0x61646d696e


Bypasses:
- String-matching filters

---

#### ▶ Unicode Encoding
Encodes characters with Unicode escapes.

Example:

admin → \u0061\u0064\u006d\u0069\u006e


Effective when:
- Filters only check ASCII characters

---

## 🧪 Example Filter Bypass Scenario

### 🔎 Application Defence
PHP removes keywords:

OR, AND, UNION, SELECT


Using:

str_replace()


---

### ❌ Normal Injection (Blocked)

Intro to PHP' OR 1=1


→ `OR` removed → payload fails.

---

### ✅ Encoded Payload (Works)

1%27%20||%201%3D1%20--+


Decoded as:

1' || 1=1 --


Meaning:
- `'` → closes string
- `||` → SQL OR operator
- `1=1` → always true
- `--` → comments rest of query

---

## 🧠 Why Encoding Works
- Filters inspect **raw input**
- Server decodes afterward
- SQL engine executes restored payload
- Keyword stripping becomes useless

---

## 📜 Keywords & Symbols Often Filtered

### 🔹 SQL Keywords
- OR
- AND
- UNION
- SELECT
- INSERT
- UPDATE
- DELETE
- DROP
- WHERE
- FROM

### 🔹 Characters
- '
- "
- --
- #
- ;
- /*
- */
- =
- %

### 🔹 Operators Used to Bypass
- `||` (OR)
- `&&` (AND)
- `LIKE`
- `BETWEEN`
- `IN`
- `CASE`

---

## 🛡️ Defensive Takeaways
- Use **Prepared Statements**
- Parameterised queries
- Avoid blacklist-based filtering
- Treat encoded input as hostile
- Disable multi-statement execution

---

## 🎯 Pentester Checklist
- Test encoded payloads
- Bypass keyword stripping
- Inject via backend endpoints
- Avoid client-side validation
- Inspect decoded queries

---