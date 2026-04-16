
---

# 🛸 Gobuster Precise Recon

Gobuster is the go-to for high-speed discovery. Unlike Nmap, which looks at ports, Gobuster looks at **content** and **structure**.

---

### 📂 1. The "Standard" Directory Discovery

Use this when you have a web server and want to find hidden paths like `/admin`, `/backup`, or `/config`.

Bash

```
gobuster dir -u http://10.10.10.15 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40 -b 404,403
```

> [!NOTE] **Tactical Breakdown**
> 
> - `dir`: Directory brute-forcing mode.
>     
> - `-w`: Uses the "Medium" list (Industry standard for OSCP).
>     
> - `-t 40`: 40 threads is usually the "sweet spot" for speed without crashing the web service.
>     
> - `-b 404,403`: **Hides** Not Found and Forbidden errors for a clean output.
>     

---

### 📄 2. The "Sensitive File" Hunt

Use this when you are looking for specific configuration files, backups, or scripts that shouldn't be public.

Bash

```
gobuster dir -u http://10.10.10.15 -w /usr/share/wordlists/dirb/common.txt -x php,txt,bak,zip,conf,sql
```

> [!TIP] **File Hunting Profile**
> 
> - `-x`: Appends extensions. **Crucial** for finding files like `config.php.bak` or `database.sql`.
>     
> - By adding `.bak` and `.zip`, you often find compressed source code left behind by devs.
>     

---

### 🌐 3. The "Subdomain" Expansion

Use this for external reconnaissance when you have a domain name and want to find hidden infrastructure.

Bash

```
gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -i
```

> [!ABSTRACT] **DNS Profile**
> 
> - `dns`: Switches to DNS mode.
>     
> - `-d`: Defines the target domain.
>     
> - `-i`: **Show IP addresses**—essential for identifying if multiple subdomains point to the same server.
>     

---

### 👻 4. The "VHost" Bypass (Virtual Hosts)

**Essential for Labs:** Use this when a single IP hosts multiple websites. If `http://10.10.10.15` shows a default page, a VHost scan might find `http://dev.target.htb` on that same IP.

Bash

```
gobuster vhost -u http://target.htb -w /usr/share/wordlists/SecLists/Discovery/DNS/combined_subdomains.txt --append-domain
```

> [!IMPORTANT] **VHost Precision**
> 
> - `vhost`: Checks the `Host` header in the HTTP request.
>     
> - `--append-domain`: Automatically turns a wordlist entry like `dev` into `dev.target.htb`.
>     
> - **Note:** Many modern CTFs hide the "real" site behind a VHost that doesn't have a public DNS record.
>     

---

### 🕵️ 5. The "Authenticated" Scan

Use this if you have credentials (e.g., from a successful login or a leak) and want to see what's hidden inside a protected `/dashboard`.

Bash

```
gobuster dir -u http://10.10.10.15/dashboard -w list.txt -U admin -P password123 -c "session=12345"
```

> [!SUCCESS] **Auth Profile**
> 
> - `-U / -P`: Basic HTTP Authentication.
>     
> - `-c`: Passes a **Cookie**. If you intercepted a session cookie in Burp Suite, paste it here to scan as a logged-in user.
>     

---

### 🚀 6. The "Blitz" Scan (Recursive)

Use this only if the server is stable and you want to dive deep into every folder found.

Bash

```
gobuster dir -u http://10.10.10.15 -w list.txt -r --depth 2
```

> [!DANGER] **High Resource Usage**
> 
> - `-r`: **Recursive** mode. If it finds `/admin`, it will automatically start scanning `/admin/`.
>     
> - `--depth 2`: Limits how deep the "rabbit hole" goes to prevent infinite loops.
>     

---

### 📂 Common Status Codes Reference

|**Code**|**Category**|**Meaning**|
|---|---|---|
|**200**|Success|File/Folder exists and is accessible.|
|**204**|No Content|Resource exists but is empty.|
|**301/302**|Redirect|Permanent/Temporary move. Follow the path!|
|**401**|Unauthorized|Requires Auth (Try `-U` and `-P`).|
|**403**|Forbidden|Server knows it exists but won't let you in (try `-x` for files).|
|**500**|Server Error|You might be scanning too fast (`-t` too high).|

---

> [!TIP] **Obsidain Pro-Tip**
> 
> Since you use **Arch Linux**, remember your wordlists are likely in `/usr/share/wordlists/`. If you haven't yet, install `seclists`—it’s the "Gold Standard" for Gobuster.

