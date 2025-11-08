
->> user accounts you see in AD include 
built in local accounts (not managed by AD)
domain user accounts (limited in AD)
virtual account
manage service  accounts(higher priv than Domain user in AD)
Domain Admin Account(has all the privilages) Target!



BUILTIN\Administrator:	Local admin access on a domain controller
Domain Admins:	Administrative access to all resources in the domain
Enterprise Admins:	Available only in the forest root
Schema Admins:	Capable of modifying domain/forest; useful for red teamers
Server Operators:	Can manage domain servers
Account Operators:	Can manage users that are not in privileged groups




AD Enum

Get-ADUser  -Filter *
 Get-ADUser -Filter * -SearchBase "CN=Users,DC=THMREDTEAM,DC=COM"