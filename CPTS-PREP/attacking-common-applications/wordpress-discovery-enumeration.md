# 🛰️ WordPress Detection Strategies

## Core WordPress Version

### 🕵️‍♂️ Detecting WordPress Core Version

```bash
# 1. Meta generator tag
curl -s http://target.com | grep generator

# 2. RSS feed generator
curl -s http://target.com/?feed=rss2 | grep generator

# 3. readme.html file
curl -s http://target.com/readme.html | grep Version

# 4. Version parameter in scripts/styles
curl -s http://target.com | grep -oP 'ver=\K[0-9.]+'
```

## Plugin/Theme Versioning

### 🕵️‍♂️ Detecting Plugin and Theme Versions

```bash
# 1. readme.txt files
curl -s http://target.com/wp-content/plugins/PLUGIN/readme.txt | grep "Stable tag"

# 2. CSS/JS version parameters  
curl -s http://target.com | grep -oP 'plugin-name.*?ver=\K[0-9.]+'

# 3. Plugin headers in PHP files
curl -s http://target.com/wp-content/plugins/PLUGIN/plugin-file.php | grep "Version:"
```

---

## Intelligence Gathering Workflow

### 📋 Comprehensive Enumeration Checklist

#### Phase 1: Initial Discovery
- [ ] **WordPress Confirmation** - robots.txt, directory structure, meta tags
- [ ] **Version Detection** - Core version identification
- [ ] **Directory Listing** - Check for exposed directories
- [ ] **XML-RPC Status** - Test availability and functionality

#### Phase 2: Component Analysis  
- [ ] **Active Theme** - Identification and version detection
- [ ] **Plugin Discovery** - Enumerate installed plugins
- [ ] **Plugin Versions** - Specific version identification
- [ ] **User Enumeration** - Valid username discovery

#### Phase 3: Vulnerability Mapping
- [ ] **CVE Research** - Map versions to known vulnerabilities
- [ ] **Configuration Issues** - Default credentials, exposed files
- [ ] **Custom Code Review** - Theme/plugin custom functionality

#### Phase 4: Attack Surface Assessment
- [ ] **Entry Points** - Login forms, comment sections, contact forms
- [ ] **File Upload** - Media upload functionality
- [ ] **Administrative Access** - wp-admin accessibility
- [ ] **API Endpoints** - REST API and XML-RPC availability

---

## Common Vulnerability Patterns

### 🔥 High-Priority Findings

#### Outdated Core Installation
```bash
# Impact: Multiple CVEs affecting core functionality
# Detection: Version comparison with latest releases
# Risk: High - Core vulnerabilities often lead to RCE
```

#### Vulnerable Plugins
```bash
# Most Common: 
# - Contact Form 7 (various versions)
# - wpDiscuz (RCE vulnerabilities)
# - mail-masta (LFI vulnerabilities)
# - File Manager plugins (arbitrary file access)

# Detection Strategy:
# 1. Enumerate all plugins
# 2. Identify exact versions
# 3. Cross-reference with vulnerability databases
```

#### Default/Weak Credentials
```bash
# Common credentials to test:
admin:admin
admin:password
admin:123456
wordpress:wordpress

# Test against wp-login.php and wp-admin access
```

#### Directory Listing Enabled
```bash
# Check critical directories:
/wp-content/plugins/     # Plugin source code exposure
/wp-content/uploads/     # Uploaded file enumeration  
/wp-content/themes/      # Theme file access
```

---

## Example Discovery Session

### Target: blog.inlanefreight.local

#### Step 1: Initial Fingerprinting
```bash
# Confirm WordPress installation
curl -s http://blog.inlanefreight.local/robots.txt
# Output shows /wp-admin/ and /wp-content/ directories

# Check version
curl -s http://blog.inlanefreight.local | grep generator
# Output: <meta name="generator" content="WordPress 5.8" />
```

#### Step 2: Theme & Plugin Discovery
```bash
# Identify theme
curl -s http://blog.inlanefreight.local/ | grep themes
# Output: /wp-content/themes/business-gravity/

# Find plugins
curl -s http://blog.inlanefreight.local/ | grep plugins
# Output: contact-form-7, mail-masta, wpDiscuz plugins detected
```

#### Step 3: User Enumeration
```bash
# Test login error messages
curl -X POST http://blog.inlanefreight.local/wp-login.php -d "log=admin&pwd=test"
# Output: "The password for username admin is incorrect."
# Confirms 'admin' is a valid user
```

#### Step 4: Automated Validation
```bash
# WPScan confirmation
wpscan --url http://blog.inlanefreight.local --enumerate --api-token TOKEN
# Confirms findings and identifies additional vulnerabilities
```

---

## Professional Documentation

### 📄 Enumeration Findings Template
```
=== WordPress Discovery Report ===

Target: [URL]
Discovery Date: [DATE]

== Core Information ==
WordPress Version: [VERSION]
Theme: [THEME NAME] v[VERSION]
XML-RPC Status: [ENABLED/DISABLED]

== User Accounts ==
[USERNAME] - [ROLE] - [DISCOVERY METHOD]

== Installed Plugins ==
[PLUGIN NAME] v[VERSION] - [VULNERABILITY STATUS]

== Security Findings ==
[HIGH/MEDIUM/LOW] - [VULNERABILITY DESCRIPTION]
Evidence: [SCREENSHOT/REQUEST-RESPONSE]
CVE: [IF APPLICABLE]

== Recommended Actions ==
1. [IMMEDIATE ACTIONS]
2. [SECURITY IMPROVEMENTS]
3. [MONITORING RECOMMENDATIONS]
```

---

## HTB Academy Lab Solutions

### 🧑‍💻 Lab Questions

#### Q1: Find flag.txt in accessible directory
**Solution Methodology:**
```bash
# Test directory listing on common paths
curl -s http://blog.inlanefreight.local/wp-content/uploads/
curl -s http://blog.inlanefreight.local/wp-content/plugins/
curl -s http://blog.inlanefreight.local/wp-content/themes/

# Look for exposed files in plugin directories
curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/
# Check for flag.txt in exposed directories
```

#### Q2: Discover additional plugin (manual enumeration)
**Solution Methodology:**
```bash
# Analyze different pages for plugin references
curl -s http://blog.inlanefreight.local/?p=1 | grep plugins
curl -s http://blog.inlanefreight.local/category/news/ | grep plugins

# Check page source on multiple URLs:
# - Homepage
# - Individual posts (?p=1, ?p=2)
# - Category pages
# - Archive pages

# Look for plugin CSS/JS files not found in initial scan
```

#### Q3: Find plugin version number
**Solution Methodology:**
```bash
# Check plugin directory for version files
curl -s http://blog.inlanefreight.local/wp-content/plugins/[PLUGIN]/readme.txt
curl -s http://blog.inlanefreight.local/wp-content/plugins/[PLUGIN]/style.css | grep Version
curl -s http://blog.inlanefreight.local/wp-content/plugins/[PLUGIN]/ | grep -i version

# Look for version in plugin CSS/JS URLs
curl -s http://blog.inlanefreight.local/?p=1 | grep -oP 'plugin-name.*?ver=\K[0-9.]+'
```

---

## Next Steps

After completing enumeration, proceed to:
1. **[[WordPress Attacks & Exploitation]]** - Weaponizing discovered vulnerabilities
2. **[[Privilege Escalation]]** - Gaining administrative access
3. **[[Post-Exploitation]]** - Maintaining access and lateral movement

**💡 Key Takeaway:** Thorough enumeration is critical for WordPress assessments. Manual techniques often discover vulnerabilities missed by automated scanners, while automated tools validate and expand manual findings.
---