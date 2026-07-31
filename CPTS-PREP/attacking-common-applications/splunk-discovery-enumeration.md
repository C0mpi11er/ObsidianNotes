```markdown
# 🛰️ Splunk Enumeration Guide

## Introduction & Overview
[!ABSTRACT] This guide systematically identifies and assesses Splunk installations, focusing on version detection, service discovery, and security configuration evaluation.

### Purpose of the Assessment
- **Identify Splunk services**: Discover running instances across networks.
- **Version enumeration**: Gather detailed information about installed versions.
- **Security configuration analysis**: Evaluate authentication mechanisms and default credentials.
- **Sensitive data exposure assessment**: Determine if sensitive information is exposed or accessible via the web interface.

---

## Discovery & Identification

### Service Detection
```bash
# Port scanning for Splunk services
nmap -sV target-range | grep "Splunk"

# Web application detection
http-methods target-range:8000
```

### Version Fingerprinting
```bash
# REST API version checking
curl -Is http://target.com:8089/services/server/info

# Login page banner grabbing
curl -sI http://target.com:8000/en-US/account/login | grep Server

# Authentication requirements check
curl -sv -k "https://target.com:8000/en-US/app/launcher/home" 2>&1 | \
grep '401 Unauthorized'
```

### License Analysis
```bash
# Splunk license type determination
curl -Is http://target.com:8000/services/licenser/check | grep Server

# Free vs Enterprise detection
curl -sI "http://target.com:8089/services/server/info" | \
grep -i 'Splunk Web for'

# Trial license identification
curl -sI "https://target.com:8000/en-US/about" | \
grep -E "Trial|Enterprise"
```

### Authentication Assessment
```bash
# Default credentials testing
for credential in admin:changeme admin:admin; do
    csrf_token=$(curl -s "http://target.com:8000/en-US/account/login" | \
      grep -oP 'name="splunk_form_key" value="\K[^"]+')
    
    response_code=$(curl -s -c cookies.txt \
        -d "username=${credential%:*}&password=${credential#*:}&splunk_form_key=$csrf_token" \
        "http://target.com:8000/en-US/account/login" -w "%{http_code}")
    
    if [ "$response_code" == "302" ]; then
        echo "[!] Default credentials found: $credential"
        break
    fi
done

# Unauthenticated access detection
curl -sI http://target.com:8000/en-US/app/launcher/home | \
grep '401 Unauthorized' || echo "[!] No authentication required"
```

### Splunk Universal Forwarder Identification
```bash
# Universal Forwarder service discovery
nmap -sV target-range | grep "Splunk UF"

# Authentication bypass testing for forwarders
curl -I http://forwarder-ip:8089/services/server/info | \
grep '401 Unauthorized' || echo "[!] No authentication required"
```

### Deployment Server Identification
```bash
# Nmap scan for deployment server service
nmap -sV target-range | grep "Splunk Web for Enterprise"

# Check for deployment server management interface
curl -Is http://deployment-server:8000/services/deployment/server | \
grep 'HTTP/1.1 200 OK'
```

---

## Security Configuration Assessment

### Authentication Mechanism Detection
```bash
# List of supported authentication providers
curl -s "http://target.com:8000/services/authentication/providers" | \
grep -oP '<name>\K[^<]+'

# LDAP configuration detection
curl -s "http://target.com:8000/services/configs/conf-authentication/LDAP" | \
grep -i "host|port"
```

### Security Hardening Evaluation
```bash
# SSL/TLS configuration check
curl -I http://target.com:8000 | grep -E 'Strict-Transport-Security|X-Frame-Options'

# Authentication enforcement verification
response_code=$(curl -s -o /dev/null -w "%{http_code}" "http://target.com:8000/en-US/app/launcher/home")
if [ "$response_code" == "200" ]; then
    echo "[!] No authentication required"
else
    echo "[+] Authentication enforced"
fi

# Default credential test (admin:changeme)
csrf_token=$(curl -s "http://target.com:8000/en-US/account/login" | \
  grep -oP 'name="splunk_form_key" value="\K[^"]+')
  
response_code=$(curl -s -c cookies.txt -d "username=admin&password=changeme&splunk_form_key=$csrf_token" \
"http://target.com:8000/en-US/account/login" -w "%{http_code}")
if [ "$response_code" == "302" ]; then
    echo "[!] Default credentials still active"
