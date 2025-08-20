Here's a **comprehensive CyberChef cheat sheet** — ideal for quick reference during forensic analysis, reverse engineering, penetration testing, or malware analysis:

---

## 🧠 **CyberChef Cheat Sheet**

> A developer's and hacker's swiss army knife for data encoding, decoding, encryption, and analysis.

---

### 🔧 **General Navigation**

|Shortcut / Action|Description|
|---|---|
|`Operations` panel (left)|Drag and drop operations|
|`Recipe` panel (middle)|Sequence of actions applied to input|
|`Input` panel (top-right)|Raw input (hex, text, binary, etc.)|
|`Output` panel (bottom-right)|Results of the recipe|

---

### 📂 **Common Operations by Category**

#### 🔡 **Text & Encoding**

|Operation|Purpose|
|---|---|
|`From Base64` / `To Base64`|Decode/encode Base64|
|`From Hex` / `To Hex`|Convert between hex and text|
|`From Binary` / `To Binary`|Convert between binary and text|
|`From Charcode` / `To Charcode`|Char to ASCII/Unicode|
|`URL Decode` / `URL Encode`|Decode/encode URL encoding|

---

#### 🔐 **Cryptography**

|Operation|Description|
|---|---|
|`MD5`, `SHA1`, `SHA256`|Hashing algorithms|
|`AES Decrypt` / `AES Encrypt`|Symmetric encryption (Key, IV)|
|`DES`, `TripleDES`|Older symmetric encryption methods|
|`XOR`|XOR against single byte, string, or key|
|`HMAC`|Compute HMAC using MD5, SHA variants|

---

#### 🔍 **Analysis & Extraction**

|Operation|Purpose|
|---|---|
|`Entropy`|Detect randomness (e.g., encrypted data)|
|`Extract Files`|Extract embedded files (PDF, PNG, etc.)|
|`Find/Replace`|Search & replace regex/text|
|`Strings`|Extract readable strings|
|`Highlight Matches`|Visual pattern identification|

---

#### 🧪 **Data Manipulation**

|Operation|Description|
|---|---|
|`Remove null bytes`|Cleans up raw data|
|`Strip HTTP headers`|Useful when working with raw HTTP|
|`Merge` / `Split`|Combine or separate data chunks|
|`Fork`|Create branches for parallel decoding|

---

#### 🧱 **Encoding Layers (Steganography & Obfuscation)**

|Operation|Description|
|---|---|
|`Magic`|Auto-suggest decoding operations|
|`Regular Expression`|Extract complex patterns|
|`Remove Whitespace`|Clean formatting-based encoding|
|`Unescape JavaScript`|Decode obfuscated JS code|

---

### 📦 **Compression & Decompression**

|Operation|Description|
|---|---|
|`Gzip`, `Gunzip`|Compress/decompress Gzip|
|`Deflate`, `Inflate`|Raw compression methods|
|`Bzip2`, `XZ`|Archive formats|
|`ZIP Extract`|Extract files from zip|

---

### 🧮 **Numbers & Arithmetic**

|Operation|Description|
|---|---|
|`To / From Base`|Convert between base 2–64|
|`Add`, `Subtract`, `Multiply`|Basic arithmetic|
|`Modulus`, `Bitwise Ops`|Low-level math operations|

---

### 🗺️ **Location & Time**

|Operation|Description|
|---|---|
|`Parse DateTime`|Converts timestamps (UNIX, ISO, etc.)|
|`UNIX Timestamp`|Convert to/from epoch time|
|`Timestamp Decoder`|Detects and converts timestamps|

---

### 💻 **Code & Scripts**

|Operation|Description|
|---|---|
|`Disassemble x86`|Basic reverse engineering|
|`JavaScript Beautify/Uglify`|Format JS code|
|`Extract URLs/Emails`|Quickly pull common indicators|
|`HTML Entity Decode`|Convert `&#xAB;` and `&amp;` types|

---

### 🚀 **Popular Recipes (Quick Combos)**

|Name|Recipe Example|
|---|---|
|**Base64 XOR decode**|`From Base64` → `XOR` with key|
|**Deobfuscate JS**|`Unescape JavaScript` → `Beautify JavaScript`|
|**Extract URLs**|`Strings` → `Extract URLs`|
|**Decode Web Token (JWT)**|`From Base64` (split JWT by dot `.` and decode parts)|
|**Password hash cracking**|`From Hash` → Try matching using known wordlists manually|

---

### 🧰 **Tips & Tricks**

- Use **Magic** to auto-suggest decoding operations.
    
- Use the **search bar** to quickly find operations.
    
- Add **comments** in recipes to document your steps.
    
- Use **forks** for trying multiple decoding paths.
    
- Input formats: `text`, `hex`, `base64`, `file upload`.
    

---

Would you like a downloadable PDF version or a printable format of this cheat sheet?