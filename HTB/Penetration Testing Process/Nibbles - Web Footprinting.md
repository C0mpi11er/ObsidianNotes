This lecture is a masterclass in why **iterative enumeration** beats raw brute force every time. It follows the journey from a seemingly "empty" web server to discovering a specific CMS and identifying a foothold through manual investigation.

Here is the structured summary for your notes.

---

## 🛠️ Phase 1: Initial Discovery & Footprinting

The process began with a "dead end" that was solved by looking under the hood.

- **Tooling:** `whatweb` initially showed a generic Apache server on Ubuntu.
    
- **The "Aha!" Moment:** A manual check of the page source revealed an HTML comment: ``.
    
- **Pivoting:** Running `whatweb` specifically on the `/nibbleblog/` directory identified the technology stack: **PHP, jQuery, and Nibbleblog CMS.**
    

---

## 📂 Phase 2: Targeted Enumeration

Once the CMS was identified, the focus shifted to mapping out the application's structure.

|Tool|Target|Result|
|---|---|---|
|**Gobuster**|`/nibbleblog/`|Found `/admin.php`, `/README`, and `/content/`.|
|**cURL**|`/nibbleblog/README`|Confirmed version **v4.0.3** (Vulnerable to authenticated file upload).|
|**Manual Browsing**|`/content/`|Confirmed **Directory Listing** is enabled.|

---

## 🔐 Phase 3: Bypassing Security Controls

This phase highlights the importance of recognizing when automated tools (like Hydra) will fail and when manual logic must take over.

- **The Obstacle:** Nibbleblog utilizes **Blacklist Protection**. Rapid failed login attempts result in an IP lockout, making automated brute-forcing non-viable.
    
- **Information Gathering:**
    
    - Inspecting `/content/private/users.xml` confirmed the valid username is **`admin`**.
        
    - Inspecting `/content/private/config.xml` revealed frequent mentions of "Nibbles" in site titles and emails.
        
- **The Solution:** Using environmental clues (the box name and site context) to guess the password: **`nibbles`**.
    

---

## 💡 Key Takeaways & Methodology

1. **Source Code is Recon:** Always check for HTML comments; developers often leave breadcrumbs for themselves.
    
2. **Verify Versions Early:** Finding the `README` file confirmed a version known to be vulnerable to a Metasploit module/manual file upload exploit.
    
3. **Respect the WAF/Security Plugin:** If a site has IP blacklisting, stop the automated tools immediately and move to manual "low and slow" guessing based on gathered intel.
    
4. **The "Nibble" Strategy:** Penetration testing is about peeling back layers. A "boring" root page is often just a mask for a vulnerable sub-directory.
    

---

> [!IMPORTANT] **Summary Lesson:** Success in this lab relied on **Directory Listing** being left enabled on sensitive folders like `/content/private/`. Without those XML files, identifying the username and password hints would have been significantly harder.

