Awesome! Here's your **Ultimate PowerShell Cheat Sheet** – all sections combined into one clean, comprehensive reference covering:

✅ File/Folder Management  
✅ Content Reading  
✅ Searching, Sorting, Filtering  
✅ System & Network Utilities  
✅ Scripting Essentials  
✅ Piping + Pattern Matching  
✅ Exporting & Grouping  
✅ Aliases & Quick Tips

---

# ⚡ Ultimate PowerShell Cheat Sheet

---

## 📁 File & Folder Management

|Task|Command|
|---|---|
|List files in directory|`Get-ChildItem` or `ls`|
|List files recursively|`Get-ChildItem -Recurse`|
|List only files|`Get-ChildItem -File`|
|List only folders|`Get-ChildItem -Directory`|
|Create folder|`New-Item -Path "NewFolder" -ItemType Directory`|
|Create file|`New-Item -Path "file.txt" -ItemType File`|
|Copy item|`Copy-Item -Path "source" -Destination "dest"`|
|Move item|`Move-Item -Path "source" -Destination "dest"`|
|Rename item|`Rename-Item -Path "old.txt" -NewName "new.txt"`|
|Delete file/folder|`Remove-Item "path"`|

---

## 📂 Reading File Content

|Task|Command|
|---|---|
|Read file content|`Get-Content "file.txt"` or `gc file.txt`|
|Read last 10 lines|`Get-Content file.txt -Tail 10`|
|Monitor file (tail -f)|`Get-Content file.txt -Wait`|
|Read full file as string|`Get-Content file.txt -Raw`|

---

## 🔎 Search, Filtering & Pattern Matching

|Task|Command|
|---|---|
|Search inside files|`Select-String -Path "*.txt" -Pattern "keyword"`|
|Case-sensitive search|`Select-String -Pattern "Warning" -CaseSensitive`|
|Match wildcard pattern|`Where-Object { $_.Name -like "*config*" }`|
|Regex match|`Where-Object { $_.Name -match "^log.*\.txt$" }`|

---

## 📊 Sorting & Filtering

|Task|Command|
|---|---|
|Sort by name|`Sort-Object Name`|
|Sort by size (asc)|`Sort-Object Length`|
|Sort by size (desc)|`Sort-Object Length -Descending`|
|Filter by extension|`Where-Object { $_.Extension -eq ".log" }`|
|Filter by size|`Where-Object { $_.Length -gt 10KB }`|

---

## 🔗 Piping & Combining

```powershell
Get-ChildItem -Recurse -File |
Where-Object { $_.Extension -eq ".log" -and $_.Length -gt 1MB } |
Sort-Object Length -Descending
```

### Count file types:

```powershell
Get-ChildItem -Recurse -File | Group-Object Extension | Sort-Object Count -Descending
```

---

## 📜 Scripting Essentials

|Task|Command|
|---|---|
|Define variable|`$name = "PowerShell"`|
|If statement|`if ($a -eq $b) { "Equal" }`|
|ForEach loop|`foreach ($item in $list) { ... }`|
|While loop|`while ($i -lt 10) { ... }`|
|Function|`function Greet { Write-Output "Hi!" }`|

---

## 📡 Network & System Commands

|Task|Command|
|---|---|
|Ping site|`Test-Connection google.com`|
|DNS lookup|`Resolve-DnsName example.com`|
|IP configuration|`Get-NetIPAddress`|
|Open ports|`Get-NetTCPConnection`|
|Get processes|`Get-Process`|
|Kill a process|`Stop-Process -Name "notepad"`|
|Get system info|`Get-ComputerInfo`|

---

## 👤 User & Environment

|Task|Command|
|---|---|
|Current user|`[System.Security.Principal.WindowsIdentity]::GetCurrent().Name`|
|List users|`Get-LocalUser`|
|List groups|`Get-LocalGroup`|
|Check admin rights|`([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)`|
|Env variables|`Get-ChildItem Env:`|

---

## 💾 Output & Exporting

|Task|Command|
|---|---|
|Export to text|`Get-Process|
|Export to CSV|`Get-Process|
|Export to JSON|`Get-Process|

---

## 🧹 Misc Essentials

|Task|Command|
|---|---|
|Clear screen|`Clear-Host` or `cls`|
|View command history|`Get-History`|
|Repeat last command|`Invoke-History`|
|Get help|`Get-Help Command`|
|Update help files|`Update-Help`|

---

## ⚡ Popular Aliases

|Alias|Full Command|
|---|---|
|`ls`|`Get-ChildItem`|
|`cd`|`Set-Location`|
|`gc`|`Get-Content`|
|`gci`|`Get-ChildItem`|
|`rm`|`Remove-Item`|
|`cp`|`Copy-Item`|
|`mv`|`Move-Item`|
|`ni`|`New-Item`|
|`sort`|`Sort-Object`|

---

## 🧪 Full Example – Real-world Pipeline

```powershell
Get-ChildItem -Recurse -Filter *.ps1 -File |
Where-Object { $_.Length -gt 10KB } |
Sort-Object Length -Descending |
Select-Object Name, Length, LastWriteTime |
Export-Csv -Path "script_report.csv" -NoTypeInformation
```

---

Would you like me to:

- Export this as a **PDF** or **Markdown file**?
    
- Add syntax highlighting?
    
- Create a **print-friendly one-pager**?
    

Let me know how you'd like to use it!