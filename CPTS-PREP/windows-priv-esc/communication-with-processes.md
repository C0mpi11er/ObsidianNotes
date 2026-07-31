# 🛰️ Process Communication Analysis

## 🔍 Introduction to Process Communication

### Overview of Inter-Process Communication (IPC)
[!INFO] Inter-process communication (IPC) is essential for services and applications to interact within a system. Common IPC methods include named pipes, sockets, and shared memory. This analysis focuses on identifying insecure configurations that could lead to privilege escalation.

## 🖥️ Network Services

### List All Listening Services
```cmd
# Look for active listening ports
netstat -ano | findstr LISTENING
```

#### Filter Specific Ports
```cmd
# Focus on common application ports
netstat -ano | findstr :80
netstat -ano | findstr :443

# Identify services running as SYSTEM or other privileged users
tasklist /svc
```

### Service-to-Process Mapping
[!CHECK] Use `tasklist` to identify the process name from its PID.

```cmd
# Example workflow:
netstat -ano | findstr :80  # Find PID listening on port 80
tasklist | findstr "3142"   # Identify process name
```

## 🔄 Named Pipes

### Enumerate All Named Pipes
[!CHECK] Use `pipelist.exe` or PowerShell to enumerate named pipes.

```cmd
# List all named pipes using pipelist (Sysinternals)
pipelist.exe /accepteula

# Alternative using PowerShell
Get-ChildItem \\.\pipe\
```

#### Check Pipe Permissions with AccessChk
[!CHECK] Identify dangerous permissions such as `WRITE_DAC` or excessive access for the Everyone group.

```cmd
accesschk.exe -accepteula -w \pipe\SQLLocal\SQLEXPRESS01 -v
```

## 🚨 Common Attack Vectors

### Web Server Exploitation
**Scenario**: Identify a web server running as SYSTEM and deploy a shell.

```cmd
# 1. Find the PID listening on port 80 or 8080
netstat -ano | findstr :80
tasklist | findstr "[PID]"

# 2. Deploy a reverse shell if writable directories exist
```

### FileZilla Server Attack
**Scenario**: Exploit the FileZilla admin interface on localhost:14147.

```cmd
# 1. Identify PID listening on port 14147
netstat -ano | findstr :14147

# 2. Connect to the admin interface and extract credentials.
```

### Splunk Universal Forwarder Attack
**Scenario**: Exploit default configuration without authentication.

```cmd
# 1. Check for Splunk service running as SYSTEM
tasklist /svc | findstr splunk

# 2. Deploy malicious applications if writable directories exist
```

### Named Pipe Privilege Escalation
**Example**: WindscribeService vulnerability exploitation.

```cmd
# 1. Find vulnerable pipe with excessive permissions
accesschk.exe -w \pipe\WindscribeService -v

# 2. Exploit the pipe for privilege escalation.
```

## 🎯 HTB Academy Lab Solutions

### Question 1: Service on Port 21
**Objective**: Identify service listening on 0.0.0.0:21.

```cmd
# Connect via RDP
xfreerdp /v:10.129.43.43 /u:htb-student /p:HTB_@cademy_stdnt!

# Find PID listening on port 21
netstat -ano | findstr :21

# Identify process by PID
tasklist | findstr "[PID]"
```

**Answer**: `filezilla server`.

### Question 2: WRITE_DAC Privileges on Named Pipe
**Objective**: Find account with WRITE_DAC over `\pipe\SQLLocal\SQLEXPRESS01`.

```cmd
# Check named pipe permissions
accesschk.exe -accepteula -w \pipe\SQLLocal\SQLEXPRESS01 -v

# Analyze output for WRITE_DAC privilege
```

**Answer**: `NT SERVICE\MSSQL$SQLEXPRESS01`.

## 🔍 Attack Pattern Recognition

### Network Service Indicators
[!WARNING] 
- Port 8080: Tomcat, development servers.
- Port 9090: Administrative interfaces.
- Port 10000+: Custom applications.
- Localhost-only services are insecure by design.

### Named Pipe Red Flags
[!DANGER]
- Everyone group with WRITE_DAC.
- Pipes with FILE_ALL_ACCESS permissions.
- Custom application pipes named suspiciously.

### Service Context Analysis
[!SUCCESS] 
- SYSTEM: Highest privileges.
- NT AUTHORITY\SYSTEM: System-level access.
- Administrator: Admin privileges.
- Service accounts often overprivileged.

## 📋 Process Communication Checklist

### Network Services
- [ ] **Active connections** (`netstat -ano`).
- [ ] **Localhost services** (127.0.0.1 binding).
- [ ] **Process identification** (`tasklist`).
- [ ] **Service context** (user running service).
- [ ] **Web server detection** (port 80, 8080, 8443).
- [ ] **Administrative interfaces** (non-standard ports).

### Named Pipes
- [ ] **Pipe enumeration** (`pipelist.exe` or `Get-ChildItem \\.\pipe\`).
- [ ] **Permission analysis** (`accesschk.exe -w \pipe\*`).
- [ ] **Everyone group access** (overly permissive pipes).
- [ ] **Custom application pipes** (non-standard names).
- [ ] **WRITE_DAC privileges** (permission modification).

### Attack Surface Assessment
- [ ] **SeImpersonatePrivilege** detection.
- [ ] **Vulnerable service versions**.
- [ ] **Default configurations** (Splunk, FileZilla).
- [ ] **File upload capabilities** (web servers).
- [ ] **Administrative access** (localhost services).

## 💡 Key Takeaways

1. **Network services running as privileged users provide direct escalation paths**.
2. **Localhost-only services often lack security controls**.
3. **Named pipes with excessive permissions enable privilege escalation**.
4. **Web servers with SeImpersonatePrivilege lead to SYSTEM access**.
5. **Default configurations frequently contain security weaknesses**.
6. **Service context matters - identify which user runs each service**.

---

*Process communication analysis reveals privilege escalation opportunities through network services and inter-process communication vulnerabilities.*