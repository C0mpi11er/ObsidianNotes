
# PowerShell Cheat Sheet

> [!NOTE]  
> PowerShell commands are called **cmdlets**. They usually follow this format:  
> `Verb-Noun`  
> Example: `Get-ChildItem`, `Set-Location`, `Copy-Item`

---

## 1. Basic Navigation

|Task|Command|
|---|---|
|Show current directory|`pwd`|
|List files/folders|`ls`|
|List files/folders full command|`Get-ChildItem`|
|Change directory|`cd foldername`|
|Go back one folder|`cd ..`|
|Go to root of drive|`cd \`|
|Go to user home|`cd ~`|
|Switch drive|`D:`|
|Clear screen|`cls`|

> [!TIP]  
> `ls`, `dir`, and `gci` all work in PowerShell.  
> They are aliases for `Get-ChildItem`.

```powershell
pwd
ls
cd Desktop
cd ..
cd C:\
```

---

## 2. Listing Files Better

|Task|Command|
|---|---|
|List hidden files|`ls -Force`|
|List only files|`ls -File`|
|List only folders|`ls -Directory`|
|List recursively|`ls -Recurse`|
|Sort by size|`ls|
|Sort by newest|`ls|
|Show full file details|`ls|

```powershell
ls -Force
ls -File
ls -Directory
ls -Recurse
ls | Sort-Object LastWriteTime -Descending
```

> [!IMPORTANT]  
> Be careful with `-Recurse` in large folders like `C:\Users` or `C:\Windows`.  
> It can return a huge amount of results.

---

## 3. Creating Files and Folders

|Task|Command|
|---|---|
|Create folder|`mkdir foldername`|
|Create empty file|`New-Item file.txt`|
|Create folder full command|`New-Item -ItemType Directory foldername`|
|Create file full command|`New-Item -ItemType File file.txt`|

```powershell
mkdir notes
New-Item test.txt
New-Item -ItemType Directory reports
New-Item -ItemType File report.txt
```

---

## 4. Opening Files and Folders

|Task|Command|
|---|---|
|Open current folder in Explorer|`explorer .`|
|Open specific folder|`explorer C:\Users`|
|Open file with default app|`Invoke-Item file.txt`|
|Open file alias|`ii file.txt`|

```powershell
explorer .
ii notes.txt
Invoke-Item image.png
```

> [!TIP]  
> `.` means **current directory**.

---

## 5. Copy, Move, Rename, Delete

|Task|Command|
|---|---|
|Copy file|`Copy-Item file.txt backup.txt`|
|Copy folder|`Copy-Item folder backup -Recurse`|
|Move file|`Move-Item file.txt folder\`|
|Rename file|`Rename-Item old.txt new.txt`|
|Delete file|`Remove-Item file.txt`|
|Delete folder|`Remove-Item folder -Recurse`|
|Force delete|`Remove-Item file.txt -Force`|

```powershell
Copy-Item notes.txt backup.txt
Copy-Item logs logs_backup -Recurse
Move-Item test.txt C:\Users\Public\
Rename-Item old.txt new.txt
Remove-Item test.txt
```

> [!CAUTION]  
> `Remove-Item -Recurse -Force` can delete folders and everything inside them.  
> Double-check your path before running it.

---

## 6. Reading File Content

|Task|Command|
|---|---|
|Show file content|`Get-Content file.txt`|
|Alias for Get-Content|`cat file.txt`|
|Show first lines|`Get-Content file.txt -TotalCount 10`|
|Show last lines|`Get-Content file.txt -Tail 10`|
|Watch file live|`Get-Content file.txt -Wait`|

```powershell
cat notes.txt
Get-Content logs.txt -Tail 20
Get-Content app.log -Wait
```

> [!TIP]  
> `Get-Content -Wait` is useful for watching log files in real time.

---

## 7. Writing to Files

|Task|Command|
|---|---|
|Write text to file|`"hello" > file.txt`|
|Append text to file|`"new line" >> file.txt`|
|Use Set-Content|`Set-Content file.txt "hello"`|
|Use Add-Content|`Add-Content file.txt "new line"`|

```powershell
"Hello world" > hello.txt
"Another line" >> hello.txt

