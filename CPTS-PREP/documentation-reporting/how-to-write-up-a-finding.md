# 🛰️ Penetration Testing Report Template

## 🔍 Evidence Collection Guidelines

### 📊 Reproduction Steps Guidelines
[!INFO] Structure principles:
- Break each step into separate figures
- Include full tool configuration
- Write narrative between figures
- Explain thought process
- Offer alternative validation tools

[!WARNING] Evidence quality criteria:
- Completely defensible proof
- Clear cause-and-effect demonstration
- Client environment verification
- Professional presentation

### 📷 Screenshot Standards
[!NOTE] Requirements:
- Include URL/address bar
- Show `ifconfig/ipconfig` for host verification
- Disable bookmarks bar
- Remove unprofessional browser extensions
- Crop to relevant information
- Add minimal annotations for clarity

[!FAILURE] Avoid:
- Random internet images
- Generic vulnerability screenshots
- Unclear context or location
- Unprofessional browser setup

### 💻 Terminal Output Presentation
[!SUCCESS] Preferred: Copy-paste terminal text
Benefits:
- Client can copy-paste commands
- Easier redaction
- Professional appearance
- Smaller file sizes

Example format:
```bash
┌─[htb-student]─[10.10.14.3]
└──╼ $ crackmapexec smb 172.16.5.5 -u administrator -p '<REDACTED>'
SMB    172.16.5.5    445    DC01    [+] INLANEFREIGHT.LOCAL\administrator:<REDACTED> (Pwn3d!)
```

## 📝 Remediation Best Practices

### ✅ Good Remediation Examples
[!SUCCESS] Specific and actionable:
"To remediate this finding, update the following registry values:
- `HKLM\System\CurrentControlSet\Control\Lsa\RestrictAnonymous = 2`
- `HKLM\System\CurrentControlSet\Control\Lsa\RestrictAnonymousSAM = 1`

Note: Registry changes should be tested in a small group before enterprise deployment."

[!INFO] Multiple options provided:
"There are different approaches to address this finding:
1. [Vendor] has published an official workaround (see references)
2. Commercial tools are available but may be cost-prohibitive
3. Interim mitigation can be achieved through network segmentation"

### ❌ Bad Remediation Examples
[!WARNING] Vague and unhelpful:
"Reconfigure your registry settings to harden against X"
"An attacker can own your whole network cause your DC is way out of date. You should really fix that!"
"Implement [expensive commercial tool] to address this finding"

Problems:
- No specific steps
- Unprofessional language
- Only expensive solutions
- No context or warnings

## 🎯 Sample Finding Examples

### 🔑 Kerberoasting Finding
```markdown
## Weak Kerberos Authentication ("Kerberoasting")

| Field | Details |
|-------|---------|
| **Severity** | High |
| **CVSS Score** | 9.5 |
| **Affected Systems** | INLANEFREIGHT.LOCAL domain |
| **CVE** | N/A (Configuration Issue) |

### Description
Service accounts in the Active Directory domain are configured with Service Principal Names (SPNs) that allow any authenticated domain user to request Kerberos tickets encrypted with the service account's password. These tickets can be extracted and subjected to offline password cracking attacks.

### Impact
Successful exploitation provides attackers with service account credentials that often have elevated privileges, enabling lateral movement and potential domain compromise.

### Remediation
1. Enable AES encryption for Kerberos (disable RC4)
2. Implement Group Managed Service Accounts (gMSA)
3. Use 25+ character complex passwords for service accounts
4. Regular password rotation for service accounts
5. Monitor for unusual TGS ticket requests

### References
- Microsoft: [[Kerberoasting Attack Protection]]
- MITRE ATT&CK: [[T1208 - Kerberoasting]]

### Reproduction Steps
[Detailed GetUserSPNs.py and Hashcat evidence]
```

### 🌐 Web Application Finding
```markdown
## Tomcat Manager Weak/Default Credentials

| Field | Details |
|-------|---------|
| **Severity** | High |
| **CVSS Score** | 9.5 |
| **Affected Systems** | 192.168.195.205:8080 |

### Description
Apache Tomcat Manager application is accessible with default credentials (tomcat:tomcat), allowing unauthorized administrative access and potential remote code execution.

### Impact
Attackers can deploy malicious web applications (WAR files) leading to complete server compromise and potential lateral movement within the network.

### Remediation
1. Change default Tomcat Manager credentials immediately
2. Restrict access to management interface by IP
3. Disable Tomcat Manager if not required
4. Implement strong authentication mechanisms
5. Regular credential rotation policy

### References
- Apache Tomcat: [[Security Considerations]]
- OWASP: [[Default Passwords]]

### Reproduction Steps
[Browser screenshots and WAR upload evidence]
```

## 🔍 Quality Reference Selection

### ✅ Good Reference Sources
[!INFO] Vendor-agnostic sources:
- OWASP documentation
- NIST guidelines
- SANS Institute resources
- Security research papers
- Vendor security advisories

Quality criteria:
- Thorough walkthrough provided
- No paywall restrictions
- Gets to the point quickly
- Clean, professional websites
- Reputable, stable sources

### ❌ Poor Reference Sources
[!WARNING] Avoid:
- Paywall-protected content
- Competitor websites
- Personal blogs (unstable)
- Ad-heavy websites
- Overly complex documentation
- Recipe-style articles with excessive fluff

## 🎯 HTB Academy Lab Solution

### Lab Question
```bash
# Question: Good or Bad remediation recommendation?
# "An attacker can own your whole entire network cause your DC is way out of date. You should really fix that!"

# Answer: Bad

Problems with this recommendation:
- Unprofessional language ("cause", "way out of date")
- Vague guidance ("fix that")
- No specific steps
- No context or warnings
- Inflammatory tone
```

### WriteHat Tool Practice
```bash
# Lab access:
# Browse to: https://TARGET_IP
# Credentials: htb-student:HTB_@cademy_stdnt!

Practice activities:
1. Add findings to database
2. Generate custom reports
3. Experiment with finding templates
4. Practice evidence organization
```

## 🔧 Professional Writing Guidelines

### 📝 Language Standards
[!INFO] Professional tone:
- Clear, concise language
- Technical accuracy
- Respectful communication
- Actionable guidance
- Appropriate warnings

Avoid:
- Casual language
- Inflammatory statements
- Vague recommendations
- Unprofessional tone
- Absolute statements without proof

### 🎯 Client Consideration
[!NOTE] Reader perspective:
- May not have penetration testing background
- Need clear reproduction steps
- Require actionable remediation
- Appreciate effort level estimates
- Value multiple solution options

Evidence presentation:
- Assume no tool familiarity
- Explain each step clearly
- Provide alternative tools
- Include setup configurations
- Verify complete defensibility

---

## 💡 Key Takeaways

1. **Detailed findings** enable technical team reproduction and validation.
2. **Evidence quality** must be completely defensible.
3. **Remediation recommendations** should be specific and actionable.
4. **Professional language** essential for client credibility.
5. **Multiple solution options** accommodate different budgets and capabilities.
6. **Reference quality** affects long-term finding usefulness.
7. **Consistent formatting** improves report readability and professionalism.

---

*Well-written findings combine technical accuracy with clear communication, providing clients with actionable intelligence for vulnerability remediation and security improvement.*