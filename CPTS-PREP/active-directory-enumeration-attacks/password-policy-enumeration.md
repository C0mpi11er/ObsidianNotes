# 🛰️ Password Policy Enumeration in Active Directory

## 🔍 Overview

Understanding a domain's password policy is crucial for both offensive and defensive cybersecurity operations. This guide provides detailed steps to enumerate password policies in an Active Directory environment, specifically focusing on the INLANEFREIGHT.LOCAL lab within HTB Academy.

---

## 🚀 Pre-Requisites & Tools

### 🔧 Tools
- `enum4linux`: Enumerate AD information via NULL session.
- `rpcclient`: Connect and query AD using RPC over SMB.
- `crackmapexec` (`cme`): Automated tool for password policy enumeration.
- `ldapsearch`: Query LDAP directories for security settings.

### 🌐 Methodology
1. **Connect to Target System**
2. **Enumerate Password Policy Using NULL Session**
3. **Verify with CrackMapExec & rpcclient**
4. **Analyze & Document Results**

---

## 📈 Enumeration Commands

### 💻 Connect to the Target System

```bash
# SSH to target system
ssh htb-student@TARGET_IP
# Password: HTB_@cademy_stdnt!
```

### 🔍 Enumerate Using `enum4linux`
```bash
# Enumerate password policy via NULL session
enum4linux -P 172.16.5.5
```

### 🔧 Query with `rpcclient` & `ldapsearch`
```bash
# Connect to target using rpcclient
rpcclient -U "" -N 78.193.76.101

# Check password policy details
rpcclient $> getdompwinfo

# Use ldapsearch for additional attributes
ldapsearch -h 78.193.76.101 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep minPwdLength
```

### 🛠️ Verify with `crackmapexec`
```bash
# Use crackmapexec to get password policy details
crackmapexec smb 78.193.76.101 --pass-pol
```

---

## 🔍 Password Policy Analysis

### 🔍 **INLANEFREIGHT.LOCAL Analysis**
| **Setting** | **Value** | **Implication** |
|-------------|-----------|-----------------|
| **Minimum Length** | 8 characters | Allows weak passwords like `Welcome1` |
| **Lockout Threshold** | 5 attempts | Safe for 2-3 password spraying attempts |
| **Lockout Duration** | 30 minutes | Accounts auto-unlock (no admin required) |
| **Password Complexity** | Enabled | Requires at least 3/4 character types |
| **Password History** | 24 passwords | Prevents immediate reuse |
| **Maximum Age** | Unlimited | Passwords never expire |

### ⚠️ **Password Spraying Implications**
- **Safe Attempt Count**: 2-3 attempts per user
- **Wait Time**: 31+ minutes between spray rounds
- **Target Passwords**: `Welcome1`, `Password1`, `Company1`
- **Risk Level**: Low (auto-unlock, high threshold)

---

## 📋 Default Domain Policy

| **Policy** | **Default Value** |
|------------|-------------------|
| Enforce password history | 24 days |
| Maximum password age | 42 days |
| Minimum password age | 1 day |
| **Minimum password length** | **7** |
| Password complexity | Enabled |
| Store passwords using reversible encryption | Disabled |
| Account lockout duration | Not set |
| Account lockout threshold | 0 |
| Reset account lockout counter | Not set |

### 📊 Comparison & Analysis
- **INLANEFREIGHT.LOCAL Policy**: MinPwdLength:8, LockOutDuration:30 min, LockOutThreshold:5
- **Default Policy**: MinPwdLength:7, No specific lockout settings

---

## 🎯 HTB Academy Lab Walkthrough

### 📝 Lab Questions

#### **Question 1**: *"What is the default Minimum password length when a new domain is created?"*
#### **Question 2**: *"What is the minPwdLength set to in the INLANEFREIGHT.LOCAL domain?"*

### 🚀 Step-by-Step Solution

#### 1️⃣ **Connect to Target**
```bash
# SSH to target system
ssh htb-student@TARGET_IP
# Password: HTB_@cademy_stdnt!
```

#### 2️⃣ **Method 1: enum4linux**
```bash
# Enumerate password policy
enum4linux -P TARGET_IP
```