Set-Content notes.txt "First line"
Add-Content notes.txt "Second line"
```

> [!IMPORTANT]  
> `>` overwrites the file.  
> `>>` appends to the file.

---

## 8. Searching Files and Text

|Task|Command|
|---|---|
|Find file by name|`ls -Recurse -Filter "*.txt"`|
|Search text in file|`Select-String "password" file.txt`|
|Search text recursively|`Select-String "error" *.log`|
|Search all files recursively|`Select-String "error" -Path .\* -Recurse`|

```powershell
ls -Recurse -Filter "*.txt"
Select-String "error" app.log
Select-String "failed" -Path .\*.log
```

> [!NOTE]  
> `Select-String` is like `grep` in Linux.

---

## 9. Filtering with Where-Object

|Task|Command|
|---|---|
|Files bigger than 1MB|`ls|
|Files modified today|`ls|
|Folders only|`ls|

```powershell
ls | Where-Object {$_.Length -gt 1MB}
ls | Where-Object {$_.Name -like "*.txt"}
ls | Where-Object {$_.LastWriteTime -gt (Get-Date).Date}
```

> [!TIP]  
> `$_` means **the current item** being processed.

---

## 10. Useful Operators

|Operator|Meaning|Example|
|---|---|---|
|`-eq`|equals|`$x -eq 5`|
|`-ne`|not equal|`$x -ne 5`|
|`-gt`|greater than|`$x -gt 5`|
|`-lt`|less than|`$x -lt 5`|
|`-ge`|greater/equal|`$x -ge 5`|
|`-le`|less/equal|`$x -le 5`|
|`-like`|wildcard match|`$name -like "*.txt"`|
|`-match`|regex match|`$text -match "error"`|
|`-contains`|list contains item|`$list -contains "admin"`|

```powershell
$name = "report.txt"
$name -like "*.txt"

$age = 20
$age -gt 18
```

---

## 11. Variables

|Task|Command|
|---|---|
|Create variable|`$name = "Tobias"`|
|Print variable|`$name`|
|Store command output|`$files = ls`|
|Count items|`$files.Count`|

```powershell
$name = "Tobias"
echo $name

$files = ls
$files.Count
```

> [!NOTE]  
> PowerShell variables always start with `$`.

---

## 12. Environment Variables

|Task|Command|
|---|---|
|Show all env variables|`Get-ChildItem Env:`|
|Show PATH|`$env:Path`|
|Show username|`$env:USERNAME`|
|Show computer name|`$env:COMPUTERNAME`|
|Set env variable temporarily|`$env:TEST="hello"`|

```powershell
$env:USERNAME
$env:COMPUTERNAME
$env:Path
Get-ChildItem Env:
```

> [!IMPORTANT]  
> Setting an env variable with `$env:NAME="value"` only lasts for the current session.

---

## 13. Processes

