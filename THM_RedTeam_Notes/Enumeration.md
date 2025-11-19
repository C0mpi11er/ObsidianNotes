
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


DNS SMB SNMP
========
dns zone trnf might be resricted if its not then 
->>dig -t AXFR DOMAIN_NAME @DNS_SERVER




snmp for holding info about devices
smb for getting shared info and filed like frm device to prnters etc  ->> cmmd "net share"




The focus of this room was on built-in command-line tools in both Linux and MS Windows systems. Many commands exist in both systems, although the command arguments and resulting output are different. The following tables show the primary Linux and MS Windows commands that we relied on to get more information about the system.
Linux Command 	Description
hostname 	shows the system’s hostname
who 	shows who is logged in
whoami 	shows the effective username
w 	shows who is logged in and what they are doing
last 	shows a listing of the last logged-in users
ip address show 	shows the network interfaces and addresses
arp 	shows the ARP cache
netstat 	prints network connections
ps 	shows a snapshot of the current processes
Windows Command 	Description
systeminfo 	shows OS configuration information, including service pack levels
whoami 	shows the user name and group information along with the respective security identifiers
netstat 	shows protocol statistics and current TCP/IP network connections
net user 	shows the user accounts on the computer
net localgroup 	shows the local groups on the computer
arp 	shows the IP-to-Physical address translation tables


