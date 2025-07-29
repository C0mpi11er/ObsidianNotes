`xfreerdp /dynamic-resolution +clipboard /cert:ignore /v:10.10.54.162 /u:Administrator /p:'TryH4ckM3!'`

#switch the ip user name and password 

#Downloading files sever from linux 

Invoke-WebRequest -Uri http://linux-ip:8000/filename.ext -OutFile "C:\Users\YourUser\Downloads\filename.ext"

**Grant Full Control To User For file**
icacls "C:\Path\To\File.ext" /grant YourUser:F
# note!! use this methode it is better 

xfreerdp /u:username /p:password /v:<ip>:3389 /dynamic-resolution /network:auto +heartbeat