else
    echo "[+] Credentials changed"
fi

# Remove temporary cookies file
rm -f cookies.txt
```

---

## Intelligence Gathering Workflow

### Systematic Splunk Assessment

#### Phase 1: Discovery & Identification
- [ ] **Service Detection** - Port scanning and service identification
- [ ] **Version Fingerprinting** - REST API, login pages, headers
- [ ] **License Analysis** - Free vs Enterprise vs Trial detection
- [ ] **Authentication Assessment** - Default credentials, bypass testing

#### Phase 2: Access Control Evaluation
- [ ] **Authentication Bypass** - Unauthenticated access testing
- [ ] **Default Credential Testing** - Common username/password combinations
- [ ] **License Type Analysis** - Free license authentication bypass
- [ ] **API Endpoint Assessment** - REST API access and permissions

#### Phase 3: Data and Configuration Analysis
- [ ] **Index Discovery** - Available data indexes and sources
- [ ] **Application Enumeration** - Installed apps and add-ons
- [ ] **Search Capability Testing** - Data access and query permissions
- [ ] **Configuration Exposure** - Sensitive configuration access

#### Phase 4: Infrastructure Mapping
- [ ] **Deployment Architecture** - Indexers, forwarders, deployment servers
- [ ] **Universal Forwarder Discovery** - Connected endpoints and agents
- [ ] **Cluster Analysis** - Multi-node deployments and replication
- [ ] **Integration Assessment** - External system connections and data sources

---

## Risk Assessment Framework

### Splunk Security Priorities

#### Critical Findings
```bash
# Immediate security concerns to identify:
critical_checks=(
    "Unauthenticated access to Splunk instance"
    "Default credentials (admin:changeme)"
    "Splunk Free license with no authentication"
    "Exposed configuration endpoints"
    "Sensitive data in search indexes"
    "Unrestricted Universal Forwarder deployment"
    "Administrative API access without authentication"
    "Deployment server compromise potential"
)

# Risk assessment automation
for check in "${critical_checks[@]}"; do
    case "$check" in
        "Unauthenticated access to Splunk instance")
            curl -s "http://target.com:8000/en-US/app/launcher/home" | \
              grep -q "Splunk" && echo "[!] CRITICAL: $check"
            ;;
        "Default credentials (admin:changeme)")
            # Test would be implemented with credential testing function
            echo "[+] Testing: $check"
            ;;
    esac
done
```

#### Data Sensitivity Analysis
```bash
# Assess the sensitivity of data accessible through Splunk
data_sensitivity_analysis() {
    local base_url=$1
    
    echo "[+] Analyzing data sensitivity in Splunk indexes:"
    
    # High-sensitivity data patterns
    sensitive_searches=(
        "eventtype=authentication"     # Authentication logs
        "sourcetype=*windows*"         # Windows event logs
        "source=*security*"            # Security logs
        "source=*audit*"               # Audit trails
        "password OR credential"       # Credential exposure
        "ssn OR social_security"       # PII data
        "credit_card OR payment"       # Financial data
        "email OR communication"       # Communication logs
    )
    
    for search in "${sensitive_searches[@]}"; do
        echo "[+] Checking for: $search"
        # Implementation would test search capabilities
    done
}

# data_sensitivity_analysis "http://target.com:8000"
```

---

## Next Steps

After Splunk enumeration, proceed to:
1. **[Splunk Attacks & Exploitation](splunk-attacks.md)** - Custom application RCE and data exfiltration
2. **[PRTG Network Monitor Discovery](prtg-discovery.md)** - Infrastructure monitoring reconnaissance
3. **[SIEM Security Assessment](siem-security.md)** - Advanced log analytics exploitation

**💡 Key Takeaway:** Splunk enumeration focuses on **SIEM infrastructure reconnaissance**, **authentication bypass discovery**, and **sensitive data access evaluation**. Enterprise environments frequently contain **Splunk instances with weak authentication** or **Free license configurations**, making systematic enumeration crucial for identifying **high-value data repositories** and **privileged system access**.

**📊 Professional Impact:** Splunk compromises provide access to **comprehensive organizational logs**, **security monitoring data**, and **business intelligence**, often with **SYSTEM/root privileges** and **lateral movement opportunities** through **Universal Forwarder networks**.
```