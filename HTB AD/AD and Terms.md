Active Directory (AD) is a directory service for Windows network environments. It is a distributed, hierarchical structure that allows for centralized management of an organization's resources, including users, computers, groups, network devices and file shares, group policies, servers and workstations, and trusts. AD provides authentication and authorization functions within a Windows domain environment


basic AD user account with no added privileges can be used to enumerate the majority of objects contained within AD, including but not limited to:
	
Domain Computers ,	Domain Users,
Domain Group Information, 	Organizational Units (OUs),
Default Domain Policy, 	Functional Domain Levels,
Password Policy, 	Group Policy Objects (GPOs),
Domain Trusts, 	Access Control Lists (ACLs)


 
### 🗒️ Active Directory Term

**Object** — Any AD resource (users, computers, OUs, printers, DCs).

**Attributes** — Properties describing an object (e.g., hostname, displayName).

**Schema** — Blueprint defining object types and their attributes.

**Domain** — Logical group of AD objects; security boundary.

**Forest** — Collection of domains; highest AD container.

**Tree** — Domains sharing one root namespace inside a forest.

**Container** — Object that can hold other objects.

**Leaf** — Object with no children.

**GUID** — Permanent unique ID for every AD object.

**Security Principal** — Anything that can authenticate and be assigned permissions.

**SID** — Unique identifier for a security principal.

**Distinguished Name (DN)** — Full LDAP path to an object.

**Relative Distinguished Name (RDN)** — Object’s name within its parent container.

**sAMAccountName** — Legacy logon name (≤20 chars).

**userPrincipalName (UPN)** — Logon format: user@domain.

**FSMO Roles** — Special DC roles ensuring AD consistency.

**Global Catalog (GC)** — DC storing forest-wide searchable object data.

**RODC** — Read-only domain controller.

**Replication** — Syncing AD changes between DCs.

**SPN** — Kerberos identifier for a service.

**GPO** — Collection of policy settings applied to users/computers.

**ACL** — List of permission rules on an object.

**ACE** — Single allow/deny/audit rule in an ACL.

**DACL** — Controls who can access an object.

**SACL** — Logs access attempts.

**FQDN** — Full DNS name of a host.

**Tombstone** — Deleted AD object awaiting final removal.

**AD Recycle Bin** — Allows restoration of deleted objects with attributes intact.

**SYSVOL** — Replicated folder storing GPOs and scripts.

**AdminSDHolder** — Template ACL for protected groups.

**dsHeuristics** — Forest-wide behavior configuration attribute.

**adminCount** — Marks protected/privileged accounts.

**ADUC** — GUI for routine AD management.

**ADSI Edit** — Advanced low-level AD editor.

**sIDHistory** — Stores old SIDs from migrations.

**NTDS.DIT** — AD database containing user data and password hashes.

**MSBROWSE** — Legacy LAN browsing protocol (obsolete).