# 🛰️ Secure File Upload Implementation

## Introduction to File Upload Security
[!INFO] Insecure file upload mechanisms can lead to severe vulnerabilities such as Remote Code Execution (RCE), Directory Traversal, and more. This guide details secure strategies for managing file uploads in web applications.

## File Type Whitelisting & Blacklisting
[!SUCCESS] Ensure only trusted file types are allowed by implementing a strict whitelist or cautious blacklist.

```php
$allowedExtensions = ['jpg', 'jpeg', 'png', 'gif'];
$fileExtension = pathinfo($_FILES['upload']['name'], PATHINFO_EXTENSION);
if (!in_array($fileExtension, $allowedExtensions)) {
    die('Invalid file type');
}
```

## MIME Type Validation
[!SUCCESS] Confirm uploaded files match expected MIME types.

```php
$expectedMIMEType = ['image/jpeg', 'image/png'];
$fileMimeType = $_FILES['upload']['type'];
if (!in_array($fileMimeType, $expectedMIMEType)) {
    die('Invalid file type');
}
```

## File Size and Content Validation
[!SUCCESS] Limit file sizes and check the integrity of uploaded files.

```php
$allowedSize = 5000000; // 5MB
if ($_FILES['upload']['size'] > $allowedSize) {
    die('File too large');
}
```

## Temporary Storage with Random Names
[!SUCCESS] Store temporarily with unique, random names to prevent malicious overwrites.

```php
$tmpName = $_FILES['upload']['tmp_name'];
$storedName = bin2hex(random_bytes(16)) . '.' . pathinfo($_FILES['upload']['name'], PATHINFO_EXTENSION);
move_uploaded_file($tmpName, $tempDir . $storedName);
```

## Moving Files to Secure Locations
[!SUCCESS] Move validated files to a secure directory with restricted permissions.

```php
$secureUploadDir = '/var/www/html/uploads/';
if (!is_dir($secureUploadDir)) {
    mkdir($secureUploadDir, 0755, true);
}
move_uploaded_file($_FILES['upload']['tmp_name'], $secureUploadDir . $storedName);
```

## Random File Names for Security
[!SUCCESS] Use cryptographically secure random names to store files.

```php
$storedName = bin2hex(random_bytes(16)) . '.' . pathinfo($fileName, PATHINFO_EXTENSION);
move_uploaded_file($_FILES['upload']['tmp_name'], $secureUploadDir . $storedName);
```

## Ensuring Proper File MimeType Handling
[!SUCCESS] Verify MIME type to match expected content and ensure secure handling.

```php
$mimeType = mime_content_type($_FILES['upload']['tmp_name']);
if (!in_array($mimeType, ['image/jpeg', 'image/png'])) {
    die('Invalid file type');
}
move_uploaded_file($_FILES['upload']['tmp_name'], $secureUploadDir . $storedName);
```

---

## Further Security Measures
[!INFO] Implement additional hardening techniques for comprehensive protection.

### System Function Restrictions
[!WARNING] Disable potentially dangerous functions in PHP to mitigate risks.
```ini
disable_functions = exec,shell_exec,system,passthru,popen,proc_open,file_get_contents,file_put_contents,fwrite,include,require
```

### Error Handling Security
[!SUCCESS] Secure error management by logging detailed errors server-side and displaying generic messages.

```php
if (!move_uploaded_file($tmpName, $destination)) {
    error_log("File upload failed: $tmpName -> $destination");
    die("File upload failed. Please try again.");
}
```

### Web Server Configuration
[!SUCCESS] Restrict file execution in uploads directory and deny direct access to PHP files.
```apache
<Directory "/var/www/uploads">
    php_flag engine off
    AddType text/plain .php .phtml .pht
    RemoveHandler .php .phtml .php3 .php4 .php5
</Directory>

<Files "*.php">
    Order Allow,Deny
    Deny from all
</Files>
```
```nginx
location /uploads {
    location ~ \.php$ {
        deny all;
        return 403;
    }
}
```

