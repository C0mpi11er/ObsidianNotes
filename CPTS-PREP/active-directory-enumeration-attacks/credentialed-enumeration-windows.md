# 🛰️ Windows-Based Credentialed Enumeration

## 🔍 Lab Walkthrough & Solution Guide

### 💡 Introduction to BloodHound and PowerView Usage

BloodHound is a powerful tool that uses graph theory to map the relationships within an Active Directory environment, making it easier to identify potential privilege escalation paths. PowerView is a PowerShell module packed with functions for advanced reconnaissance, such as enumerating users, groups, ACLs, and more.

---

## 🧵 BloodHound & PowerView Queries

### 🔍 BloodHound Queries in Cypher Language

#### 💡 Basic Queries
```cypher
# Find Kerberoastable accounts
MATCH (u:User) WHERE u.hasspn = true RETURN u

# Find users with administrative rights on specific computers
MATCH p=(u:User)-[:AdminTo]->(c:Computer) RETURN p

# Identify constrained delegation opportunities
MATCH p=(u:User)-[:AllowedToDelegate]->(c:Computer) RETURN p
```

#### 🚀 Advanced Queries
```cypher
# Find Kerberoastable accounts with high-value attributes
MATCH (u:User {highvalue:true}) WHERE u.hasspn = true RETURN u

# Identify users who are admins on computers that have sessions from high-value users
MATCH p=(u:User)-[:AdminTo]->(c:Computer)<-[:HasSession]-(hvuser:User) WHERE hvuser.highvalue = true RETURN p

# Find constrained delegation opportunities with specific conditions
MATCH p=(u:User {highvalue:true})-[:AllowedToDelegate]->(c:Computer) RETURN p
```

### 💡 PowerView Queries and Commands

#### 🔍 Basic Enumerations
```powershell
# List all users in the domain
Get-DomainUser -Properties memberof,admincount,serviceprincipalname

# List all groups in the domain with their members
Get-DomainGroup -Properties memberof | Get-DomainGroupMember

# Check if a user is an administrator on a remote host
Test-AdminAccess -ComputerName HOSTNAME
```

#### 🚀 Advanced Enumerations
```powershell
# Find Kerberoastable users
Get-DomainUser -SPN | Select-Object samaccountname,serviceprincipalname

# Identify service accounts with constrained delegation
Get-DomainUser -TrustedToAuth | Where-Object {$_.trustedtodelegated -eq $true}

# List all computers in the domain
Get-DomainComputer -Properties operatingsystem,serviceprincipalname

# Find local admin rights on remote hosts
Find-DomainLocalAdminAccess -ComputerName HOSTNAME -Credential (Get-Credential)
```

---

## 📝 Lab Questions & Solutions

### 🔍 **Question 1: "Using Bloodhound, determine how many Kerberoastable accounts exist within the INLANEFREIGHT domain. (Submit the number as the answer)"**

**Solution Process:**
```cypher
# Method 1: BloodHound Raw Query
MATCH (u:User) WHERE u.hasspn=true RETURN count(u)

# Method 2: Pre-built Query Analysis
# Go to Analysis tab -> "Find All Kerberoastable Users"
# Count the returned nodes

# Method 3: PowerView verification
Get-DomainUser -SPN | Measure-Object | Select-Object Count

# Method 4: ActiveDirectory module verification
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} | Measure-Object | Select-Object Count
```

**Expected Answer Format:** `[number]` (e.g., `13`)

### ⚡ **Question 2: "What PowerView function allows us to test if a user has administrative access to a local or remote host?"**

**Solution:**
```powershell
# The function is: Test-AdminAccess
Test-AdminAccess -ComputerName HOSTNAME

# Alternative verification:
Get-Help Test-AdminAccess
```

**Expected Answer:** `Test-AdminAccess`

### 📁 **Question 3: "Run Snaffler and hunt for a readable web config file. What is the name of the user in the connection string within the file?"**

**Solution Process:**
```cmd
# Step 1: Run Snaffler to find web.config files
.\Snaffler.exe -d INLANEFREIGHT.LOCAL -s -v data | findstr -i "web.config"

# Step 2: Look for web.config files in the output
# Example output might show:
# [File] {Red}<KeepExtExactRed|R|^\.config$|1024B|...>(\\HOST\Share\path\web.config) .config

# Step 3: Access the file and examine connection strings
type "\\HOSTNAME\Share\path\web.config"

# Step 7: Look for connection string patterns like:
# <connectionStrings>
#   <add name="DefaultConnection" connectionString="Server=...;User ID=USERNAME;Password=..." />
# </connectionStrings>

# Step 8: Extract the username from the connection string
```

**Expected Answer Format:** `[username]` (e.g., `sqlservice`)

### 🔐 **Question 4: "What is the password for the database user?"**

**Solution Process:**
```cmd
# Continue from Question 3 - examine the same web.config file
# Look for the password in the connection string:
# connectionString="Server=server;User ID=username;Password=PASSWORD_HERE;"

# Extract the password value from the connection string
```

**Expected Answer Format:** `[password]` (e.g., `MyV3ryStr0ngP@ssw0rd!`)

---

## 🔧 Advanced Enumeration Techniques

