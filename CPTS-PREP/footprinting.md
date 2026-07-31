# 🛰️ Cloud Resources Discovery

## 🔍 Overview [!ABSTRACT]

Cloud services (AWS, Azure, GCP) are essential for modern companies but often misconfigured, leading to unauthorized access to sensitive data.

---

### 📂 Common Cloud Storage Types

| Provider | Storage Type | URL Pattern |
|----------|-------------|-------------|
| **AWS**  | S3 Buckets  | `*.amazonaws.com` |
| **Azure** | Blob Storage | `*.blob.core.windows.net` |
| **GCP**   | Cloud Storage | `*.storage.googleapis.com` |

---

## 🔍 Discovery Methods

### 🛠 DNS Enumeration [!INFO]
```bash
# Often cloud storage appears in DNS records
for i in $(cat subdomainlist);do host $i | grep "has address" | grep company.com | cut -d" " -f1,4;done

# Example output showing AWS S3:
blog.company.com 10.129.24.93
company.com 10.129.27.33
matomo.company.com 10.129.127.22
s3-website-us-west-2.amazonaws.com 10.129.95.250  # ← AWS S3 detected
```

### 🌐 Google Dorking for Cloud Storage [!INFO]

#### **AWS S3 Discovery:**
```bash
# Google search queries
intext:"company_name" inurl:amazonaws.com
site:amazonaws.com "company_name"
site:s3.amazonaws.com "company_name"
filetype:pdf site:amazonaws.com "company_name"
```

#### **Azure Blob Discovery:**
```bash
intext:"company_name" inurl:blob.core.windows.net
site:blob.core.windows.net "company_name" 
filetype:pdf site:blob.core.windows.net "company_name"
```

#### **GCP Storage Discovery:**
```bash
intext:"company_name" inurl:storage.googleapis.com
site:storage.googleapis.com "company_name"
```

### 📝 Source Code Analysis [!INFO]

**Check website source for cloud references:**
```html
<!-- DNS prefetch hints in HTML -->
<link rel="dns-prefetch" href="//company.blob.core.windows.net">
<link rel="preconnect" href="https://company.blob.core.windows.net" crossorigin>

<!-- Direct links to cloud resources -->
<img src="https://company-assets.s3.amazonaws.com/logo.png">
<script src="https://company.blob.core.windows.net/js/app.js"></script>
```

---

## 🛠 Specialized Tools

### 🚀 Domain.Glass [!INFO]
```bash
# Website: https://domain.glass/
# Features:
- Infrastructure mapping
- Cloudflare detection
- SSL certificate analysis
- Social media presence
- External tool integration
```

### 🌐 GrayHatWarfare [!INFO]
```bash
# Website: https://grayhatwarfare.com/
# Features:
- AWS S3 bucket enumeration
- Azure blob container search
- GCP storage bucket discovery
- File type filtering
- Content preview
```

**GrayHatWarfare Search Examples:**
```bash
# Search patterns
company_name
company-name
company_abbreviation
companyname

# File type filters
.pdf, .doc, .xlsx, .txt, .zip, .sql, .config
```

### 🚀 Automated Tools [!INFO]
```bash
# CloudEnum
git clone https://github.com/initstring/cloud_enum.git
python3 cloud_enum.py -k company_name

# S3Scanner  
python3 s3scanner.py -l buckets.txt

# AWSBucketDump
python3 AWSBucketDump.py -l buckets.txt
```

---

## 🗄 High-Value Targets [!INFO]

### 🔍 Critical Files to Search For

| File Type  | Examples    | Risk Level |
|------------|-------------|------------|
| **SSH Keys**       | `id_rsa`, `id_rsa.pub`, `.pem`            | 🔴 Critical  |
| **Configurations** | `config.xml`, `.env`, `settings.conf`      | 🔴 Critical  |
| **Database Dumps** | `.sql`, `.db`, `.sqlite`                   | 🔴 Critical  |
| **Source Code**    | `.git`, `.zip`, `.tar.gz`                  | 🟡 High       |
| **Documents**      | `.pdf`, `.docx`, `.xlsx`                   | 🟡 Medium     |
| **Credentials**    | `passwords.txt`, `.htpasswd`               | 🔴 Critical  |

