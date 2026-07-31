# 🛰️ SQL Basics and Operations

## Drop Table (Permanent Deletion!)
```sql
DROP TABLE logins;
```

## Drop Database (Extremely Dangerous!)
```sql
DROP DATABASE users;
```
[!DANGER] Warning: DROP operations are permanent and cannot be undone!

---

## Common WHERE Clause Conditions

### Comparison Operators
```sql
-- Equality
SELECT * FROM logins WHERE id = 1;

-- Inequality  
SELECT * FROM logins WHERE id != 1;
SELECT * FROM logins WHERE id <> 1;

-- Greater/Less than
SELECT * FROM logins WHERE id > 1;
SELECT * FROM logins WHERE id < 10;
SELECT * FROM logins WHERE id >= 1;
SELECT * FROM logins WHERE id <= 10;
```

### Pattern Matching
```sql
-- LIKE with wildcards
SELECT * FROM logins WHERE username LIKE 'admin%';  -- Starts with 'admin'
SELECT * FROM logins WHERE username LIKE '%min';    -- Ends with 'min'
SELECT * FROM logins WHERE username LIKE '%min%';   -- Contains 'min'

-- IN clause
SELECT * FROM logins WHERE id IN (1, 2, 3);

-- BETWEEN
SELECT * FROM logins WHERE id BETWEEN 1 AND 5;
```

### Logical Operators

#### Basic Logical Operations
```sql
-- AND (both conditions must be true)
SELECT * FROM logins WHERE id > 1 AND username = 'admin';

-- OR (at least one condition must be true)
SELECT * FROM logins WHERE id = 1 OR username = 'admin';

-- NOT (negates the condition)
SELECT * FROM logins WHERE NOT id = 1;
```

#### Operator Evaluation Examples
```sql
-- AND evaluation (returns 1 for true, 0 for false)
SELECT 1 = 1 AND 'test' = 'test';    -- Returns: 1 (true)
SELECT 1 = 1 AND 'test' = 'abc';     -- Returns: 0 (false)

-- OR evaluation  
SELECT 1 = 1 OR 'test' = 'abc';      -- Returns: 1 (true, first condition true)
SELECT 1 = 2 OR 'test' = 'abc';      -- Returns: 0 (false, both conditions false)

-- NOT evaluation
SELECT NOT 1 = 1;                    -- Returns: 0 (false, negation of true)
SELECT NOT 1 = 2;                    -- Returns: 1 (true, negation of false)
```

#### Symbol Operators (Alternative Syntax)
```sql
-- && (same as AND)
SELECT 1 = 1 && 'test' = 'abc';      -- Returns: 0

-- || (same as OR)  
SELECT 1 = 1 || 'test' = 'abc';      -- Returns: 1

-- != (same as NOT EQUAL)
SELECT 1 != 1;                       -- Returns: 0
SELECT 1 != 2;                       -- Returns: 1
```

#### Practical WHERE Clause Examples
```sql
-- NOT with inequality
SELECT * FROM logins WHERE username != 'john';

-- Multiple conditions
SELECT * FROM logins WHERE username != 'john' AND id > 1;

-- Complex logic
SELECT * FROM logins WHERE (id > 1 AND username = 'admin') OR (id < 5 AND username != 'tom');
```

---

## Operator Precedence (Critical for SQL Injection!)

### Precedence Order (High to Low)
1. **Arithmetic**: Division (/), Multiplication (*), Modulus (%)
2. **Arithmetic**: Addition (+), Subtraction (-)  
3. **Comparison**: =, >, <, <=, >=, !=, LIKE
4. **Logical**: NOT (!)
5. **Logical**: AND (&&)
6. **Logical**: OR (||)

### Precedence Examples
```sql
-- Expression: username != 'tom' AND id > 3 - 2
-- Step 1: Arithmetic first: 3 - 2 = 1
-- Step 2: Comparison: username != 'tom' AND id > 1  
-- Step 3: Evaluate both comparisons, then AND

SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;

-- This is evaluated as:
-- SELECT * FROM logins WHERE (username != 'tom') AND (id > 1);
```

### Parentheses Override Precedence
```sql
-- Force different evaluation order with parentheses
SELECT * FROM logins WHERE (username = 'admin' OR username = 'tom') AND id > 1;

-- Without parentheses (different result):
SELECT * FROM logins WHERE username = 'admin' OR password = 'pass' AND id = 1;
-- Evaluated as: username = 'admin' OR (password = 'pass' AND id = 1)

-- SAFE: Use parentheses for clarity
SELECT * FROM logins WHERE (username = 'admin' OR password = 'pass') AND id = 1;
```

#### Common Precedence Gotchas
```sql
-- DANGEROUS: This might not work as expected!
SELECT * FROM logins WHERE username = 'admin' OR password = 'pass' AND id = 1;
-- Evaluated as: username = 'admin' OR (password = 'pass' AND id = 1)

-- SAFE: Use parentheses for clarity
SELECT * FROM logins WHERE (username = 'admin' OR password = 'pass') AND id = 1;
```

### NULL Handling
```sql
-- Check for NULL values
SELECT * FROM logins WHERE password IS NULL;
SELECT * FROM logins WHERE password IS NOT NULL;
```

---

## Useful Functions

### String Functions
```sql
-- Concatenation
SELECT CONCAT(username, ':', password) FROM logins;

-- String length
SELECT username, LENGTH(username) FROM logins;

-- Substring
SELECT SUBSTRING(username, 1, 3) FROM logins;

-- Case conversion
SELECT UPPER(username), LOWER(username) FROM logins;
```

### Date Functions
```sql
-- Current date/time
SELECT NOW();
SELECT CURDATE();
SELECT CURTIME();

-- Date formatting
SELECT DATE_FORMAT(date_of_joining, '%Y-%m-%d') FROM logins;
```

### Aggregate Functions
```sql
-- Count records
SELECT COUNT(*) FROM logins;

-- Maximum/Minimum
SELECT MAX(id), MIN(id) FROM logins;

-- Group by
SELECT username, COUNT(*) FROM logins GROUP BY username;
```

---

## Security Notes

### Bad Practices
```sql
-- NEVER store plain-text passwords!
INSERT INTO logins VALUES(1, 'admin', 'password123', NOW());

-- NEVER use SELECT * in production
SELECT * FROM users;  -- Exposes all data
```

### Good Practices
```sql
-- Use specific column selection
SELECT username, email FROM users;

-- Use parameterized queries (application level)
-- Instead of: "SELECT * FROM users WHERE id = " + userInput
-- Use prepared statements with placeholders

-- Hash passwords before storage
-- INSERT INTO logins VALUES(1, 'admin', SHA2('password123', 256), NOW());
```

---

## HTB Academy Example Scenario

**Target Database:** `employees`  
**Connection:** `mysql -u root -ppassword -h target --skip-ssl`

### Common Enumeration Steps
```sql
-- 1. Show available databases
SHOW DATABASES;

-- 2. Select target database
USE employees;

-- 3. Show tables
SHOW TABLES;

-- 4. Examine table structure
DESCRIBE departments;
DESCRIBE employees;

-- 5. Extract data
SELECT * FROM departments;
SELECT * FROM employees WHERE department = 'Development';

-- 6. Find specific information
SELECT department_id FROM departments WHERE department_name = 'Development';
```

### Expected Workflow
1. Connect: `mysql -u root -ppassword -h target --skip-ssl`
2. List: `SHOW DATABASES;`
3. Select: `USE database_name;`
4. Explore: `SHOW TABLES;`
5. Query: `SELECT * FROM table_name;`
6. Target: `SELECT column FROM table WHERE condition;`