# 🛰️ Introduction to IDOR Vulnerabilities

[!INFO] Improper Direct Object References (IDOR) is a security vulnerability that occurs when an application does not properly restrict access to objects based on the identity of the user making the request. This allows attackers to potentially access data or perform actions they shouldn't be able to.

## 📜 Understanding IDOR

### 🔍 Definition and Impact
[!WARNING] IDOR vulnerabilities arise when a direct object reference (e.g., an integer representing an internal identifier) is used in URLs, form submissions, or API calls without proper authorization checks. Attackers can manipulate these references to access unauthorized data.

### 🕵️‍♂️ Identifying IDOR

[!SUCCESS] Look for endpoints that accept user-controlled identifiers (e.g., `user_id`, `profile_id`) and test if they allow access to other users' resources without proper authentication or authorization checks. 

## 🔑 Exploitation Examples

### 🌐 Basic Example: Profile API Access
#### Exploit Scenario
An attacker gains unauthorized access to another user's profile by manipulating the `uid` parameter in a RESTful API call.

[!EXAMPLE]
```http
GET /profile/api.php/profile/5 HTTP/1.1
Host: 94.237.54.192:58374
Cookie: role=employee

# Expected Response:
{
    "uid": 5,
    "uuid": "[UUID_VALUE]",
    "role": "employee", 
    "full_name": "[NAME]",
    "email": "[EMAIL]",
    "about": "[DESCRIPTION]"
}
```

[!SUCCESS]
```bash
curl -s -H "Cookie: role=employee" \
     "http://94.237.54.192:58374/profile/api.php/profile/5"
```

### 📐 Advanced Techniques

#### API Parameter Discovery
[!INFO] Test various parameter names to identify which one is used for object references.

```bash
# Hidden Parameter Testing:
curl "http://target.com/api/user?id=1"
curl "http://target.com/api/user?user_id=1"  
curl "http://target.com/api/user?uid=1"
```

#### UUID and GUID Bypass
[!INFO] Predictable patterns in UUIDs can allow attackers to bypass intended restrictions.

```bash
# Sequential UUIDs (sometimes predictable):
user1: 00000000-0000-0000-0000-000000000001
user2: 00000000-0000-0000-0000-000000000002

# Time-based UUIDs (can be calculated):
user_created_at_time_X: uuid_for_time_X
```

#### Session-Based IDOR
[!INFO] Manipulating session cookies can allow access to unauthorized resources.

```http
GET /profile HTTP/1.1
Host: target.com
Cookie: user_id=1; session=abc123

# Try different user_id values:
Cookie: user_id=2; session=abc123
```

## 🚧 Vulnerable Code Examples

### PHP - Insecure Direct Access
[!FAILURE] No authorization check before accessing documents.

```php
<?php
// Vulnerable: No authorization check
$uid = $_GET['uid'];
$query = "SELECT * FROM documents WHERE user_id = " . $uid;
$result = mysqli_query($connection, $query);

// Displays all documents for any user ID
while ($row = mysqli_fetch_array($result)) {
    echo "<a href='/documents/" . $row['filename'] . "'>" . $row['title'] . "</a>";
}
?>
```

### PHP - Secure Implementation
[!SUCCESS] Verify user ownership before accessing data.

```php
<?php
// Secure: Verify user ownership
session_start();
$uid = $_GET['uid'];
$current_user = $_SESSION['user_id'];

// Check if user can access this data
if ($uid != $current_user && !is_admin($current_user)) {
    die("Access denied");
}

$query = "SELECT * FROM documents WHERE user_id = ? AND (user_id = ? OR ? = 1)";
$stmt = $pdo->prepare($query);
$stmt->execute([$uid, $current_user, is_admin($current_user)]);
?>
```

### API - Insecure Access Control
[!FAILURE] Client-side role validation only.

