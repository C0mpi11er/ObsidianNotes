# 🛰️ Jenkins Enumeration Guide

## Initial Setup

### Hosts File Configuration & Jenkins Accessibility Verification

```bash
echo "TARGET_IP jenkins.inlanefreight.local" >> /etc/hosts

# Verify Jenkins accessibility
curl -I http://jenkins.inlanefreight.local:8000/
```

## Authentication Process

### Logging In with Default Credentials

```bash
# Login with provided credentials: admin:admin
curl -c cookies.txt -d "j_username=admin&j_password=admin" \
  http://jenkins.inlanefreight.local:8000/j_security_check

# Verify authentication success
curl -b cookies.txt http://jenkins.inlanefreight.local:8000/manage | grep -q "Manage Jenkins"
```

## Version Detection Methods

### Method 1: Login Page Footer Analysis

```bash
# Method 1: Login page footer analysis
curl -s http://jenkins.inlanefreight.local:8000/login | \
  grep -oP 'Jenkins ver\. \K[0-9.]+'
```

### Method 2: Management Interface

```bash
# Method 2: Management interface
curl -b cookies.txt http://jenkins.inlanefreight.local:8000/manage | \
  grep -oP 'Jenkins \K[0-9.]+'
```

### Method 3: API Endpoint (Authenticated)

```bash
# Method 3: API endpoint (authenticated)
curl -b cookies.txt http://jenkins.inlanefreight.local:8000/api/json | \
  jq -r '.version'
```

### Method 4: System Information Page

```bash
# Method 4: System information page
curl -b cookies.txt http://jenkins.inlanefreight.local:8000/systemInfo | \
  grep -i "Jenkins version"
```

### Method 5: Browser-Based Approach

- **Navigate to:** `http://jenkins.inlanefreight.local:8000/`
- **Login with:** admin / admin
- **Check footer or go to Manage Jenkins -> System Information**

## Version Verification

```bash
# HTB Academy expected version: 2.303.1
# This version can typically be found in:
# 1. Login page footer: "Jenkins ver. 2.303.1"
# 2. Manage Jenkins page header
# 3. System Information under "Jenkins version"
# 4. API response in version field

# HTB Answer: 2.303.1
```

---

## Enterprise Deployment Patterns

### Internal Network Recognition

#### CI/CD Infrastructure Mapping

```bash
# Jenkins in enterprise environments commonly found:
# 1. Development networks (internal CI/CD)
# 2. Build servers (dedicated infrastructure)
# 3. Integration environments (staging/testing)
# 4. Cloud deployments (AWS/Azure/GCP)

# Network reconnaissance for Jenkins clusters
nmap -sS -p 8080,8443,5000 10.10.0.0/16 | grep -B 2 -A 2 "8080/tcp.*open"

# Jenkins master-agent architecture detection
nmap -sV -p 5000 target-range | grep -i "jenkins\|jnlp"

# Load balancer detection
curl -I http://jenkins.internal.com:8080/ | grep -i "x-forwarded\|load-balancer"
```

#### Development Tool Integration

```bash
# Common Jenkins integrations to identify:
integration_indicators=(
    "git"                  # Source control
    "docker"              # Containerization
    "kubernetes"          # Orchestration
    "aws"                 # Cloud deployment
    "ansible"             # Configuration management
    "terraform"           # Infrastructure as code
    "sonarqube"           # Code quality
    "nexus"               # Artifact repository
)

echo "[+] Checking for development tool integrations:"
for tool in "${integration_indicators[@]}"; do
    curl -s -b cookies.txt "http://jenkins.inlanefreight.local:8000/configure" | \
      grep -qi "$tool" && echo "[+] $tool integration detected"
done
```

### Security Configuration Analysis

#### Authentication Method Assessment

```bash
# Security realm analysis
curl -s -b cookies.txt "http://jenkins.inlanefreight.local:8000/configureSecurity/" | \
  grep -A 5 -B 5 "securityRealm"

# Authorization strategy detection
curl -s -b cookies.txt "http://jenkins.inlanefreight.local:8000/configureSecurity/" | \
  grep -A 10 "authorizationStrategy"

# Anonymous access configuration
curl -s "http://jenkins.inlanefreight.local:8000/configureSecurity/" | \
  grep -i "anonymous" && echo "[!] Anonymous access may be enabled"

# CSRF protection status
curl -s -b cookies.txt "http://jenkins.inlanefreight.local:8000/configureSecurity/" | \
  grep -i "csrf" && echo "[+] CSRF protection configured"
```

---

## Intelligence Gathering Workflow

### Systematic Jenkins Assessment

#### Phase 1: Discovery & Identification
- [ ] **Service Detection** - Port scanning and service identification
- [ ] **Version Fingerprinting** - Login pages, API endpoints, headers
- [ ] **Authentication Analysis** - Default credentials, anonymous access
- [ ] **Plugin Enumeration** - Security plugins and extensions

#### Phase 2: Access Control Assessment
- [ ] **Authentication Bypass** - Anonymous access testing
- [ ] **Default Credential Testing** - Common username/password combinations
- [ ] **Authorization Analysis** - Permission matrix and role evaluation
- [ ] **Session Management** - Cookie analysis and session security

#### Phase 3: Build System Analysis
- [ ] **Job Configuration** - Build processes and trigger mechanisms
- [ ] **Pipeline Security** - Groovy script analysis and command execution
- [ ] **Artifact Assessment** - Build outputs and sensitive data exposure
- [ ] **Credential Discovery** - Hardcoded secrets and credential stores

#### Phase 4: Infrastructure Mapping
- [ ] **Agent Discovery** - Build slave identification and configuration
- [ ] **Integration Analysis** - Third-party tool connections and APIs
- [ ] **Network Architecture** - Master-agent communication and clustering
- [ ] **Supply Chain Assessment** - Deployment targets and production impact

---

## Risk Assessment Framework

### Jenkins Security Priorities

#### Critical Findings

```bash
# Immediate security concerns to identify:
critical_checks=(
    "Anonymous Script Console access"
    "Anonymous administrative access"
    "Default credentials (admin:admin)"
    "Exposed credential stores"
    "Unauthenticated build triggering"
    "Pipeline privilege escalation"
    "Hardcoded secrets in job configs"
    "Unrestricted agent registration"
)

# Risk assessment automation
for check in "${critical_checks[@]}"; do
    case "$check" in
        "Anonymous Script Console access")
            curl -s "http://jenkins.inlanefreight.local:8000/script" | \
              grep -q "Script Console" && echo "[!] CRITICAL: $check"
            ;;
        "Anonymous administrative access")
            curl -s "http://jenkins.inlanefreight.local:8000/manage" | \
              grep -q "Manage Jenkins" && echo "[!] CRITICAL: $check"
            ;;
        # Add other checks as needed
    esac
done
```

---

## Next Steps

After Jenkins enumeration, proceed to:
1. **[Jenkins Attacks & Exploitation](jenkins-attacks.md)** - Script Console abuse and RCE
2. **[CI/CD Pipeline Security](cicd-security.md)** - Build process manipulation
3. **[GitLab Discovery](gitlab-discovery.md)** - Source code management reconnaissance

**💡 Key Takeaway:** Jenkins enumeration focuses on **CI/CD infrastructure reconnaissance**, **authentication bypass discovery**, and **build system analysis**. Enterprise environments frequently contain **Jenkins instances with weak security configurations**, making systematic enumeration crucial for identifying **development infrastructure attack vectors** and **supply chain compromise opportunities**.

---