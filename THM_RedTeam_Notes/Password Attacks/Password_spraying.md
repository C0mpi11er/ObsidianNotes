
Password Spraying is an effective technique used to identify valid credentials. Nowadays, password spraying is considered one of the common password attacks for discovering weak passwords.


command in hydra to do password spraying..just to give an idea
```shell-session
 hydra -L usernames-list.txt -p Spring2021 ssh://10.1.1.10
```

for RDP serveices use RDPassSpray tool 


OWA (out look web access )
Tools:

- [SprayingToolkit](https://github.com/byt3bl33d3r/SprayingToolkit) (atomizer.py)
- [MailSniper](https://github.com/dafthack/MailSniper)

### SMB

- Tool: Metasploit (auxiliary/scanner/smb/smb_login)