```php
<?php
// Vulnerable: Client-side role validation only
$input = json_decode(file_get_contents('php://input'), true);
$role = $_COOKIE['role']; // Client-controlled

// Dangerous: No server-side authorization check
if ($_SERVER['REQUEST_METHOD'] == 'PUT') {
    $uid = $input['uid'];
    $uuid = $input['uuid']; 
    $new_role = $input['role'];
    
    if ($uuid == get_user_uuid($uid)) { // Weak validation
        update_user_profile($uid, $input);
    }
}

// Admin functions only check client-side role
if ($_SERVER['REQUEST_METHOD'] == 'POST' && $role == 'admin') {
    create_user($input); // Bypassed if role cookie modified
}
?>
```

### API - Secure Implementation  
[!SUCCESS] Server-side session validation.

```php
<?php
// Secure: Server-side session validation
session_start();
$current_user_id = $_SESSION['user_id'];
$current_user_role = get_user_role_from_db($current_user_id);

$input = json_decode(file_get_contents('php://input'), true);
$target_uid = $input['uid'];

function can_modify_profile($current_id, $target_id, $role) {
    // Users can only modify their own profile
    if ($current_id == $target_id) return true;
    
    // Admins can modify any profile
    if ($role == 'admin') return true;
    
    return false;
}

if ($_SERVER['REQUEST_METHOD'] == 'PUT') {
    if (!can_modify_profile($current_user_id, $target_uid, $current_user_role)) {
        http_response_code(403);
        die(json_encode(['error' => 'Access denied']));
    }
    
    // Prevent privilege escalation
    if (isset($input['role']) && $input['role'] != get_user_role_from_db($target_uid)) {
        if ($current_user_role != 'admin') {
            http_response_code(403);
            die(json_encode(['error' => 'Cannot change role']));
        }
    }
    
    update_user_profile($target_uid, $input);
}

// Admin-only functions with proper validation
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    if ($current_user_role != 'admin') {
        http_response_code(403);
        die(json_encode(['error' => 'Admin access required']));
    }
    create_user($input);
}
?>
```

## 🛡️ Prevention & Hardening

### Access Control Implementation
[!SUCCESS] Implement rule-based access control.

```php
function can_access_user_data($current_user_id, $target_user_id) {
    // Users can only access their own data
    if ($current_user_id == $target_user_id) return true;
    
    // Admins can access all data
    if (is_admin($current_user_id)) return true;
    
    // Managers can access their team data
    if (is_manager($current_user_id) && 
        is_team_member($target_user_id, $current_user_id)) {
        return true;
    }
    
    return false;
}
```

### Indirect Object References

[!SUCCESS] Use secure object reference design.

```php
// Instead of direct user IDs
// OLD: /profile.php?uid=123

// Use session-based access
// NEW: /profile.php (get uid from session)

// Or use mapping tables
$mapping = [
    'abc123' => 1,  // Random token maps to user ID
    'def456' => 2,
];
$uid = $mapping[$_GET['token']];
```

## 🔍 Detection & Monitoring

### Log Analysis
[!INFO] Monitor for IDOR attempts.

```bash
# Monitor for IDOR attempts:
grep -E "uid=|user_id=|id=" /var/log/apache2/access.log | \
grep -E "[?&](uid|user_id|id)=[0-9]+" | \
sort | uniq -c | sort -nr

# Look for rapid sequential requests
awk '{print $1, $7}' /var/log/apache2/access.log | \
grep -E "uid=[0-9]+" | sort | uniq -c | sort -nr
```

### Security Testing Checklist
[!CHECK]
- [ ] Test all URL parameters with different values
- [ ] Analyze JavaScript code for hidden API calls  
- [ ] Check file naming patterns for predictability
- [ ] Test encoded/hashed parameters for reversibility
- [ ] Verify authorization on all data access points
- [ ] Monitor for mass enumeration attempts

---

*IDOR vulnerabilities highlight the critical importance of proper authorization checks and secure access control design in web applications.*