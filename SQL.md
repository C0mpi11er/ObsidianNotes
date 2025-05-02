
### 🗂️ Database-Level SQL Commands (with Examples)

|**Command**|**Syntax**|**Example**|**Description**|
|---|---|---|---|
|**Show Databases**|`SHOW DATABASES;`|—|Lists all databases|
|**Create Database**|`CREATE DATABASE db_name;`|`CREATE DATABASE mydb;`|Creates a new database|
|**Create If Not Exists**|`CREATE DATABASE IF NOT EXISTS db_name;`|`CREATE DATABASE IF NOT EXISTS mydb;`|Avoids error if database exists|
|**Drop Database**|`DROP DATABASE db_name;`|`DROP DATABASE mydb;`|Deletes the database|
|**Use Database**|`USE db_name;`|`USE mydb;`|Switches to the selected database|
|**Show Tables**|`SHOW TABLES;`|—|Lists tables in the current database|
|**Show Table Structure**|`DESCRIBE table_name;` or `SHOW COLUMNS FROM table_name;`|`DESCRIBE users;`|Displays table schema|
|**Show Indexes**|`SHOW INDEX FROM table_name;`|`SHOW INDEX FROM users;`|Lists indexes on the table|
|**Show Create Table**|`SHOW CREATE TABLE table_name;`|`SHOW CREATE TABLE users;`|Displays full `CREATE` statement for the table|
### 📋 SQL Table Syntax Cheat Sheet with Examples

|**Command**|**Syntax**|**Example**|**Description**|
|---|---|---|---|
|**Create Table**|`CREATE TABLE table_name (column_name datatype [constraints], ...);`|`CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(100));`|Creates a new table|
|**Create If Not Exists**|`CREATE TABLE IF NOT EXISTS table_name (...);`|`CREATE TABLE IF NOT EXISTS products (id INT, price DECIMAL(10,2));`|Avoids error if table exists|
|**Drop Table**|`DROP TABLE table_name;`|`DROP TABLE orders;`|Deletes the entire table|
|**Truncate Table**|`TRUNCATE TABLE table_name;`|`TRUNCATE TABLE logs;`|Deletes all rows quickly|
|**Rename Table**|`RENAME TABLE old_name TO new_name;`|`RENAME TABLE clients TO customers;`|Changes the table name|
|**Add Column**|`ALTER TABLE table_name ADD column_name datatype;`|`ALTER TABLE users ADD email VARCHAR(255);`|Adds a new column|
|**Drop Column**|`ALTER TABLE table_name DROP COLUMN column_name;`|`ALTER TABLE users DROP COLUMN email;`|Removes a column|
|**Modify Column**|`ALTER TABLE table_name MODIFY column_name new_datatype;`|`ALTER TABLE users MODIFY name TEXT;`|Changes column type|
|**Rename Column**|`ALTER TABLE table_name RENAME COLUMN old TO new;`|`ALTER TABLE users RENAME COLUMN name TO full_name;`|Renames a column|
|**Add Constraint**|`ALTER TABLE table ADD CONSTRAINT name type (col);`|`ALTER TABLE orders ADD CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id);`|Adds FK, PK, etc.|
|**Drop Constraint**|`ALTER TABLE table DROP CONSTRAINT constraint_name;`|`ALTER TABLE orders DROP CONSTRAINT fk_user;`|Removes a constraint|
|**Show Tables**|`SHOW TABLES;`|—|Lists all tables|
|**Show Columns**|`SHOW COLUMNS FROM table_name;`|`SHOW COLUMNS FROM users;`|Lists columns info|
|**Describe Table**|`DESCRIBE table_name;` or `DESC table_name;`|`DESC users;`|Describes table structure|
|**Insert Into**|`INSERT INTO table (col1, col2) VALUES (val1, val2);`|`INSERT INTO users (id, name) VALUES (1, 'Alice');`|Adds a new row|
|**Update Rows**|`UPDATE table SET col = val WHERE condition;`|`UPDATE users SET name = 'Bob' WHERE id = 1;`|Updates rows|
|**Delete Rows**|`DELETE FROM table WHERE condition;`|`DELETE FROM users WHERE id = 1;`|Deletes specific rows|
|**Select Query**|`SELECT column1, column2 FROM table WHERE condition;`|`SELECT name FROM users WHERE id = 1;`|Queries rows|

Sure! Let’s break down **CRUD in SQL** like you're completely new to it — no jargon, just simple language and real-life examples.

---

## 🧠 What is CRUD?

**CRUD** stands for:

- **C**reate → Add new data
    
- **R**ead → View data
    
- **U**pdate → Change data
    
- **D**elete → Remove data
    

Imagine a **notebook** where you write down names and phone numbers. That’s like a database table. Now:

- If you **add a new name**, you’re doing **Create**.
    
- If you **look at a name**, you’re doing **Read**.
    
- If you **change a number**, you’re doing **Update**.
    
- If you **cross out a name**, you’re doing **Delete**.
    

SQL is just a special language to do this with computer databases.

---

