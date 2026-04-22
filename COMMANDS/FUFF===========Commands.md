
---

# 🌀 ffuf: High-Speed Fuzzing

`ffuf` is a professional-grade web fuzzer. Its power comes from the ability to place the `FUZZ` keyword anywhere in an HTTP request.

---

### 📂 1. The "Standard" Directory Discovery

The baseline scan for finding hidden paths.

Bash

```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.15/FUZZ -t 100 -mc 200,301,302
```

> [!NOTE] **Tactical Breakdown**
> 
> - `-w`: Path to your wordlist.
>     
> - `-u`: The target URL with the **`FUZZ`** keyword where the wordlist entries will be injected.
>     
> - `-t 100`: ffuf handles high threading extremely well. 100 is a safe "fast" default.
>     
> - `-mc 200,301`: **Match Code.** Only show results with these status codes.
>     

---

### 📄 2. Extension & File Fuzzing

Use this to find backups, configuration files, or scripts.

Bash

```
ffuf -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.15/FUZZ -e .php,.txt,.bak,.zip -mc 200
```

> [!TIP] **Extension Logic**
> 
> - `-e`: Comma-separated list of extensions. ffuf will try `word.php`, `word.txt`, etc.
>     
> - This is often faster than the equivalent Gobuster `-x` flag.
>     

---

### 🕵️ 3. Subdomain & VHost Fuzzing

Essential for identifying hidden development environments or internal portals.

Bash

```
# Subdomain Fuzzing
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.target.htb/

# VHost Fuzzing (When the IP is the same)
ffuf -w list.txt -u http://target.htb -H "Host: FUZZ.target.htb" -fs 1495
```

> [!IMPORTANT] **The Filter Flag (`-fs`)**
> 
> - `-H`: Injects the wordlist into the **Host Header**.
>     
> - `-fs 1495`: **Filter Size.** When fuzzing VHosts, the server often returns a "Default" page for every wrong guess. Find the size of that default page and use `-fs` to hide it.
>     

---

### 🔑 4. Parameter & GET/POST Fuzzing

Use this to find hidden parameters (like `?debug=true`) that could lead to LFI, SQLi, or RCE.

Bash

```
# GET Parameter Fuzzing
ffuf -w list.txt -u http://10.10.10.15/admin/config.php?FUZZ=test -mc 200

# POST Data Fuzzing
ffuf -w list.txt -u http://10.10.10.15/login.php -X POST -d "username=admin&password=FUZZ" -mr "Login successful"
```

> [!SUCCESS] **Data Injection**
> 
> - `-X POST`: Changes the HTTP method.
>     
> - `-d`: Defines the POST data.
>     
> - `-mr "Login successful"`: **Match Regexp.** Only show results where the response body contains this specific string.
>     

---

### 🛠️ 5. Advanced Filtering (The "Noise" Reducer)

ffuf provides powerful ways to filter out the "garbage" responses that clutter your terminal.

Bash

```
ffuf -w list.txt -u http://10.10.10.15/FUZZ -fc 404 -fs 0 -fl 15
```

|**Flag**|**Function**|**Use Case**|
|---|---|---|
|`-fc`|**Filter Code**|Hide specific status codes (e.g., 403).|
|`-fs`|**Filter Size**|Hide responses of a specific byte size.|
|`-fl`|**Filter Lines**|Hide responses with a specific number of lines.|
|`-fw`|**Filter Words**|Hide responses with a specific word count.|

---

### 🚀 6. Proxying through Burp Suite

Use this when you find a "hit" and want to manually inspect the request/response in Burp.

Bash

```
ffuf -w list.txt -u http://10.10.10.15/FUZZ -x http://127.0.0.1:8080
```

> [!ABSTRACT] **Analysis Profile**
> 
> - `-x`: Routes all ffuf traffic through a proxy.
>     
> - **Warning:** This will significantly slow down the scan, so only use it for small wordlists or specific targets.
>     

---

### 📂 Comparison: ffuf vs. Gobuster

|**Feature**|**Gobuster**|**ffuf**|
|---|---|---|
|**Speed**|Fast|**Extremely Fast**|
|**Recursion**|Yes|Yes (`-recursion`)|
|**VHost**|Yes|**Superior** (via Headers)|
|**Flexibility**|Limited to URL/DNS|**Anywhere** (via FUZZ keyword)|
|**Filtering**|Status Code only|Code, Size, Lines, Words, Regex|

> [!TIP] **Obsidian Callout**
> 
> Since you use `Obsidian Advanced Slides`, these commands make great code-block slides for your technical presentations!