### Container and Infrastructure Security
[!SUCCESS] Use isolation strategies to further secure file upload processes.
- **Separate Upload Server**
- **Containerized Processing**
- **Network Segmentation**

**PHP Open Base Directory:**
```ini
open_basedir = /var/www/html/:/tmp/:/var/tmp/
```

---

## Additional Security Checklist
[!SUCCESS] Comprehensive checklist for secure file upload implementation.

### File Processing Security
[!CHECK]
1. **File Size Limits:** Limit uploads to a reasonable size.
2. **Malware Scanning:** Use ClamAV or similar tools to scan files.
3. **Library Updates:** Keep all libraries up-to-date with security patches.

**File Size Check Example:**
```php
if ($_FILES['upload']['size'] > 5000000) {
    die("File too large");
}
```

### Web Application Firewall (WAF)
[!CHECK] Implement WAF rules to protect against common attacks.
```apache
SecRule FILES "@detectSQLi" "id:1001,phase:2,block,msg:'SQL Injection in file'"
SecRule ARGS:filename "@contains .." "id:1003,phase:2,block,msg:'Directory traversal attempt'"
```

### Content Sanitization
[!CHECK] Strip metadata and reprocess images to ensure safety.
```php
function sanitizeImage($inputPath, $outputPath) {
    $image = imagecreatefromjpeg($inputPath);
    if ($image === false) {
        return false;
    }
    
    // Create clean image without metadata
    $result = imagejpeg($image, $outputPath, 90);
    imagedestroy($image);
    
    return $result;
}
```

### Monitoring and Logging
[!CHECK] Log security events for later review.
```php
function logSecurityEvent($event, $details) {
    $logEntry = [
        'timestamp' => date('Y-m-d H:i:s'),
        'ip' => $_SERVER['REMOTE_ADDR'],
        'user_agent' => $_SERVER['HTTP_USER_AGENT'],
        'event' => $event,
        'details' => $details
    ];
    
    error_log(json_encode($logEntry), 3, '/var/log/security.log');
}
```

### Rate Limiting
[!CHECK] Implement rate limiting to prevent abuse.
```php
function checkUploadRate($userId) {
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    
    $key = "upload_rate:$userId";
    $current = $redis->get($key) ?: 0;
    
    if ($current >= 10) {
        return false;
    }
    
    $redis->incr($key);
    $redis->expire($key, 3600);
    
    return true;
}
```

---

## Penetration Testing Checklist
[!INFO] Checklist for evaluating upload security during pentests.

### Security Assessment Points

**1. Extension Validation**
- [ ] Whitelist implementation present
- [ ] Blacklist implementation present
- [ ] Both frontend and backend validation
- [ ] Case sensitivity handling
- [ ] Double extension protection

**2. Content Validation**
- [ ] MIME type checking implemented
- [ ] File signature verification
- [ ] Content-Type header validation
- [ ] Cross-validation between extension and content

**3. Access Control**
- [ ] Upload directory hidden from direct access
- [ ] Controlled download mechanism
- [ ] Proper authorization checks
- [ ] File ownership validation

**4. System Hardening**
- [ ] Dangerous functions disabled
- [ ] Error messages sanitized
- [ ] File size limits enforced
- [ ] Upload rate limiting

**5. Infrastructure Security**
- [ ] WAF protection active
- [ ] Antivirus scanning enabled
- [ ] Proper file permissions
- [ ] Network segmentation

### Recommended Mitigations
[!WARNING] Implement these measures to improve security:
1. **Implement dual validation (whitelist + blacklist)**
2. **Add content validation alongside extension checks**
3. **Hide upload directories and use controlled access**
4. **Disable dangerous system functions**
5. **Implement comprehensive logging and monitoring**
6. **Add file size and rate limiting**
7. **Deploy WAF protection as secondary defense**
8. **Regular security updates for all libraries**
9. **Malware scanning for all uploads**
10. **Proper error handling without information disclosure**

Once all security measures are implemented, the web application should be relatively secure against common file upload threats.
---