## 🗃️ Let’s say we have a table called `contacts`

```sql
CREATE TABLE contacts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    phone VARCHAR(20)
);
```

---

### 🟢 1. **CREATE** – Add new info

```sql
INSERT INTO contacts (name, phone) VALUES ('Alice', '1234567890');
```

This adds a new row to the table with Alice's name and number.

---

### 🔵 2. **READ** – View info

```sql
SELECT * FROM contacts;
```

This shows **everything** in the `contacts` table.

You can also search for a specific person:

```sql
SELECT * FROM contacts WHERE name = 'Alice';
```

---

### 🟡 3. **UPDATE** – Change existing info

```sql
UPDATE contacts SET phone = '9876543210' WHERE name = 'Alice';
```

This changes Alice's phone number to a new one.

---

### 🔴 4. **DELETE** – Remove info

```sql
DELETE FROM contacts WHERE name = 'Alice';
```

This removes Alice from the table.

---

## 🔁 Summary Table

|Action|SQL Command Example|
|---|---|
|Create|`INSERT INTO contacts (name, phone) VALUES ('Bob', '1112223333');`|
|Read|`SELECT * FROM contacts;`|
|Update|`UPDATE contacts SET phone = '9999999999' WHERE name = 'Bob';`|
|Delete|`DELETE FROM contacts WHERE name = 'Bob';`|

---


Clauses
---
Here's an **updated SQL cheat sheet** that includes `DISTINCT`, `GROUP BY`, `ORDER BY ASC`, and now also **`HAVING`**, complete with syntax, examples, and when to use each.

---

## 🧾 SQL Cheat Sheet: `DISTINCT`, `GROUP BY`, `ORDER BY ASC`, `HAVING`

|**Clause**|**Purpose**|**Basic Syntax**|**Example**|**Result**|
|---|---|---|---|---|
|`DISTINCT`|Removes duplicate rows|`SELECT DISTINCT column FROM table;`|`SELECT DISTINCT city FROM customers;`|Unique city names|
|`GROUP BY`|Groups rows for aggregation|`SELECT column, AGG_FUNC(col2) FROM table GROUP BY column;`|`SELECT city, COUNT(*) FROM customers GROUP BY city;`|Count per city|
|`ORDER BY ASC`|Sorts results in ascending order (default)|`SELECT * FROM table ORDER BY column ASC;`|`SELECT name FROM customers ORDER BY name ASC;`|Alphabetical names|
|`HAVING`|Filters **groups** created by `GROUP BY` based on aggregate values|`SELECT col, AGG_FUNC(col2) FROM table GROUP BY col HAVING condition;`|`SELECT city, COUNT(*) FROM customers GROUP BY city HAVING COUNT(*) > 2;`|Only cities with more than 2 customers|

---

## 🔍 1. `DISTINCT`

### ✅ Use when:

You want only **unique** values.

```sql
SELECT DISTINCT department FROM employees;
```

📌 Lists unique departments.

---

## 🔍 2. `GROUP BY`

### ✅ Use when:

You want to **group** rows and apply an **aggregate function** (e.g., `COUNT`, `SUM`, `AVG`).

```sql
SELECT department, COUNT(*) FROM employees GROUP BY department;
```

📌 Shows how many employees are in each department.

---

## 🔍 3. `ORDER BY ASC`

### ✅ Use when:

You want to **sort** results from smallest to largest or A–Z.

```sql
SELECT name, salary FROM employees ORDER BY salary ASC;
```

📌 Shows employees from lowest to highest salary.

---

## 🔍 4. `HAVING`

### ✅ Use when:

