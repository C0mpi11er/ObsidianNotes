
things to find in this phase 

    Users and groups
    Hostnames
    Routing tables
    Network shares
    Network services
    Applications and banners
    Firewall configurations
    Service settings and audit configurations
    SNMP and DNS details
    Hunting for credentials (saved on web browsers or client applications)
linux
===========
->> sear release in /etc/*-release
->> get hostname
->> passwd group shadow (root) mail dir ins /var
->> installed apps bin and sbin
->> dns server resolve.conf 
netstat

========
list open files lsof

windows
===============
systeminfo>> info
quckly system patch/uptadate-->> wmic qfe get Caption,Description

windows services-->> net start
interest in installed apps ->>  wmic product get name,version,vendor

user ->> whoami /groups
user ->> net user
groups ->> net group(domain controller) netlocal group 
netlocal group administrators 

sys network ->> ipconfig /all

netstat for listening services etc 

