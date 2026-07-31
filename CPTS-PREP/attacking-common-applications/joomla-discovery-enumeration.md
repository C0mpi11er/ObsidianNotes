# 🛰️ Common Security Issues

## Installation Directory Exposure
```bash
# Check if installation directory exists (should be removed)
curl -I http://target.com/installation/

# If accessible, may reveal:
# - Database configuration
# - System information
# - Installation logs
```

## Configuration Backup Files
```bash
# Look for configuration backups
curl -I http://target.com/configuration.php.bak
curl -I http://target.com/configuration.php.old
curl -I http://target.com/configuration.php~
```

## Directory Listing Vulnerabilities
```bash
# Test for directory listing on key folders
directories=(
    "/administrator/components/"
    "/components/"
    "/modules/"
    "/plugins/"
    "/templates/"
    "/images/"
    "/media/"
)

for dir in "${directories[@]}"; do
    curl -s "http://target.com$dir" | grep -q "Index of" && echo "Directory listing: $dir"
done
```

## Component-Specific Vulnerabilities
```bash
# Research component versions for CVEs
curl -s http://target.com/administrator/components/com_admin/admin.xml | grep version
curl -s http://target.com/components/com_content/ | grep -i version

# Cross-reference with CVE databases
# - JVN (Japan Vulnerability Notes)
# - CVE Details
# - Exploit-DB
```

---

## 📈 Intelligence Gathering Workflow

### Comprehensive Enumeration Checklist

#### Phase 1: Initial Discovery
- [ ] **Joomla Confirmation** - Meta tags, robots.txt, directory structure
- [ ] **Version Detection** - README.txt, XML manifests, cache files
- [ ] **Directory Listing** - Check for exposed directories
- [ ] **Admin Panel Location** - Confirm administrator access

#### Phase 2: Component Analysis
- [ ] **Active Template** - Identification and version detection
- [ ] **Component Discovery** - Enumerate installed components
- [ ] **Plugin Enumeration** - System and content plugins
- [ ] **Module Analysis** - Site and administrative modules

#### Phase 3: Vulnerability Research
- [ ] **CVE Mapping** - Map versions to known vulnerabilities
- [ ] **Configuration Review** - Default settings and misconfigurations
- [ ] **Extension Security** - Third-party component vulnerabilities

#### Phase 4: Authentication Assessment
- [ ] **Default Credentials** - Test common username/password combinations
- [ ] **Brute Force Viability** - Assess account lockout policies
- [ ] **Admin Access Methods** - Alternative authentication mechanisms

---

## 📊 Global Joomla Statistics API

### Version Distribution Analysis
```bash
# Query Joomla public statistics API
curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool

# Analyze version distribution for targeting
curl -s https://developer.joomla.org/stats/cms_version | jq '.data.cms_version'

# Example output showing 2.7M+ installations:
{
    "data": {
        "cms_version": {
            "3.5": 13,
            "3.6": 24.29,
            "3.8": 18.84,
            "3.9": 30.28,
            "4.0": 1.52
        },
        "total": 2776276
    }
}
```

### Geographic and Technology Statistics
```bash
# Additional API endpoints for intelligence
curl -s https://developer.joomla.org/stats/php_version | jq .
curl -s https://developer.joomla.org/stats/db_type | jq .
curl -s https://developer.joomla.org/stats/server_os | jq .
```

---

## 🎮 HTB Academy Lab Solutions

### Lab 1: Version Fingerprinting
**Question:** "Fingerprint the Joomla version in use on http://app.inlanefreight.local (Format: x.x.x)"

**Solution Methodology:**
```bash
# Method 1: XML Manifest (Most Accurate)
curl -s http://app.inlanefreight.local/administrator/manifests/files/joomla.xml | grep -oP '<version>\K[^<]+'

# Method 2: README.txt Analysis
curl -s http://app.inlanefreight.local/README.txt | head -n 10 | grep -i version

# Method 3: Cache XML File
curl -s http://app.inlanefreight.local/plugins/system/cache/cache.xml | grep -oP 'version="[^"]*"'

# Method 4: DroopeScan Verification
droopescan scan joomla --url http://app.inlanefreight.local

# Expected format: 3.9.4 (or similar version number)
```

### Lab 2: Admin Password Discovery
**Question:** "Find the password for the admin user on http://app.inlanefreight.local"

**Solution Methodology:**
```bash
# Method 1: Default Credentials Testing
curl -X POST http://app.inlanefreight.local/administrator/index.php \
  -d "username=admin&passwd=admin&task=login" \
  -v

# Method 2: Common Password List
passwords=(
    "admin"
    "password" 
    "123456"
    "password123"
    "admin123"
)

for pass in "${passwords[@]}"; do
    response=$(curl -s -X POST "http://app.inlanefreight.local/administrator/index.php" \
        -d "username=admin&passwd=$pass&task=login")
    
    if [[ $response != *"Username and password do not match"* ]]; then
        echo "Found password: $pass"
        break
    fi
done

# Method 3: Custom Brute Force Script
python3 joomla-brute.py -u http://app.inlanefreight.local \
  -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt \
  -usr admin

# Expected answer: admin (weak default configuration)
```

---

## 📝 Professional Documentation

### Enumeration Findings Template
```
=== Joomla Discovery Report ===

Target: [URL]
Discovery Date: [DATE]

== Core Information ==
Joomla Version: [VERSION]
Template: [TEMPLATE NAME] v[VERSION]
Admin Panel: [URL/administrator/]

== Installed Components ==
[COMPONENT NAME] - [DISCOVERY METHOD]

== System Modules ==
[MODULE NAME] - [STATUS/VERSION]

== Security Findings ==
[HIGH/MEDIUM/LOW] - [VULNERABILITY DESCRIPTION]
Evidence: [SCREENSHOT/REQUEST-RESPONSE]
CVE: [IF APPLICABLE]

== Recommended Actions ==
1. [IMMEDIATE SECURITY UPDATES]
2. [CONFIGURATION IMPROVEMENTS]
3. [MONITORING RECOMMENDATIONS]
```

---

## 🛡️ Defensive Considerations

### Security Hardening Recommendations
```bash
# Essential Joomla security steps
1. Remove /installation/ directory after setup
2. Rename /administrator/ directory to custom path
3. Enable two-factor authentication
4. Implement strong passwords and account policies
5. Regular core and extension updates
6. File permission hardening (644 for files, 755 for directories)
```

### Monitoring and Detection
```bash
# Log locations to monitor
/logs/error.php          # Error logs
/administrator/logs/     # Admin activity logs

# File integrity monitoring
find /administrator -name "*.php" -type f -exec md5sum {} \; > joomla_hashes.txt
```

---

## 🚀 Next Steps

After Joomla enumeration, proceed to:
1. **[Joomla Attacks & Exploitation](joomla-attacks.md)** - Weaponizing discovered vulnerabilities
2. **[Component-Specific Attacks](joomla-component-attacks.md)** - Extension exploitation techniques
3. **[Privilege Escalation](joomla-privilege-escalation.md)** - Administrative access and persistence

**💡 Key Takeaway:** Joomla enumeration requires systematic analysis of version indicators, component discovery, and security configuration assessment. While less common than WordPress, Joomla installations often contain unique vulnerabilities in custom components and configurations that reward thorough enumeration efforts.