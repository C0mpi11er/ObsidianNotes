
Here’s a **clean, structured Obsidian note** you can drop straight into your vault 👇

---

# 📌 DOM-Based Vulnerabilities (Obsidian Note)

## 🧠 What is the DOM?

- **DOM (Document Object Model)** = Browser’s hierarchical structure of a webpage.
    
- JavaScript interacts with the DOM to:
    
    - Modify elements
        
    - Change content
        
    - Handle user input
        

⚠️ DOM itself is safe — **insecure data handling is the problem**

---

## ⚠️ What are DOM-Based Vulnerabilities?

- Occur when **attacker-controlled data (source)** flows into a **dangerous function (sink)**.
    
- Happens entirely on the **client-side (browser)**.
    

---

## 🔄 Taint Flow Concept

### 📥 Source

- Input controlled by attacker
    

**Common Sources:**

```
location
document.URL
document.cookie
document.referrer
window.name
localStorage
sessionStorage
IndexedDB
```

---

### 📤 Sink

- Dangerous function that executes or processes data
    

**Examples:**

```
eval()
document.write()
innerHTML
window.location
setAttribute()
JSON.parse()
```

---

## ⚡ Core Idea

> DOM vulnerabilities = **Source → Sink (unsafe handling)**

---

## 💥 Example: DOM Open Redirect

```javascript
goto = location.hash.slice(1)
if (goto.startsWith('https:')) {
  location = goto;
}
```

### 🔓 Exploit:

```
https://victim.com/page#https://evil.com
```

➡️ Redirects victim to attacker site  
➡️ Useful for **phishing attacks**

---

## 🎯 Common DOM Vulnerabilities & Sinks

|Vulnerability|Sink|
|---|---|
|DOM XSS|document.write()|
|Open Redirect|window.location|
|Cookie Manipulation|document.cookie|
|JS Injection|eval()|
|Link Manipulation|element.src|
|Web Message Abuse|postMessage()|
|HTML Injection|innerHTML|
|Storage Abuse|sessionStorage.setItem()|
|JSON Injection|JSON.parse()|

---

## 🧬 Other Attack Sources

- Reflected input
    
- Stored data
    
- Web messages (`postMessage`)
    

---

## 🧱 Prevention Techniques

### ✅ 1. Avoid Direct Source → Sink Flow

- Never pass user input directly into sinks
    

---

### ✅ 2. Input Validation (Whitelist)

```javascript
if (input === "safe_value") { ... }
```

---

### ✅ 3. Output Encoding / Sanitization

- HTML encode
    
- URL encode
    
- JavaScript escape
    

⚠️ Must match the **context** of usage

---

### ✅ 4. Safe APIs Instead of Dangerous Ones

- Avoid:
    
    - `innerHTML`
        
    - `eval()`
        
- Prefer:
    
    - `textContent`
        
    - `setAttribute()` (carefully)
        

---

## 🧨 DOM Clobbering

- Advanced attack technique
    
- Inject HTML to **override JS variables**
    

### 🧪 Example:

```html
<a name="config" href="malicious.js"></a>
```

➡️ Overrides global variable `config`  
➡️ App loads malicious script

---

## 🧠 Key Takeaways

- DOM vulns are **client-side**
    
- Always think in:
    
    ```
    Source → Flow → Sink
    ```
    
- URL is the **most common attack vector**
    
- Prevention = **validation + encoding + safe coding**
    

---