|Task|Command|
|---|---|
|List processes|`Get-Process`|
|Find process by name|`Get-Process chrome`|
|Sort by CPU|`Get-Process|
|Stop process by name|`Stop-Process -Name notepad`|
|Stop process by PID|`Stop-Process -Id 1234`|

```powershell
Get-Process
Get-Process chrome
Get-Process | Sort-Object CPU -Descending
Stop-Process -Name notepad
```

> [!CAUTION]  
> Don’t stop system processes unless you know what they do.

---

## 14. Services

|Task|Command|
|---|---|
|List services|`Get-Service`|
|Find service|`Get-Service *win*`|
|Start service|`Start-Service servicename`|
|Stop service|`Stop-Service servicename`|
|Restart service|`Restart-Service servicename`|

```powershell
Get-Service
Get-Service *ssh*
Restart-Service Spooler
```

> [!TIP]  
> Services are background programs.  
> Example: Print Spooler, Windows Update, SSH server.

---

## 15. Network Commands

|Task|Command|
|---|---|
|Show IP config|`ipconfig`|
|Detailed IP config|`ipconfig /all`|
|Test connection|`Test-Connection google.com`|
|Ping alias|`ping google.com`|
|Check open port|`Test-NetConnection host -Port 80`|
|Show listening ports|`netstat -ano`|
|DNS lookup|`Resolve-DnsName google.com`|

```powershell
ipconfig
ipconfig /all
Test-Connection 8.8.8.8
Test-NetConnection google.com -Port 443
Resolve-DnsName example.com
netstat -ano
```

> [!IMPORTANT]  
> `Test-NetConnection` is very useful for checking whether a port is reachable.

---

## 16. System Information

|Task|Command|
|---|---|
|Computer info|`Get-ComputerInfo`|
|OS info|`systeminfo`|
|Current user|`whoami`|
|User groups|`whoami /groups`|
|Hostname|`hostname`|
|PowerShell version|`$PSVersionTable`|

```powershell
whoami
hostname
systeminfo
Get-ComputerInfo
$PSVersionTable
```

---

## 17. Users and Groups

|Task|Command|
|---|---|
|Current user|`whoami`|
|Local users|`Get-LocalUser`|
|Local groups|`Get-LocalGroup`|
|Group members|`Get-LocalGroupMember Administrators`|

```powershell
whoami
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember Administrators
```

> [!NOTE]  
> Some user/group commands require PowerShell to be opened as Administrator.

---

## 18. Running Commands as Admin

|Task|Command|
|---|---|
|Start PowerShell as admin|Right-click → Run as administrator|
|Start process as admin|`Start-Process powershell -Verb RunAs`|

```powershell
Start-Process powershell -Verb RunAs
```

> [!IMPORTANT]  
> Admin PowerShell gives higher privileges. Use it carefully.

---

## 19. Command History

|Task|Command|
|---|---|
|Show command history|`Get-History`|
|Run previous command by ID|`Invoke-History 5`|
|Clear history in session|`Clear-History`|

```powershell
Get-History
Invoke-History 3
Clear-History
```

> [!TIP]  
> Use the **Up Arrow** and **Down Arrow** keys to move through previous commands.

---

## 20. Help System

|Task|Command|
|---|---|
|Get help|`Get-Help command`|
|Examples only|`Get-Help command -Examples`|
|Full help|`Get-Help command -Full`|
|Online help|`Get-Help command -Online`|
|Find commands|`Get-Command *service*`|

```powershell
Get-Help Get-Process
Get-Help Get-Service -Examples
Get-Command *net*
```

> [!TIP]  
> When stuck, use:  
> `Get-Help CommandName -Examples`

---

## 21. Aliases

|Alias|Full Command|
|---|---|
|`ls`|`Get-ChildItem`|
|`dir`|`Get-ChildItem`|
|`cd`|`Set-Location`|
|`pwd`|`Get-Location`|
|`cat`|`Get-Content`|
|`echo`|`Write-Output`|
|`cls`|`Clear-Host`|
|`cp`|`Copy-Item`|
|`mv`|`Move-Item`|
|`rm`|`Remove-Item`|
|`ps`|`Get-Process`|

```powershell
Get-Alias
Get-Alias ls
```

---

## 22. Piping Commands

> [!NOTE]  
> The pipe `|` sends the output of one command into another command.

```powershell
Get-Process | Sort-Object CPU -Descending
ls | Where-Object {$_.Length -gt 1MB}
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Common pipeline tools:

|Command|Use|
|---|---|
|`Where-Object`|Filter|
|`Sort-Object`|Sort|
|`Select-Object`|Pick columns|
|`Format-Table`|Table output|
|`Format-List`|Detailed output|
|`Measure-Object`|Count/sum/average|

```powershell
Get-Process |
Sort-Object CPU -Descending |
Select-Object -First 10 Name, Id, CPU
```

---

## 23. Selecting Output

|Task|Command|
|---|---|
|Select columns|`Select-Object Name,Id`|
|First 5 items|`Select-Object -First 5`|
|Last 5 items|`Select-Object -Last 5`|
|Unique values|`Select-Object -Unique`|

```powershell
Get-Process | Select-Object Name, Id, CPU
Get-Process | Select-Object -First 10
ls | Select-Object Name, Length, LastWriteTime
```

---

## 24. Formatting Output

|Task|Command|
|---|---|
|Format as table|`Format-Table`|
|Format as list|`Format-List`|
|Auto-size table|`Format-Table -AutoSize`|

```powershell
Get-Process | Format-Table -AutoSize
Get-Service | Format-List
ls | Format-Table Name, Length, LastWriteTime -AutoSize
```

> [!CAUTION]  
> Use `Format-Table` mostly at the end of a command.  
> It changes the output into display text.

---

## 25. Exporting Output

|Task|Command|
|---|---|
|Export to text|`command > output.txt`|
|Export CSV|`Export-Csv file.csv -NoTypeInformation`|
|Export JSON|`ConvertTo-Json`|
|Save command output|`Out-File file.txt`|

```powershell
Get-Process > processes.txt

Get-Process |
Select-Object Name, Id, CPU |
Export-Csv processes.csv -NoTypeInformation

Get-Process |
Select-Object -First 5 |
ConvertTo-Json
```

---

## 26. Importing Data

|Task|Command|
|---|---|
|Import CSV|`Import-Csv file.csv`|
|Read JSON|`Get-Content file.json|

```powershell
$data = Import-Csv users.csv
$data

