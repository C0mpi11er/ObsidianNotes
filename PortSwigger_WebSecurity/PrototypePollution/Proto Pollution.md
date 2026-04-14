
This update streamlines your **Obsidian** note using PortSwigger's core learning path data. It focuses on the three main attack surfaces: **Client-side**, **Server-side (Node.js)**, and **Bypassing Protections**.

---

# 🧪 Prototype Pollution Master Note

> [!ABSTRACT] Definition
> 
> Prototype pollution occurs when an application merges user-controlled input into an object without sanitization, allowing an attacker to modify `Object.prototype`.

---

## 1. Vulnerability Core: The "Why"

Vulnerabilities arise primarily from **recursive merge** functions. If the logic doesn't filter keys, an attacker can inject `__proto__`.

### The Three Components

1. **Source**: Controllable input (URL, JSON, Web Messages).
    
2. **Sink**: A function that executes code (e.g., `eval()`, `innerHTML`, `child_process.spawn()`).
    
3. **Gadget**: A property passed to a sink that is _undefined_ on the object but _defined_ on the polluted prototype.
    

---

## 2. Attack Vectors & Payloads

### 🌐 Client-Side (DOM XSS)

Targeting property fallback in the browser.

- **Standard**: `/?__proto__[transport_url]=data:,alert(1);`
    
- **Constructor Bypass**: `/?constructor[prototype][transport_url]=data:,alert(1);`
    

### 🖥️ Server-Side (Node.js RCE)

Targeting environment variables or command arguments.

- **Payload**:
    
    JSON
    
    ```
    {
      "__proto__": {
        "shell":"node",
        "NODE_OPTIONS":"--inspect-brk"
      }
    }
    ```
    

---

## 3. Bypassing Common Defenses

> [!WARNING] Defensive Bypass Techniques
> 
> Developers often try to block the string `__proto__`. Use these alternatives:

- **The Constructor Link**: If `__proto__` is blocked, use `obj.constructor.prototype`.
    
- **Flawed Sanitization**: If the app strips `__proto__` only once, use nested pollution:
    
    - `/?__pro__proto__to__[foo]=bar`
        
- **JSON Encoding**: Some filters miss URL-encoded or double-encoded characters:
    
    - `%5f%5fproto%5f%5f`
        

---

## 4. Modern Analysis with Burp Suite

> [!TIP] DOM Invader Workflow
> 
> 1. Open Burp's browser and enable **DOM Invader**.
>     
> 2. Enable **Prototype Pollution** in the settings.
>     
> 3. Click **"Scan for gadgets"**.
>     
> 4. It will automatically test properties and identify which ones hit a "Sink."
>     

---

## 5. Prevention (The "Hardening" Layer)

> [!CHECK] Secure Coding
> 
> - **Prototypeless Objects**: `const obj = Object.create(null);`
>     
> - **Freezing**: `Object.freeze(Object.prototype);`
>     
> - **Input Validation**: Use a schema (like Joi or Ajv) to strip sensitive keys.
>     
> - **Map Data Type**: Use `new Map()` instead of `{}` for dynamic key-value storage.
>     

---

### 📝 Quick Reference Table

|**Target**|**Technique**|**Common Property**|
|---|---|---|
|**XSS**|Client-side Sink|`transport_url`, `callback`, `template`|
|**RCE**|Node.js Environment|`NODE_OPTIONS`, `env`, `shell`|
|**Auth Bypass**|Logic Flaw|`isAdmin`, `isStaff`, `authorized`|
|**Bypass**|Constructor Vector|`constructor[prototype][foo]`|

---

**Obsidian Organization Note:** Link this to your `02_VULNS/Web` and `04_RESOURCES/PortSwigger` folders.