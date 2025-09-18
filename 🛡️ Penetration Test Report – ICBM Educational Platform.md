
````markdown
**Date of Assessment:** September 3–8, 2025  
**Tester:** IcΔrusθ
**Scope:** Public-facing website and API endpoints (`icbm.edu.ng`)  
**Deliverable:** Security assessment findings, proof of exploitation, and recommendations  

---

## 1. Executive Summary

Between September 3rd and September 8th, 2025, a penetration test was conducted on the ICBM Educational Platform. Multiple high-risk vulnerabilities were identified, including:

1. Insecure Direct Object References (IDOR) in dashboard endpoints.  
2. Improper API access control, allowing full **student database extraction**.  
3. Firewall masking bypass through **favicon hash leakage**, exposing the **real server IP**.  
4. Unrestricted brute-force authentication attempts.  
5. File upload misconfigurations (PHP files accepted but execution blocked).  

The combination of these weaknesses allowed complete compromise of sensitive student data.  

A sample dump of all student records has been provided separately in a password-protected archive:  
`all_icbm_student.zip` (password shared securely).  

---

## 2. Methodology

The following methodology was used:

1. **Reconnaissance & Enumeration**  
   - Discovered dashboard endpoints:  `/instructor/dashboard`.  
   - Uploaded PHP test files (execution blocked, but directory traversal confirmed).  
   - Performed directory enumeration using **FFuF**, **Feroxbuster**, and **Gobuster**.  

2. **Authentication Testing**  
   - Observed lack of login attempt rate limiting → brute-force possible.  

3. **API Enumeration**  
   - Found `/api/admin` endpoint (initially redirected).  
   - After extended enumeration (~9 hours), discovered:  
     - `/api/admin/students`  
     - `/api/admin/profiles`  
     - `/api/admin/all`  

4. **Exploitation**  
   - Queried student data via direct object access.  
   - Successfully dumped **entire student database**.  

5. **Firewall Bypass**  
   - Extracted **favicon.png** from the website.  
   - Generated favicon hash and fingerprinted the framework/server.  
   - Identified the **real backend IP** (bypassing firewall masking).  
   - Connected directly to server and continued exploitation.  

---

## 3. Findings

### 3.1 Insecure Direct Object Reference (IDOR)  
- Accessing `/student/dashboard` → switching to `/instructor/dashboard` displayed an unintended page.  
- While no direct leak occurred, this exposed weak role-based access controls.  

### 3.2 API Insecure Access Control  
- The `/api/admin/students` endpoint allowed unauthenticated queries.  
- Student records included:  
   "id": "xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "applicationId": "APP-2025-XXXXX",
  "email": "user@example.com",
  "fullName": "John Doe",
  "status": "pending",
  "createdAt": "2025-07-27T14:06:47.065Z",
  "updatedAt": "2025-08-19T20:16:14.447Z" 

### 3.3 Firewall & IP Leakage via Favicon  
- Favicon hash matched backend server fingerprint.  
- Revealed the **true server IP**, bypassing firewall masking.  
- Enabled **direct access to APIs** that were otherwise hidden.  

### 3.4 Brute-force Risk  
- Login endpoints had no rate limiting or account lockouts.  

### 3.5 File Upload Misconfiguration  
- Arbitrary PHP files could be uploaded but were not executed.  
- Indicates partial filtering; could still be escalated.  

---

## 4. Risk & Impact Summary

- 🔴 **Critical**: Full student database compromised.  
  → Enables identity theft, fraud, GDPR/NDPR violations.  

- 🟠 **High**: Real server IP leakage via `favicon.png`.  
  → Nullifies firewall protection, exposes backend to direct attack.  

- 🟡 **Medium**: Weak firewall configuration.  
  → May allow bypasses of security layers.  

- 🟢 **Low**: Misconfigured headers & file upload controls.  
  → Expands attack surface, potential future exploitation.  

---

## 5. Exploitation Flow

```mermaid
flowchart TD
    A[Reconnaissance] --> B[Found favicon.png]
    B --> C[Generated hash & fingerprinted server]
    C --> D[Real IP exposed]
    D --> E[Firewall masking bypassed]
    E --> F[API endpoints discovered]
    F --> G[Student database dumped]
````

---

## 6. Recommendations

1. **API Security**
    
    - Enforce strict authentication and role-based access control on all `/api/admin/*` endpoints.
        
    - Implement **input validation** and **output sanitization**.
        
2. **Firewall & IP Masking**
    
    - Remove public exposure of `favicon.png` that reveals framework hashes.
        
    - Use **reverse proxy with strict filtering** to fully hide backend IP.
        
3. **Authentication Hardening**
    
    - Enforce rate limiting and account lockouts.
        
    - Implement multi-factor authentication (MFA).
        
4. **File Upload Policy**
    
    - Restrict uploads to **safe formats** only (e.g., `.pdf`, `.jpg`).
        
    - Perform MIME-type validation and virus scanning.
        
5. **Monitoring & Logging**
    
    - Enable detailed API access logs.
        
    - Implement **intrusion detection alerts** for abnormal access patterns.
        
6. **Data Security**
    
    - Encrypt sensitive student records in the database.
        
    - Regularly audit database queries and access permissions.
        

---

## 7. Proof of Data Extraction

A **password-protected archive** of the dumped student database has been submitted alongside this report:

- File: `all_icbm_student.zip`
    
- Password: [Communicated securely]
    

**How to open:**

```bash
unzip all_icbm_student.zip
# Enter password when prompted
```

---

## 8. Conclusion

The ICBM Educational Platform is currently at **high risk** due to insecure APIs, weak firewall masking, and improper access control. The successful extraction of all student records highlights the urgent need for **immediate remediation**.

By addressing the issues listed in Section 6, the platform can significantly reduce its exposure to exploitation and protect sensitive student information.

---