You want to **filter groups**, **not individual rows** (that's what `WHERE` is for).

```sql
SELECT department, COUNT(*) 
FROM employees 
GROUP BY department 
HAVING name LIKE '%hack%';
```

📌 Shows only departments with **more than 5 employees**.

---

## 🧠 Bonus: Combine Everything

```sql
SELECT DISTINCT city, COUNT(*) AS total_customers
FROM customers
GROUP BY city
HAVING COUNT(*) > 2
ORDER BY total_customers ASC;
```

➡️ Result:

- Cities listed only once
    
- Groups by city
    
- Shows only cities with more than 2 customers
    
- Sorted from smallest to largest number of customers
    

---



---

### 🔹 Comparison Operators

|Operator|Description|Example|
|---|---|---|
|`=`|Equal to|`WHERE age = 25`|
|`!=` or `<>`|Not equal to|`WHERE name != 'John'`|
|`>`|Greater than|`WHERE salary > 50000`|
|`<`|Less than|`WHERE score < 80`|
|`>=`|Greater than or equal to|`WHERE marks >= 40`|
|`<=`|Less than or equal to|`WHERE age <= 60`|
|`BETWEEN`|Within a range (inclusive)|`WHERE age BETWEEN 20 AND 30`|
|`IN`|Matches any in a list|`WHERE dept IN ('HR', 'IT')`|
|`LIKE`|Pattern matching (wildcards)|`WHERE name LIKE 'A%'`|
|`IS NULL`|Checks for NULL values|`WHERE address IS NULL`|
|`IS NOT NULL`|Checks for non-NULL|`WHERE email IS NOT NULL`|

---

### 🔹 Logical Operators

|Operator|Description|Example|
|---|---|---|
|`AND`|All conditions must be true|`WHERE age > 18 AND gender = 'M'`|
|`OR`|At least one condition is true|`WHERE city = 'NY' OR city = 'LA'`|
|`NOT`|Reverses the condition|`WHERE NOT (age < 30)`|

---

### 🔹 Arithmetic Operators

|Operator|Description|Example|
|---|---|---|
|`+`|Addition|`SELECT salary + bonus`|
|`-`|Subtraction|`SELECT price - discount`|
|`*`|Multiplication|`SELECT quantity * price`|
|`/`|Division|`SELECT total / items`|
|`%`|Modulo (remainder)|`SELECT 10 % 3`|

---

### 🔹 Other Useful Operators

|Operator|Description|Example|
|---|---|---|
|`EXISTS`|Checks for subquery existence|`WHERE EXISTS (SELECT * FROM orders ...)`|
|`ALL`|Compares with all values|`WHERE salary > ALL (SELECT salary FROM ...)`|
|`ANY` or `SOME`|Compares with any one value|`WHERE score > ANY (SELECT score FROM ...)`|
|`UNION`|Combines results of two queries|`SELECT name FROM A UNION SELECT name FROM B`|

---

### 🧪 Example Combined Query

```sql
SELECT name, salary
FROM employees
WHERE (department = 'IT' OR department = 'HR')
  AND salary > 40000
  AND name LIKE 'J%'
ORDER BY salary DESC;
```


Absolutely! Let me break this down **even more simply**, like I’m explaining it to a total beginner using **real-world examples**. Think of a **table of books** like this:

|name|category|published_date|price|
|---|---|---|---|
|Android Security Internals|Defensive Security|2014-10-14|49.99|
|Bug Bounty Bootcamp|Offensive Security|2021-03-09|59.99|
|Car Hacker's Handbook|Offensive Security|2016-01-01|39.99|
|Designing Secure Software|Defensive Security|2021-12-21|54.99|
|Ethical Hacking|Offensive Security|2021-06-01|44.99|

---

## 🧵 1. `CONCAT()` → Glue text together

### Imagine you want a sentence like:

> Android Security Internals is a type of Defensive Security book.

Use this:

```sql
SELECT CONCAT(name, ' is a type of ', category, ' book.') AS book_info FROM books;
```

### Think of it like:

```text
"Title" + " is a type of " + "Category" + " book."
```

💡 It just **sticks text together**.

---

## 📚 2. `GROUP_CONCAT()` → Merge items into a single list per group

Let’s say you want to list **all book names** in each **category**, like:

> Offensive Security → Bug Bounty Bootcamp, Car Hacker's Handbook, Ethical Hacking

Do this:

```sql
SELECT category, GROUP_CONCAT(name SEPARATOR ', ') AS books
FROM books
GROUP BY category;
```

💡 It’s like making a **comma list** of items **in the same category**.

---

## ✂️ 3. `SUBSTRING()` → Pick part of a word

The `published_date` is `2021-03-09`. You just want the year.

Use this:

```sql
SELECT SUBSTRING(published_date, 1, 4) AS year FROM books;
```

📌 It means:

> Start at character 1, and take 4 characters → `2021`

---

## 🔢 4. `LENGTH()` → How many letters?

If a book name is “Ethical Hacking” (15 characters), you can find this out:

```sql
SELECT LENGTH(name) AS length_of_name FROM books;
```

💡 Includes spaces too!

---

## 📊 AGGREGATE = Think summary

### 5. `COUNT()` → How many books?

```sql
SELECT COUNT(*) AS total_books FROM books;
```

✅ It counts rows. Think: “How many books are on the list?” → 5

---

### 6. `SUM()` → Add prices

```sql
SELECT SUM(price) AS total_price FROM books;
```

✅ Adds all prices:  
49.99 + 59.99 + 39.99 + 54.99 + 44.99 = 249.95

---

### 7. `MAX()` → Biggest number or latest date

```sql
SELECT MAX(published_date) AS newest FROM books;
```

✅ Gives: `2021-12-21` → the newest book.

---

### 8. `MIN()` → Smallest number or oldest date

```sql
SELECT MIN(published_date) AS oldest FROM books;
```

✅ Gives: `2014-10-14` → the oldest book.

---

## 💬 Real Life Analogy

|FUNCTION|MEANING IN REAL LIFE|
|---|---|
|`CONCAT()`|Making a sentence by combining words|
|`GROUP_CONCAT()`|Making a list of all books in one category|
|`SUBSTRING()`|Reading only part of a word or date|
|`LENGTH()`|Counting how many letters are in a title|
|`COUNT()`|Counting how many books are on your shelf|
|`SUM()`|Adding up the prices of all your books|
|`MAX()`|Finding the most expensive or newest item|
|`MIN()`|Finding the cheapest or oldest item|

---

