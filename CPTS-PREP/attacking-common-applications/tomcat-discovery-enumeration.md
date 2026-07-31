# 🛰️ Tomcat Enumeration

## Introduction to Tomcat Service Discovery

### Objective: Identify and Enumerate Tomcat Services on Target Hosts

#### Step 1: Initial Connectivity Check
```bash
# Add target host entry in /etc/hosts
echo "10.129.201.58 web01.inlanefreight.local" >> /etc/hosts

# Verify connectivity to the Tomcat service running on port 8180
curl -I http://web01.inlanefreight.local:8180/
```

### Step 2: Version Detection Methods

#### Method 1: Error Page Analysis (Most Reliable)
```bash
# Example command to detect version from error page
curl -s http://web01.inlanefreight.local:8180/invalid | grep -i tomcat
```

#### Method 2: Documentation Page Analysis
```bash
# Example command to check documentation page for version information
curl -s http://web01.inlanefreight.local:8180/docs/ | grep -i tomcat
```

#### Method 3: Server Header Analysis
```bash
# Example command to parse server header for version details
curl -I http://web01.inlanefreight.local:8180/ | grep -i server
```

#### Method 4: Examples Application
```bash
# Example command to extract version information from examples app
curl -s http://web01.inlanefreight.local:8180/examples/ | grep -i version
```

### Step 3: Extract Expected Answer
```bash
# Version format expected: X.X.X (e.g., 10.0.10)
# Primary method - error page:
curl -s http://web01.inlanefreight.local:8180/invalid | \
  grep -oP 'Apache Tomcat/\K[0-9.]+'

# Alternative - docs page:
curl -s http://web01.inlanefreight.local:8180/docs/ | \
  grep -oP 'Apache Tomcat [0-9.]+ \(\K[0-9.]+'

# HTB Answer: 10.0.10
```

## Lab 2: Admin User Role Analysis

### Question: What role does the admin user have in the configuration example?

#### Step 1: Configuration File Analysis
```bash
# Example tomcat-users.xml content:
<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />

<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui" />
```

#### Step 2: Role Analysis
```bash
# Admin user roles breakdown:
# username="admin" has roles="manager-gui,admin-gui"

# Specific question asks for "the role" (singular)
# Primary/distinctive role for admin user: admin-gui

# HTB Answer: admin-gui
```

#### Step 3: Role Functionality Understanding
```bash
# Role hierarchy and capabilities:
# manager-gui - Tomcat Manager HTML interface
# admin-gui   - Host Manager interface (admin-specific)
# manager-script - API access for automation
# manager-jmx    - JMX monitoring access
# manager-status - Status pages only

# The admin-gui role provides host-manager access (virtual host management).
```

---

## Intelligence Gathering Workflow

### Systematic Tomcat Assessment

#### Phase 1: Discovery & Fingerprinting
- [ ] **Service Detection** - Port scanning and service identification
- [ ] **Version Fingerprinting** - Error pages, documentation, headers
- [ ] **Application Discovery** - Default apps, custom deployments
- [ ] **Directory Enumeration** - Hidden paths and administrative interfaces

#### Phase 2: Administrative Interface Assessment
- [ ] **Manager Application Access** - /manager, /host-manager testing
- [ ] **Default Credential Testing** - Common username/password combinations
- [ ] **Authentication Mechanism Analysis** - HTTP Basic, Form-based, LDAP
- [ ] **Role and Permission Mapping** - User capabilities and access levels

#### Phase 3: Application Analysis
- [ ] **WAR File Discovery** - Deployed applications identification
- [ ] **JSP Enumeration** - Server-side page discovery
- [ ] **Configuration File Access** - web.xml, tomcat-users.xml analysis
- [ ] **Framework Identification** - Spring, Struts, custom applications

#### Phase 4: Vulnerability Research
- [ ] **Version-Specific CVEs** - Known vulnerability research
- [ ] **Configuration Weaknesses** - Security misconfigurations
- [ ] **Application Vulnerabilities** - Custom code analysis
- [ ] **Privilege Escalation Vectors** - Manager interface abuse

---

## Enterprise Deployment Patterns

### Internal Network Reconnaissance

#### Multi-Instance Discovery
```bash
# Tomcat commonly runs on multiple ports in enterprise environments:
common_ports=(8080 8443 8009 8180 8280 8380 8480 8580 8680 8780 8880 8980)

for port in "${common_ports[@]}"; do
    echo "Testing port $port:"
    curl -I "http://target.com:$port/" 2>/dev/null | head -n 1
done

# AJP connector detection (port 8009):
nmap -sV -p 8009 target.com
```

#### Load Balancer Detection
```bash
# Identify load-balanced Tomcat instances:
curl -I http://target.com:8080/ | grep -i "x-forwarded\|load-balancer\|cluster"

# Session affinity testing:
curl -c cookies.txt http://target.com:8080/
curl -b cookies.txt http://target.com:8080/ | grep -i jsessionid
```

### Development vs Production Discrimination

#### Environment Identification
```bash
# Look for development/staging indicators:
dev_indicators=(
    "dev"
    "test"
    "staging"  
    "uat"
    "debug"
    "localhost"
)

for indicator in "${dev_indicators[@]}"; do
    curl -s http://target.com:8080/ | grep -i "$indicator" && \
      echo "Development indicator found: $indicator"
done

# Check for debug/development features:
curl -s http://target.com:8080/examples/ | grep -i "example\|test\|debug"
```

---

## Security Assessment Priorities

### High-Value Target Identification

#### EyeWitness Integration
```bash
# Tomcat typically appears first in EyeWitness "High Value Targets"
# Automated screenshot and service identification:
eyewitness --web -f tomcat_targets.txt

# Generate target list for EyeWitness:
cat > tomcat_targets.txt << 'EOF'
http://target1.com:8080
http://target2.com:8180
https://target3.com:8443
EOF
```

#### Risk Prioritization
```bash
# High-risk Tomcat configurations:
1. Default credentials (tomcat:tomcat, admin:admin)
2. Exposed manager applications (/manager, /host-manager)
3. Directory listing enabled on /webapps
4. Outdated versions with known CVEs
5. Development features in production
6. Weak authentication mechanisms
7. Excessive user privileges (admin-gui roles)
```

---

## Next Steps

After Tomcat enumeration:
1. **[[Tomcat Attacks & Exploitation]]** - WAR file uploads and manager abuse
2. **[[Java Application Security]]** - Servlet and JSP vulnerabilities
3. **[[Jenkins Discovery]]** - CI/CD infrastructure enumeration

**💡 Key Takeaway:** Tomcat enumeration focuses on **administrative interface discovery**, **version identification**, and **configuration analysis**. Enterprise environments frequently contain **multiple Tomcat instances** with **weak default credentials**, making systematic enumeration crucial for identifying **high-value attack vectors** and **internal network footholds**.
---