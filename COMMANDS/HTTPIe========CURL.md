curl alternativ
# 🥧 HTTPie: Human-Friendly HTTP Client

`HTTPie` (command: `http`) is a modern, professional-grade command-line HTTP client. Its power comes from natural syntax, built-in JSON support, and beautiful colorized output.

### 🌐 1. The "Standard" GET Request

The baseline command for fetching web pages or testing API endpoints.

Bash

```
http GET api.example.com/users?limit=10 sort==desc
```

> [!NOTE] **Tactical Breakdown**
> 
> - `GET`: The HTTP method (HTTPie defaults to GET if omitted, but it is good practice to specify).
>     
> - `sort==desc`: Using `==` appends parameters directly to the URL query string.
>     

### 📦 2. Sending JSON (POST/PUT)

Use this to interact with RESTful APIs. HTTPie assumes you want to send JSON by default.

Bash

```
http POST api.example.com/users username=admin password=supersecret active:=true
```

> [!TIP] **JSON Logic**
> 
> - `=`: Sends the value as a string (e.g., `"username": "admin"`).
>     
> - `:=`: Sends raw JSON types, like booleans, arrays, or integers (e.g., `"active": true`).
>     

### 📝 3. Form Data & File Uploads

Essential for simulating website login forms or uploading documents.

Bash

```
http -f POST example.com/login username=admin password=supersecret resume@~/documents/resume.pdf
```

> [!IMPORTANT] **The Form Flag (`-f`)**
> 
> - `-f`: **Form.** Converts the request from JSON to `application/x-www-form-urlencoded` or `multipart/form-data`.
>     
> - `@`: Automatically reads the file from your local disk and attaches it to the upload request.
>     

### 🔑 4. Headers & Authentication

Use this to bypass authentication gateways or spoof your User-Agent.

Bash

```
# Custom Headers and Basic Auth
http -a admin:password123 api.example.com/data X-Forwarded-For:127.0.0.1

# Bearer Token Auth
http api.example.com/secure "Authorization: Bearer my_secret_token"
```

> [!SUCCESS] **Header Injection**
> 
> - `-a`: **Auth.** Handles Basic or Digest authentication seamlessly.
>     
> - `:`: Using a single colon explicitly injects an HTTP Header.
>     

### 🛠️ 5. Output Filtering (The "Noise" Reducer)

HTTPie provides powerful ways to control exactly what part of the HTTP exchange you want to see.

Bash

```
http -v https://example.com
```

|**Flag**|**Function**|**Use Case**|
|---|---|---|
|`-v`|**Verbose**|Print the entire HTTP exchange (both the request and response).|
|`-h`|**Headers only**|Hide the body entirely; only print the response headers.|
|`-b`|**Body only**|Hide the headers; only print the response body.|
|`-d`|**Download**|Download the response body to a file, similar to `wget`.|

### 🚀 6. Proxying through Burp Suite

Use this when you want to intercept complex API interactions manually in Burp.

Bash

```
http --proxy=https:http://127.0.0.1:8080 --verify=no https://example.com/api
```

> [!ABSTRACT] **Analysis Profile**
> 
> - `--proxy`: Routes HTTP or HTTPS traffic through your specified listener.
>     
> - `--verify=no`: Ignores SSL certificate errors (crucial since Burp uses a self-signed certificate to intercept HTTPS).
>     

### 📂 Comparison: cURL vs. HTTPie

|**Feature**|**cURL**|**HTTPie**|
|---|---|---|
|**Syntax**|Archaic/Clunky|**Human-readable**|
|**Default Content-Type**|`application/x-www-form-urlencoded`|**`application/json`**|
|**Output**|Raw text|**Built-in colorized/formatted output**|
|**Ubiquity**|**Pre-installed everywhere**|Must be installed manually|
|**Speed/Raw Power**|**Unmatched**|Slightly heavier footprint|

> [!TIP] **Obsidian Callout**
> 
> Since you use `Obsidian Advanced Slides`, these commands make great code-block slides for your technical presentations!