$json = Get-Content config.json | ConvertFrom-Json
$json
```

---

## 27. Downloading Files

|Task|Command|
|---|---|
|Download file|`Invoke-WebRequest URL -OutFile file`|
|Alias|`iwr URL -OutFile file`|

```powershell
Invoke-WebRequest https://example.com/file.zip -OutFile file.zip
```

> [!IMPORTANT]  
> Only download files from trusted sources.

---

## 28. Web Requests / APIs

|Task|Command|
|---|---|
|GET request|`Invoke-RestMethod URL`|
|Save response|`$r = Invoke-RestMethod URL`|
|POST JSON|`Invoke-RestMethod -Method Post -Body $body -ContentType "application/json"`|

```powershell
Invoke-RestMethod https://api.github.com

$body = @{
  name = "test"
} | ConvertTo-Json

Invoke-RestMethod `
  -Uri "https://example.com/api" `
  -Method Post `
  -Body $body `
  -ContentType "application/json"
```

---

## 29. Execution Policy

|Task|Command|
|---|---|
|Check policy|`Get-ExecutionPolicy`|
|Check all scopes|`Get-ExecutionPolicy -List`|
|Allow local scripts for user|`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`|

```powershell
Get-ExecutionPolicy
Get-ExecutionPolicy -List
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

> [!CAUTION]  
> Don’t blindly run scripts from the internet.  
> Read the script first.

---

## 30. Running Scripts

|Task|Command|
|---|---|
|Run script in current folder|`.\script.ps1`|
|Run with parameter|`.\script.ps1 -Name Tobias`|
|Run from full path|`C:\Scripts\script.ps1`|

```powershell
.\backup.ps1
.\scan.ps1 -Target localhost
```

> [!NOTE]  
> In PowerShell, you usually need `.\` before scripts in the current folder.

---

## 31. PowerShell Profiles

|Task|Command|
|---|---|
|Show profile path|`$PROFILE`|
|Check if profile exists|`Test-Path $PROFILE`|
|Create profile|`New-Item -ItemType File -Path $PROFILE -Force`|
|Open profile|`notepad $PROFILE`|

```powershell
$PROFILE
Test-Path $PROFILE
New-Item -ItemType File -Path $PROFILE -Force
notepad $PROFILE
```

> [!TIP]  
> Your PowerShell profile is like a startup config file.  
> You can put aliases, functions, and custom settings there.

---

## 32. Useful Keyboard Shortcuts

|Shortcut|Action|
|---|---|
|`Tab`|Auto-complete|
|`Ctrl + C`|Stop command|
|`Ctrl + L`|Clear screen|
|`Up Arrow`|Previous command|
|`Down Arrow`|Next command|
|`Ctrl + R`|Search command history|
|`Ctrl + Space`|Show completion menu|

---

## 33. Useful One-Liners

### Find largest files in current folder

```powershell
ls -Recurse -File |
Sort-Object Length -Descending |
Select-Object -First 10 Name, Length, FullName
```

### Find recently modified files

```powershell
ls -Recurse -File |
Sort-Object LastWriteTime -Descending |
Select-Object -First 10 Name, LastWriteTime, FullName
```

### Find running services

```powershell
Get-Service |
Where-Object {$_.Status -eq "Running"}
```

### Find process using most CPU

```powershell
Get-Process |
Sort-Object CPU -Descending |
Select-Object -First 10 Name, Id, CPU
```

### Check if a port is open

```powershell
Test-NetConnection example.com -Port 443
```

### Get public IP

```powershell
Invoke-RestMethod https://api.ipify.org
```

---

## 34. Common Mistakes

> [!WARNING]  
> **Mistake:** Running Linux commands exactly the same way in PowerShell.  
> PowerShell has aliases like `ls`, `cat`, and `rm`, but the behavior can be different.

> [!WARNING]  
> **Mistake:** Forgetting `.\` before scripts.  
> Correct:  
> `.\script.ps1`

> [!WARNING]  
> **Mistake:** Deleting with `Remove-Item -Recurse -Force` without checking path.

> [!WARNING]  
> **Mistake:** Using `Format-Table` before exporting data.  
> Export objects first, format only for display.

---

## 35. Must-Know Commands

```powershell
pwd
ls
cd
mkdir
New-Item
Copy-Item
Move-Item
Rename-Item
Remove-Item
Get-Content
Set-Content
Add-Content
Select-String
Get-Process
Stop-Process
Get-Service
Restart-Service
Get-Command
Get-Help
Test-NetConnection
Invoke-WebRequest
Invoke-RestMethod
Export-Csv
Import-Csv
```

> [!SUCCESS]  
> If you know these commands well, you can comfortably navigate, inspect files, manage processes, check network issues, and automate simple tasks in PowerShell.