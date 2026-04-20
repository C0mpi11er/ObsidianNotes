

---

# 

> [!INFO] **Definition**
> 
> `httpx-toolkit` is a multi-purpose HTTP toolkit that allows you to run multiple probes using the `retryablehttp` library. It is designed for reliability and speed when dealing with large numbers of hosts.

### 🛠️ Basic Syntax

> [!EXAMPLE] **Common One-Liners**
> 
> Bash
> 
> ```
> # Basic probe from a list
> cat domains.txt | httpx-toolkit
> 
> # The "Recon Standard" (Status, Tech, Title)
> cat subdomains.txt | httpx-toolkit -sc -td -title
> 
> # Check for specific ports
> subfinder -d example.com -silent | httpx-toolkit -p 80,443,8080,8443
> ```

### 🔍 Probing Flags

> [!ABSTRACT] **Information Gathering**
> 
> - `-sc`, `-status-code`: Show HTTP response status codes.
>     
> - `-td`, `-tech-detect`: Display technology stack (e.g., WordPress, Cloudflare).
>     
> - `-title`: Show the page title.
>     
> - `-server`: Display the "Server" header.
>     
> - `-ip`: Display the target's IP address.
>     
> - `-cdn`: Check if the target is behind a CDN (like Akamai or Cloudflare).
>     
> - `-cl`: Show the Content-Length of the response.
>     

### 🛡️ Filtering & Matching

> [!TIP] **Refining Results**
> 
> - `-mc 200`: **Match** only status code 200.
>     
> - `-fc 404,403`: **Filter** out 404 and 403 errors.
>     
> - `-ml 0`: Match only if the content length is 0 (useful for finding redirects).
>     
> - `-path /phpinfo.php`: Look for a specific file across all subdomains.
>     

### ⚡ Performance & Stealth

> [!IMPORTANT] **Tuning the Engine**
> 
> - `-t`, `-threads`: Number of concurrent threads (Default: 50).
>     
> - `-rl`, `-rate-limit`: Global requests per second to avoid being blocked.
>     
> - `-dashboard`: Enables the interactive UI dashboard mentioned in your terminal output.
>     
> - `-silent`: Output only the results (removes the banner).
>     

---

### 💡 Pro-Tip: Handling the "Dashboard" Warning

If you want to try that new dashboard feature mentioned in your output, just append the flag:

Bash

```
subfinder -d icbm.edu.ng -silent | httpx-toolkit -sc -td -title -dashboard
```

_Note: This will open a local web interface to view your results in real-time._

Did the command against `icbm.edu.ng` return any interesting subdomains, or is it still running?