#### **Example: SSH Key Discovery**
```bash
# GrayHatWarfare search results showing leaked SSH keys
Bucket: company-backups.s3.amazonaws.com
Files:
- id_rsa          (1.6KB) - Private SSH key
- id_rsa.pub      (0.4KB) - Public SSH key  
- server_backup.tar.gz (45MB)
```

---

## 🛠 Common Misconfigurations [!INFO]

### **AWS S3 Bucket Issues**
```bash
# Public read access
aws s3 ls s3://company-bucket --no-sign-request

# List bucket contents
aws s3 sync s3://company-bucket . --no-sign-request

# Common bucket naming patterns
company-name
company-backups
company-logs
company-dev
company-prod
company-assets
```

### **Azure Blob Storage**
```bash
# Anonymous access patterns
https://company.blob.core.windows.net/container/file.pdf

# Common container names
backups, logs, assets, documents, uploads, temp
```

---

## 📝 Cloud Resource Workflow [!INFO]

#### **Phase 1: Initial Discovery**

1. **DNS enumeration** - Look for cloud storage references.
2. **Source code analysis** - Check website for cloud links.
3. **Google dorking** - Search for public cloud storage.

#### **Phase 2: Targeted Search**

1. **Company name variations** - Full name, abbreviations, domains.
2. **GrayHatWarfare** - Systematic bucket enumeration.
3. **Domain.glass** - Infrastructure mapping.

#### **Phase 3: Content Analysis**

1. **File enumeration** - List accessible files.
2. **Sensitive data identification** - SSH keys, configs, databases.
3. **Access testing** - Download capabilities.

#### **Phase 4: Exploitation**

1. **SSH key usage** - Access to company servers.
2. **Configuration abuse** - Database access, API keys.
3. **Data exfiltration** - Sensitive document download.

---

## 🛡️ Detection and Prevention [!INFO]

### **Defensive Measures**
- **Bucket policies** - Restrict public access
- **IAM controls** - Least privilege access
- **Monitoring** - Log bucket access
- **Encryption** - Encrypt data at rest
- **Regular audits** - Check for public buckets

### **Detection Methods**
- **Cloud security tools** - AWS Config, Azure Security Center
- **Third-party scanners** - Check for public exposure
- **Certificate monitoring** - Track cloud-related certificates

---

## 🚨 Real-World Impact [!INFO]

### **Common Scenarios**

1. **Employee mistakes** - Accidental public bucket creation.
2. **Legacy configurations** - Old buckets left public.
3. **Development oversight** - Test/dev buckets exposed.
4. **Third-party integrations** - Vendor access misconfigurations.

### **Business Impact**
- **Data breaches** - Customer information exposure.
- **Intellectual property theft** - Source code, documents.
- **Compliance violations** - GDPR, HIPAA penalties.
- **Infrastructure compromise** - SSH key-based access.

---

## 📝 Key Takeaways [!ABSTRACT]

1. **Certificate Transparency** is a goldmine for subdomain discovery
2. **TXT records** reveal extensive third-party integrations
3. **Shodan** provides detailed technical intelligence
4. **SPF records** can leak internal IP addresses
5. **Third-party services** expand attack surface significantly
6. **Cloud resources** are often misconfigured and publicly accessible
7. **Google dorking** is highly effective for cloud storage discovery
8. **SSH keys in cloud storage** provide direct server access

---

## 🔗 References [!INFO]

- HTB Academy: Footprinting Module
- Certificate Transparency: https://crt.sh/
- Shodan: https://www.shodan.io/
- RFC 6962: Certificate Transparency