#### 3️⃣ **Method 2: rpcclient NULL Session**
```bash
# Connect with NULL session
rpcclient -U "" -N TARGET_IP

# Query password policy
rpcclient $> getdompwinfo
min_password_length: 8
password_properties: 0x00000001
	DOMAIN_PASSWORD_COMPLEX
```

#### 4️⃣ **Method 3: ldapsearch**
```bash
# Query via LDAP
ldapsearch -h TARGET_IP -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep minPwdLength
minPwdLength: 8
```

#### 5️⃣ **Method 4: enum4linux-ng**
```bash
# Modern tool with structured output
enum4linux-ng -P TARGET_IP -oA ilfreight

# Check JSON output
cat ilfreight.json | grep -A5 "domain_password_information"
```

### ✅ **Answers**
1. **Default minimum password length**: `7`
2. **INLANEFREIGHT.LOCAL minPwdLength**: `8`

---

## 🛡️ Password Policy Best Practices

### ✅ **Strong Policy Recommendations**
- **Minimum Length**: 12-14 characters
- **Lockout Threshold**: 3-5 attempts
- **Lockout Duration**: 15-30 minutes
- **Complexity**: Enable but educate users
- **Password Age**: 90-180 days maximum

### 🚫 **Disable Legacy Features**
- **SMB NULL Sessions**: Prevent anonymous access
- **LDAP Anonymous Bind**: Require authentication
- **LM Hash Storage**: Use only NTLM/NTLMv2
- **Reversible Encryption**: Never enable

### 🔧 **Group Policy Hardening**
```
Computer Configuration → Windows Settings → Security Settings → Account Policies → Password Policy
- Minimum password length: 12
- Password complexity requirements: Enabled
- Minimum password age: 1 day
- Maximum password age: 90 days
- Password history: 24 passwords
```

---

## 🔍 Detection & Monitoring

### 📊 **Event IDs to Monitor**
- **4625**: Failed logon attempts
- **4740**: Account lockout events
- **4767**: Account unlock events
- **4724**: Password reset attempts

### 🚨 **Anomaly Detection**
- **Multiple failed authentications** from single source
- **Unusual authentication patterns** across multiple accounts
- **Service account lockouts** (often indicates spraying)
- **Authentication attempts** outside business hours

### 📈 **Baseline Metrics**
- Normal failed authentication rates
- Typical lockout frequencies
- Service account authentication patterns
- Geographic authentication patterns

---

## ⚡ Quick Reference Commands

### 🐧 **Linux Enumeration**
```bash
# CrackMapExec with credentials
crackmapexec smb TARGET -u USER -p PASS --pass-pol

# rpcclient NULL session
rpcclient -U "" -N TARGET
rpcclient $> getdompwinfo

# enum4linux-ng modern
enum4linux-ng -P TARGET -oA output

# LDAP anonymous bind
ldapsearch -h TARGET -x -b "DC=DOMAIN,DC=LOCAL" -s sub "*" | grep -A10 -B10 pwdHistoryLength
```

### 🪟 **Windows Enumeration**
```cmd
REM Built-in Windows command
net accounts

REM NULL session test
net use \\DC\ipc$ "" /u:""
```

```powershell
# PowerView
Import-Module .\PowerView.ps1
Get-DomainPolicy
```

---

## 🔑 Key Takeaways

### ✅ **Enumeration Success Factors**
- **Multiple Methods**: Try various approaches (SMB, LDAP, RPC)
- **Legacy Misconfigurations**: NULL sessions often work on older domains
- **Tool Redundancy**: Use both traditional and modern tools
- **Credential Context**: Some methods require authentication

### ⚠️ **Critical Considerations**
- **Lockout Avoidance**: Never exceed safe attempt thresholds
- **Stealth Operations**: Avoid generating excessive authentication logs
- **Policy Documentation**: Record all discovered settings for planning
- **Client Communication**: Confirm lockout policies when possible

### 🎯 **Next Steps**
1. **User Enumeration**: Gather target user lists
2. **Password List Creation**: Build spraying wordlists
3. **Attack Timing**: Plan spray intervals based on lockout policy
4. **Monitoring Setup**: Watch for defensive responses