
Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query or the details of any database errors.

Many techniques such as `UNION` attacks are not effective with blind SQL injection vulnerabilities. This is because they rely on being able to see the results of the injected query within the application's responses. It is still possible to exploit blind SQL injection to access unauthorized data, but different techniques must be used.


---

# 

### 📌 Scenario
request is using tracking id cookies to identify users

```http
Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4
```

```sql
SELECT TrackingId 
FROM TrackedUsers 
WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

- Unsanitized input → **SQL Injection**
    
- Response differs:
    
    - Match → **“Welcome back”**
        
    - No match → no message
        

---

### 🧪 Exploitation

**Confirm vuln**

```http
Cookie: TrackingId=' OR '1'='1
```

**Boolean-based extraction**
# note find sql server model
```http
Cookie: TrackingId=' OR SUBSTRING(@@version,1,1)='M'--
```

- If true → “Welcome back”
    
- If false → no message
    

👉 Repeat with positions to reveal DB version, tables, columns, and data.

---

## Exploiting blind SQL injection by triggering conditional responses - Continued

For example, suppose there is a table called `Users` with the columns `Username` and `Password`, and a user called `Administrator`. You can determine the password for this user by sending a series of inputs to test the password one character at a time.

To do this, start with the following input:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm`

This returns the "Welcome back" message, indicating that the injected condition is true, and so the first character of the password is greater than `m`.

Next, we send the following input:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't`  

This does not return the "Welcome back" message, indicating that the injected condition is false, and so the first character of the password is not greater than `t`.

Eventually, we send the following input, which returns the "Welcome back" message, thereby confirming that the first character of the password is `s`:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's`

We can continue this process to systematically determine the full password for the `Administrator` user.

#### Note

The `SUBSTRING` function is called `SUBSTR` on some types of database. For more details, see the SQL injection cheat sheet.


# Self note 
usue burp intruder and iterate through all char
### ⏭️ Move to Next Character

To test the **second character**, increment the starting position:



' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'administrator'), 2, 1)='M
