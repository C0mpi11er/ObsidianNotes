how do prototype pollution vulnerabilities arise?

Prototype pollution vulnerabilities typically arise when a JavaScript function recursively merges an object containing user-controllable properties into an existing object, without first sanitizing the keys. This can allow an attacker to inject a property with a key like __proto__, along with arbitrary nested properties.

Due to the special meaning of __proto__ in a JavaScript context, the merge operation may assign the nested properties to the object's prototype instead of the target object itself. As a result, the attacker can pollute the prototype with properties containing harmful values, which may subsequently be used by the application in a dangerous way.

It's possible to pollute any prototype object, but this most commonly occurs with the built-in global Object.prototype. 




How do prototype pollution vulnerabilities arise
= 
Successful exploitation of prototype pollution requires the following key components:

    A prototype pollution source - This is any input that enables you to poison prototype objects with arbitrary properties.

    A sink - In other words, a JavaScript function or DOM element that enables arbitrary code execution.

    An exploitable gadget - This is any property that is passed into a sink without proper filtering or sanitization.


Prototype pollution sources
=
A prototype pollution source is any user-controllable input that enables you to add arbitrary properties to prototype objects. The most common sources are as follows:

    The URL via either the query or fragment string (hash)

    JSON-based input

    Web messages



## 🧪 Prototype Pollution via URL

### 📌 Concept

Prototype pollution occurs when an attacker injects properties into `Object.prototype`, affecting **all objects** in a JavaScript runtime.

---

### 🌐 Attack Vector (URL)

https://vulnerable-website.com/?__proto__[evilProperty]=payload

---

### ⚙️ Expected Behavior (Incorrect Assumption)

Developers assume query params merge like:

{  
  existingProperty1: 'foo',  
  existingProperty2: 'bar',  
  __proto__: {  
    evilProperty: 'payload'  
  }  
}

---

### ⚠️ Actual Behavior

- `__proto__` is **special in JavaScript**
- It acts as a **getter/setter for the prototype**
- During merge:

targetObject.__proto__.evilProperty = 'payload';

---

### 💥 Result

- `evilProperty` is added to:

Object.prototype

- Therefore:

({}).evilProperty === 'payload' // true

---

### 🚨 Impact

All objects inherit polluted properties → can lead to:

- 🔓 Authentication bypass
- ⬆️ Privilege escalation
- 🧠 Logic manipulation
- 💣 Remote Code Execution (in some cases)


## 🧪 Prototype Pollution via JSON Input

### 📌 Concept

Prototype pollution can also occur when user input is supplied as **JSON** and parsed using:

JSON.parse()

---

### 🌐 Attack Vector (JSON Input)

{  
  "__proto__": {  
    "evilProperty": "payload"  
  }  
}

---

### ⚙️ Key Insight

Unlike object literals, `JSON.parse()` treats `__proto__` as a **normal string key**, not a prototype setter.

---

### 🔍 Behavior Comparison

const objectLiteral = {__proto__: {evilProperty: 'payload'}};  
const objectFromJson = JSON.parse('{"__proto__": {"evilProperty": "payload"}}');  
  
objectLiteral.hasOwnProperty('__proto__');     // false  
objectFromJson.hasOwnProperty('__proto__');    // true

#### ✅ Explanation:

- **Object literal** → `__proto__` modifies prototype directly
- **JSON.parse()** → `__proto__` is just a normal property

---

### ⚠️ Where the Vulnerability Happens

The danger arises **after parsing**, during unsafe object merging:

merge(targetObject, objectFromJson);

During merge:

targetObject.__proto__.evilProperty = 'payload';

---

### 💥 Result

- Prototype gets polluted:

Object.prototype.evilProperty = 'payload';

- All objects now inherit:

({}).evilProperty === 'payload' // true

---

### 🚨 Impact

Same as other prototype pollution vectors:

- 🔓 Authentication bypass
- ⬆️ Privilege escalation
- 🧠 Application logic corruption
- 💣 Potential RCE (depending on sink)

---

### 🎯 Real Exploitation Payloads

{"__proto__": {"isAdmin": true}}  
{"__proto__": {"role": "admin"}}  
{"__proto__": {"authenticated": true}}