### 🎯 **Comprehensive User Analysis**
```powershell
# Find high-value user accounts
Get-DomainUser -Properties admincount,serviceprincipalname,memberof | Where-Object {$_.admincount -eq 1 -or $_.serviceprincipalname -ne $null}

# Analyze password policies and account settings
Get-DomainUser -Properties pwdlastset,lastlogontimestamp,useraccountcontrol | Where-Object {$_.useraccountcontrol -match "DONT_EXPIRE_PASSWORD"}

# Find users with constrained delegation
Get-DomainUser -TrustedToAuth -Properties trustedtodelegated,serviceprincipalname
```

### 🖥️ **Computer and Service Analysis**
```powershell
# Find computers with specific services
Get-DomainComputer -Properties operatingsystem,serviceprincipalname | Where-Object {$_.serviceprincipalname -match "MSSQL|HTTP|CIFS"}

# Identify file servers and shares
Get-DomainFileServer
Get-DomainDFSShare

# Find computers with sessions from high-value users
Find-DomainUserLocation -UserGroupIdentity "Domain Admins"
```

### 🔐 **Permission and ACL Analysis**
```powershell
# Find interesting ACLs
Find-InterestingDomainAcl -ResolveGUIDs

# Find modifiable GPOs
Get-DomainGPO | Where-Object {$_.gpcfilesyspath -like "*SYSVOL*"}

# Analyze dangerous privileges
Get-DomainUser -AdminCount | Get-DomainObjectAcl -ResolveGUIDs | Where-Object {$_.ActiveDirectoryRights -match "GenericAll|WriteDacl|WriteOwner"}
```

---

## ⚡ Quick Reference Commands

### 🔧 **Essential One-Liners**
```powershell
# Quick Kerberoastable account count
(Get-ADUser -Filter {ServicePrincipalName -ne "$null"}).Count

# Find Domain Admins with PowerView
Get-DomainGroupMember -Identity "Domain Admins" | Select-Object MemberName

# Quick admin access test
Test-AdminAccess -ComputerName (Get-Content hosts.txt)

# Fast SPN enumeration
Get-DomainUser -SPN | Select-Object samaccountname,serviceprincipalname

# Trust relationship summary
Get-DomainTrust | Select-Object SourceName,TargetName,TrustDirection,TrustType
```

### 🔍 **Data Analysis and Correlation**
```powershell
# Cross-reference users and groups
$users = Get-DomainUser -Properties memberof
$groups = Get-DomainGroup
$users | ForEach-Object { 
    $_.memberof | ForEach-Object { 
        if($_ -match "Domain Admins|Enterprise Admins|Backup Operators") { 
            Write-Host "High-value group membership: $($_.samaccountname) -> $_" 
        } 
    } 
}

# Correlate sessions with admin rights
$admins = Get-DomainGroupMember -Identity "Domain Admins"
$sessions = Get-NetSession
$admins | ForEach-Object { 
    $sessions | Where-Object {$_.UserName -eq $_.MemberName} 
}
```

---

## 🔑 Key Takeaways

### ✅ **Windows Enumeration Advantages**
- **Native Tool Integration**: Access to ActiveDirectory PowerShell module and built-in cmdlets
- **Stealth Operations**: Blend in with legitimate administrative activities
- **Comprehensive Analysis**: Deep attribute and relationship enumeration
- **Visual Intelligence**: BloodHound provides unmatched attack path visualization

### 🎯 **Strategic Priorities**
1. **Kerberoastable Accounts**: Identify service accounts with SPNs for credential extraction
2. **Administrative Rights**: Map local admin access across domain systems
3. **Sensitive File Discovery**: Use Snaffler to find configuration files and credentials
4. **Attack Path Analysis**: Leverage BloodHound for relationship mapping and privilege escalation paths
5. **Trust Relationships**: Understand cross-domain attack opportunities

### 🚀 Next Steps: Leveraging Enumeration Results

1. **Credential Harvesting**:
   - Use Kerberoasting techniques to extract service account credentials.
   
2. **Privilege Escalation**:
   - Exploit administrative rights on remote hosts to gain elevated privileges.

3. **File Access & Lateral Movement**:
   - Identify file shares and use them for lateral movement within the network.

---

## 📜 Conclusion

By effectively leveraging BloodHound, PowerView, and Snaffler in your enumeration process, you can significantly enhance your ability to identify potential attack vectors within an Active Directory environment. This approach not only aids in understanding the current security posture but also provides critical insights for improving it. 

**Happy Hacking!**

---


# 🙏 Thank You
If this guide has been helpful, feel free to reach out or leave a comment below. Happy hacking and stay safe!

---

# 📂 References & Resources

- [BloodHound GitHub Repository](https://github.com/BloodHoundPy/bloodhound)
- [PowerView PowerShell Module](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
- [Snaffler Documentation](https://github.com/canbox0/snaffler) 

---



# 📦 Tools Used
1. **BloodHound**: For AD graph analysis.
2. **PowerView**: PowerShell module for advanced enumeration.
3. **Snaffler**: To find readable files and directories.

---

Feel free to explore these tools further to deepen your understanding of the environment and enhance your red teaming capabilities! 🚀