
Here's a **comprehensive `oledump.py` cheat sheet** — including its best use cases and tips for pairing it with **CyberChef** for deep document malware analysis.

---

## 📄 **`oledump.py` Cheat Sheet**

> Author: Didier Stevens | Tool for analyzing OLE/Office files (DOC, XLS, PPT, etc.) for embedded malicious macros, scripts, and objects.

GitHub: [DidierStevens/oledump](https://github.com/DidierStevens/DidierStevensSuite)

---

### ✅ **Basic Syntax**

```bash
oledump.py <file>
```

|Command|Description|
|---|---|
|`oledump.py suspicious.doc`|Dump streams inside the OLE file|
|`oledump.py -s <index> -d file.doc`|Dump raw stream content to stdout|
|`oledump.py -s <index> -v`|View stream content (decoded text)|
|`oledump.py -s <index> -a`|Analyze macros (disassemble VBS)|
|`oledump.py -V`|Enable verbose output|

---

### 📂 **Understanding Output Structure**

```
  1:       384 '\x01CompObj'
  2:      1243 '\x05DocumentSummaryInformation'
  3:      4096 'Macros/VBA/ThisDocument' M
  4:     11729 'Macros/VBA/_VBA_PROJECT' M
```

- `M` — Macro stream (contains VBA code)
    
- `A` — Auto-executable macro (autoopen, document_open)
    
- `\x01...` — Metadata/info streams
    

---

### 🔍 **Stream Operations**

|Option|Description|
|---|---|
|`-s <n>`|Select stream by index (e.g., `-s 3`)|
|`-d`|Dump raw stream data|
|`-v`|View stream with formatting|
|`-a`|Analyze/disassemble macro (if applicable)|
|`-i`|List all macro indicators (autoexec, shell)|
|`-t`|Check for text strings in stream|

---

### 🧠 **Common Use Cases**

#### 📌 1. **Find All Macros**

```bash
oledump.py suspicious.doc
```

Look for streams marked with `M`, especially ones with names like `ThisDocument`, `Module1`, etc.

---

#### 📌 2. **Dump Macro Code**

```bash
oledump.py suspicious.doc -s 3 -v
```

Shows readable macro code, often obfuscated.

---

#### 📌 3. **Analyze (Deobfuscate/Disassemble)**

```bash
oledump.py suspicious.doc -s 3 -a
```

Disassembles VBA to make logic/obfuscation visible.

---

#### 📌 4. **Autoexec Check**

```bash
oledump.py suspicious.doc -i
```

Finds auto-trigger macros like `AutoOpen`, `Document_Open`.

---

#### 📌 5. **Extract All Streams**

```bash
oledump.py suspicious.doc -s 3 -d > stream3.bin
```

Use for deeper inspection or CyberChef analysis.

---

### 🔄 **Working with CyberChef**

After extracting suspicious content with `oledump.py`, you can analyze in **CyberChef** using the following techniques:

|CyberChef Tool|Use After oledump Extraction|
|---|---|
|`From Base64`|Decode base64-obfuscated content|
|`From Charcode`|Reveal character-by-character obfuscation|
|`Regular Expression`|Extract URLs, IPs, commands|
|`Unescape JavaScript`|Handle `Chr(65)+Chr(66)` style obfuscation|
|`Entropy`|Spot encoded or encrypted payloads|
|`Strings`|Find readable text inside binary macros|
|`Magic`|Auto-suggest decoding operations|

---

### 🔐 **Detect Common Macro Behaviors**

Look for:

- `Shell`, `CreateObject`, `WScript.Shell`
    
- Obfuscated strings via `Chr`, `StrReverse`, or `Xor`
    
- Auto-start triggers: `AutoOpen`, `Workbook_Open`
    

---

### 📦 **Pro Tips**

|Tip|Benefit|
|---|---|
|Use `-s ALL -a` with a loop to auto-analyze all macro streams||
|Pipe output to CyberChef if you spot encoding or `Chr()` chains||
|Combine with `ViperMonkey` or `olevba` for deeper macro logic||
|Use `-t` to find text in all streams||

---

### 🔁 **Full Workflow: OLE → CyberChef**

```bash
oledump.py malware.doc -s 3 -v > macro.txt
```

Then in CyberChef:

1. Load `macro.txt`
    
2. Use `Extract Charcodes` → `From Charcode`
    
3. Decode obfuscation
    
4. Highlight URLs, Powershell, or dropper code
    

---

Would you like this as a printable PDF or combined with other analysis tool cheat sheets?