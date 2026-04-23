>[!ABSTRACT] **Server-Side Request Forgery (SSRF) Cheat Sheet**

SSRF allows an attacker to force a server-side application to make HTTP requests to an unintended location. This is often used to target internal infrastructure that is not accessible from the public internet (the "loopback" interface or internal subnets).

---

>[!INFO] **1. Core Concepts & Targets**

- **Loopback Interface:** Targetting `127.0.0.1` or `localhost` to access administrative panels or services running on the same server.
    
- **Internal Network:** Scanning internal IP ranges (e.g., `192.168.0.X`) to find hidden services or APIs.
    
- **Blind SSRF:** The attacker can cause a request, but the server does not return the response body. Success is measured via DNS/HTTP logs (e.g., Burp Collaborator).
    

---

>[!TIP] **2. Exploitation Vectors**

#### **Basic SSRF (Parameter-Based)**

The most common vector is a parameter that expects a URL, such as `stockApi`, `url`, or `feed`.

> [!Note] **Attacker Payload**
> 
> HTTP
> 
> ```
> POST /product/stock HTTP/1.0
> stockApi=http://127.0.0.1/admin
> ```

#### **SSRF via Referer Header**

Some analytics software logs and visits URLs found in the `Referer` header to track incoming links.

> [!Note] **Attacker Payload**
> 
> HTTP
> 
> ```
> GET / HTTP/1.1
> Referer: http://192.168.0.15:8080/internal-status
> ```

---

>[!CAUTION] **3. Bypassing Defenses**

| **Defense Type**   | **Bypass Strategy**                                 | **Example**                                     |
| ------------------ | --------------------------------------------------- | ----------------------------------------------- |
| **Blacklisting**   | Use decimal/hex IPs or DNS aliases.                 | `http://2130706433` (Decimal for 127.0.0.1)     |
| **Whitelisting**   | Use URL components like `@`, `#`, or `.`            | `http://expected-host@127.0.0.1`                |
| **Open Redirects** | Use a trusted domain to redirect to an internal IP. | `http://trusted.com/redir?url=http://127.0.0.1` |

---

>[!EXAMPLE] **4. Blind SSRF & OAST**

When you can't see the response, use **Out-of-Band Application Security Testing (OAST)**.

> [!CHECK] **Collaborator Verification**
> 
> 1. Input your Burp Collaborator URL into the vulnerable parameter.
>     
> 2. **DNS lookup only:** Request triggered but blocked at the HTTP layer by a firewall.
>     
> 3. **HTTP request:** Successful SSRF; the internal network allowed the egress connection.
>     

---

>[!IMPORTANT] **5. The SSRF Methodology**

1. **Identify:** Find parameters or headers that take URLs/IPs.
    
2. **Fuzz:** Use Intruder to sweep internal IP ranges (e.g., `192.168.0.§1§`).
    
3. **Bypass:** If `127.0.0.1` is blocked, try `localhost`, `127.1`, or encoded variations.
    
4. **Exfiltrate:** Attempt to reach cloud metadata services (e.g., `http://169.254.169.254/` for AWS/Azure) to steal IAM credentials.
    

---

>[!DONE] **6. Prevention**

- **Whitelist Domains:** Only allow connections to specific, trusted hostnames.
    
- **Network Segregation:** Ensure the web server cannot reach sensitive internal ports (like 22, 3306, 6379).
    
- **Sanitize Inputs:** Validate that the input is a valid URL and matches the expected protocol (force `https`).
    

