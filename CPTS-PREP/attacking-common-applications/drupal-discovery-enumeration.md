# 🛰️ Drupal Enumeration & Exploitation

## 📄 Introduction to Drupal Versioning

### 🍂 Initial Discovery and Version Detection

#### 🧐 Drupal Confirmation and Version Detection
```bash
# Verify if the target is a Drupal installation
curl -s http://drupal-qa.inlanefreight.local/ | grep "Drupal"

# Detect the exact version number using CHANGELOG.txt or README.txt files
curl -s http://drupal-qa.inlanefreight.local/CHANGELOG.txt | grep -m1 "Drupal" | awk '{print $2}' | tr -d ','
```

## 🔍 Content and Module Analysis

### 📑 Node Enumeration

#### 🛠️ Sequential Content Discovery
```bash
# Enumerate nodes to understand content structure
for i in {1..50}; do curl -s http://drupal-qa.inlanefreight.local/node/$i; done
```

### 🏷️ Module and Theme Discovery

#### 🔍 Identify Active Modules
```bash
# List active modules by analyzing the configuration directory
curl -s http://drupal-qa.inlanefreight.local/admin/modules | grep "name=" | awk -F'\"' '{print $4}'
```

### 📄 Configuration and Security Assessment

#### 🔍 Security Header Analysis
```bash
# Check security headers to identify protection mechanisms in place
curl -sI http://drupal-qa.inlanefreight.local/ | grep "X-Frame-Options"
curl -sI http://drupal-qa.inlanefreight.local/ | grep "Content-Security-Policy"
```

## 🛡️ Security Hardening Recommendations

### 🚫 Core Security Measures
```bash
# Disable PHP module and remove development modules in production
rm /var/www/drupal/modules/php/php.module
rm -rf /var/www/drupal/sites/all/modules/devel/

# Block access to sensitive files via .htaccess
echo "<Files \"*.info\">" > /var/www/drupal/.htaccess
echo "  Order deny,allow" >> /var/www/drupal/.htaccess
echo "  Deny from all" >> /var/www/drupal/.htaccess
echo "</Files>" >> /var/www/drupal/.htaccess
```

### 🛠️ File System Hardening
```bash
# Set proper file permissions for security
find /var/www/drupal/ -type f -exec chmod 644 {} \;
find /var/www/drupal/ -type d -exec chmod 755 {} \;

# Restrict access to configuration files
chmod 444 /var/www/drupal/sites/default/settings.php
```

## 🕵️‍♂️ Attack Pattern Recognition and Monitoring

### 🔎 Monitor for Common Attack Patterns
```bash
# Log analysis for common attack patterns
tail -f /var/log/apache2/access.log | grep -E "(CHANGELOG|node/[0-9]+|admin|settings\.php)"
```

## 📚 Cross-CMS Integration

### 🛠️ CMS Fingerprinting Automation
```bash
#!/bin/bash
# cms-identify.sh

target="$1"

echo "[+] CMS Identification for $target"

# WordPress detection
if curl -s "$target/wp-admin/" | grep -q "WordPress"; then
    echo "[+] WordPress detected"
fi

# Joomla detection  
if curl -s "$target/" | grep -qi "joomla"; then
    echo "[+] Joomla detected"
fi

# Drupal detection
if curl -s "$target/" | grep -qi "drupal"; then
    echo "[+] Drupal detected"
    version=$(curl -s "$target/CHANGELOG.txt" | grep -m1 "Drupal" 2>/dev/null)
    echo "    Version: $version"
fi
```

## 📝 Conclusion

### 💡 Key Takeaways

**Drupal enumeration requires understanding the node-based content system, version-specific file locations, and module architecture. While less common than WordPress, Drupal installations often power critical enterprise and government infrastructure, making thorough enumeration essential for comprehensive security assessments.**

---

# 🔎 Next Steps
After Drupal enumeration, proceed to:
1. **[[Drupal Attacks & Exploitation]]** - Drupalgeddon and module vulnerabilities.
2. **[[Servlet Containers]]** - Java application attacks.
3. **[[Development Tools]]** - CI/CD and build system attacks.

[!NOTE] Ensure strict adherence to the formatting rules provided.