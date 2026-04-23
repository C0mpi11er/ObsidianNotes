
>[!ABSTRACT] **Web Cache Deception (WCD) Cheat Sheet**

Web Cache Deception occurs when an attacker tricks a cache into storing sensitive, dynamic content by exploiting differences in how the **Cache Server** and the **Origin Server** interpret a URL.

---

>[!INFO] **1. The Core Vulnerability**

The attack relies on "Path Mapping Discrepancies":

- **Origin Server (REST Style):** Interprets `/api/user/profile/test.css` as a request for the `/profile` endpoint, ignoring the trailing `/test.css`.
    
- **Cache Server (File Extension Rule):** Sees the `.css` extension and assumes it is a static, non-sensitive resource. It caches the response (which contains the victim's profile data) for the attacker to retrieve.
    

---

>[!TIP] **2. Testing Methodology (Field Workflow)**

1. **Identify Sensitive Endpoint:** Find an endpoint that returns private data (e.g., `/api/orders/123`).
    
2. **Add Arbitrary Segment:** Test if the server ignores trailing paths: `/api/orders/123/foo`. If the response is the same, it's a candidate.
    
3. **Add Static Extension:** Append common cached extensions:
    
    - `.css`, `.js`, `.png`, `.jpg`, `.ico`, `.exe`
        
4. **Check Cache Headers:** Look for indicators of caching in the response:
    
    - `X-Cache: hit`
        
    - `CF-Cache-Status: HIT` (Cloudflare)
        
    - `Age: [seconds]`
        

---

>[!EXAMPLE] **3. Advanced Bypasses: Delimiter Discrepancies**

If simple path appending is blocked, use delimiters to confuse the parsers.

| **Character** | **Encoded** | **Purpose**                                                                                  |
| ------------- | ----------- | -------------------------------------------------------------------------------------------- |
| **#**         | `%23`       | Cache sees a file; Origin treats everything after `#` as a fragment (ignored).               |
| **?**         | `%3F`       | Cache sees a file in the path; Origin treats it as a query string start.                     |
| **;**         | `%3B`       | Often used in Java/Spring to separate parameters; Caches may see it as part of the filename. |

> [!Note] **Exploit Construction**
> 
> Use the PortSwigger [Delimiter List](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list) in **Burp Intruder** to find which characters the Origin ignores but the Cache includes in its "Static" rule.

---

>[!CAUTION] **4. Operational Security (OpSec)**

- **Browser Redirection:** Avoid testing in a standard browser. Many apps redirect users without active sessions, leading to false negatives. Use **Burp Repeater** to maintain session consistency.
    
- **Fat GET Requests:** Be aware that some caches support "Fat GETs" (GET requests with a body), which can lead to unique poisoning scenarios.
    

---

?[!CHECK] **5. Prevention & Remediation**

- **Cache-Control Headers:** Explicitly set `Cache-Control: no-store, private` for all sensitive/dynamic endpoints.
    
- **Disable Unnecessary Headers:** Disable support for unkeyed headers (like `X-Forwarded-Host`) if not required.
    
- **Origin-Cache Sync:** Ensure the Cache and Origin use the same URL parsing logic (Standardized Path Mapping).
    
- **Don't rely on file extensions:** Configure the cache to use the `Content-Type` header rather than just the file extension to decide what to store.
    

---

>[!SUCCESS] **WCD Quick Reference Table**

| **Phase**  | **Action**                         | **Key Indicator**                |
| ---------- | ---------------------------------- | -------------------------------- |
| **Probe**  | Add `/nonexistent.css`             | Same HTTP Status Code            |
| **Verify** | Request same URL twice             | `X-Cache: hit`                   |
| **Prove**  | Request URL from different session | Victim data returned to Attacker |

