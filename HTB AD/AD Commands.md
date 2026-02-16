Add new user
==
PS C:\htb> New-ADUser -Name "Orion Starchaser" -Accountpassword (ConvertTo-SecureString -AsPlainText (Read-Host "Enter a secure password") -Force ) -Enabled $true -OtherAttributes @{'title'="Analyst";'mail'="o.starchaser@inlanefreight.local"}



PowerShell To Unlock a User
=
PS C:\htb> Unlock-ADAccount -Identity amasters 


Reset User Password (Set-ADAccountPassword)
=
PS C:\htb> Set-ADAccountPassword -Identity 'amasters' -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rdReset!" -Force)


Force Password Change (Set-ADUser)
=
PS C:\htb> Set-ADUser -Identity amasters -ChangePasswordAtLogon $true


create OU
=
PS C:\htb> New-ADOrganizationalUnit -Name "Security Analysts" -Path "OU=IT,OU=HQ-NYC,OU=Employees,OU=CORP,DC=INLANEFREIGHT,DC=LOCAL"

create group
=

PS C:\htb> New-ADGroup -Name "Security Analysts" -SamAccountName analysts -GroupCategory Security -GroupScope Global -DisplayName "Security Analysts" -Path "OU=Security Analysts,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL" -Description "Members of this group are Security Analysts under the IT OU"

ad group
=

!note use samaccount names

Add-ADGroupMember -Identity analysts -Members ACepheus,OStarchaser,ACallisto


Duplicate the Object via PowerShell
=
PS C:\htb> Copy-GPO -SourceName "Logon Banner" -TargetName "Security Analysts Control"


Link the New GPO to an OU
=
PS C:\htb> New-GPLink -Name "Security Analysts Control" -Target "ou=Security Analysts,ou=IT,OU=HQ-NYC,OU=Employees,OU=Corp,dc=INLANEFREIGHT,dc=LOCAL" -LinkEnabled Yes


join a domain
=
PS C:\htb> Add-Computer -DomainName INLANEFREIGHT.LOCAL -Credential INLANEFREIGHT\HTB-student_adm -Restart


Add a Remote Computer to a Domain
=
PS C:\htb> Add-Computer -ComputerName ACADEMY-IAD-W10 -LocalCredential ACADEMY-IAD-W10\image -DomainName INLANEFREIGHT.LO


Check OU Membership of a Host
==
PS C:\htb> Get-ADComputer -Identity "ACADEMY-IAD-W10" -Properties * | select CN,CanonicalName,IPv4Address
