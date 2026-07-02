🛠️ Active Directory Hardening Field Manual (Defensive / Engineering Edition)

🧠 Core Operator Mindset

> [!INFO] 🎯 Hardening Philosophy
> 
> Text
> 
> ```
> Minimize the attack surface.
> Eradicate persistent credentials.
> Restrict lateral mobility.
> ```
> 
> 💡 AD Hardening is not about purchasing security products. It is the tactical deployment of native baseline parameters designed to:
> 
> - Break lateral movement patterns and restrict host token harvesting
> - Invalidate common credential extraction techniques (e.g., Kerberoasting, NTLM relaying)
> - Defend administrative boundaries across transitive and forest trusts
> - Establish comprehensive visibility into structural directory modifications

⚡ Phase 1 — Environment Auditing & Asset Management

🔍 Directory Inventory Discovery

> [!SUCCESS] 🌐 Map System Deployment Patterns
> 
> Powershell
> 
> powershell
> 
> ```
> # Extract high-fidelity infrastructure layouts periodically
> Get-ADDomainController -Filter * | Select-Object Name, IPAddress, OperatingSystem
> ```
> 
> Use code with caution.
> 
> 💡 Mechanics:
> 
> - Establishes a verified structural baseline of all core enterprise assets.
> - Identifies rogue, forgotten, or unpatched legacy systems within the tree.

⚔️ Domain Trust Structural Reconnaissance

> [!TIP] 🧠 Map Forest Boundary Risks
> 
> Powershell
> 
> powershell
> 
> ```
> Get-ADTrust -Filter * | Select-Object Name, TrustType, TrustDirection, Attributes
> ```
> 
> Use code with caution.
> 
> 💡 Strategy:
> 
> - Pinpoints inbound, outbound, or bidirectional entryways into external partners.
> - Evaluates whether transitively open pathways expose corporate data across domains.

🔥 Phase 2 — Human Element & Account Isolation

💣 Restricting Local Privileged Entryways

> [!DANGER] 🚀 Neutralize Host Token Harvesting
> 
> Powershell
> 
> powershell
> 
> ```
> # Enforce Administrative Split-Tiering and apply LAPS to workstations
> Set-GPLink -Name "Enforce_LAPS_Workstations" -Target "OU=Workstations,DC=inlanefreight,DC=local"
> ```
> 
> Use code with caution.
> 
> 💡 Strategy:
> 
> - **NEVER** give normal users administrative authority over local endpoints.
> - Disabling the default `RID-500` local admin and enforcing unique, rotated passwords via LAPS stops pass-the-hash movements cold.

⚡ Mitigating Admin Token Exposure

> [!SUCCESS] ⚡ High-Value Account Isolation
> 
> Powershell
> 
> powershell
> 
> ```
> Clear-ADAccountExpiration -Identity "Domain Admins"
> # Purge unnecessary engineering accounts from highly privileged groups
> Remove-ADGroupMember -Identity "Domain Admins" -Members "stale_admin_user"
> ```
> 
> Use code with caution.
> 
> 💡 Mechanics:
> 
> - Systematically shrinks administrative group bloat down to minimal required counts.
> - Enforces split tiers: admins must utilize distinct low-privilege keys for email/browsing.

🧪 Phase 3 — Technical Configuration Defenses

🔬 Preventing Service Target Extraction (gMSA Migration)

> [!SUCCESS] 🧠 Invalidate Kerberoasting Attack Chains
> 
> Powershell
> 
> powershell
> 
> ```
> New-ADServiceAccount -Name "gmsa_sql" -DNSHostName "sql01.inlanefreight.local" -PrincipalsAllowedToRetrieveManagedPassword "SQL_Servers"
> ```
> 
> Use code with caution.
> 
> 💡 Mechanics:
> 
> - Replaces vulnerable, human-managed service accounts with Group Managed Service Accounts.
> - Uses 128-character complex passwords rotated by Windows every 30 days to neutralize offline brute-forcing.

⚔️ Disabling Rogue Machine Account Provisioning

> [!WARNING] 🔥 Block noPac & RBCD Escalation Paths
> 
> Powershell
> 
> powershell
> 
> ```
> Set-ADDomain -Identity "inlanefreight.local" -Replace @{"ms-DS-MachineAccountQuota"=0}
> ```
> 
> Use code with caution.
> 
> 💡 Strategy:
> 
> - Forces the domain parameter `ms-DS-MachineAccountQuota` to exactly zero.
> - Strips standard users of the default right to bind 10 new machines to the directory context.

🧠 Print Spooler Architecture Hardening

> [!TIP] 🪟 Invalidate Spooler Coercion Methods
> 
> Powershell
> 
> powershell
> 
> ```
> Stop-Service -Name "Spooler" -Force; Set-Service -Name "Spooler" -StartupType Disabled
> ```
> 
> Use code with caution.
> 
> 💡 Tactics:
> 
> - Stops the Print Spooler component directly on all primary Domain Controllers.
> - Shuts down immediate coercion mechanisms (e.g., PetitPotam, PrinterBug) used to force DC authentication.

🧨 Phase 4 — Protected Users Security Group Deployment

🔍 Enforcing Endpoint Memory Restrictions

> [!BUG] ☠️ Eliminate Plaintext Credential Footprints
> 
> Powershell
> 
> powershell
> 
> ```
> Add-ADGroupMember -Identity "Protected Users" -Members "enterprise_admin_bob"
> ```
> 
> Use code with caution.
> 
> 💡 Payload Profile:
> 
> - Automatically applies non-configurable, restrictive policies to high-value identities inside Windows Server 2012 R2+ environments.
> - Prevents operating systems from caching plaintext data or keys for CredSSP, WDigest, or NTLM packages.

🎯 Kerberos Layer Hardening

> [!SUCCESS] 🧩 Neutralize Delegation & Ticket Exploitation
> 
> Text
> 
> ```
> Automatic Group Protections Applied:
> - Restricts Kerberos authentication to secure AES configurations (bans RC4/DES)
> - Blocks constrained and unconstrained Kerberos delegation actions entirely
> - Restricts Kerberos Ticket Granting Ticket (TGT) lifetimes to a strict 4-hour window
> ```
> 
> 💡 Mechanics:
> 
> - Enforces strict AES pre-authentication, rendering standard ticket-forgery mechanics useless.
> - Shortens the window of opportunity for stolen tickets by refusing any TGT renewal beyond the 4-hour limit.

---