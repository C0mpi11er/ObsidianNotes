Here's a **CAPA (Malware Capability Analyzer)** cheat sheet — ideal for malware analysts using [FireEye/FLARE's capa tool](https://github.com/mandiant/capa) to identify behaviors in executable files (like PE, ELF, etc.).

---

## 🔎 **CAPA Cheat Sheet (FLARE CAPA Tool)**

> Static analyzer to identify capabilities in executable files using rules.

---

### 🧰 **Basic Usage**

```bash
capa <file>
```

|Command|Description|
|---|---|
|`capa malware.exe`|Analyze `malware.exe` and print detected capabilities|
|`capa -v malware.exe`|Verbose mode: show more detail|
|`capa -vv malware.exe`|Very verbose: dump all matching rule info|
|`capa -j malware.exe`|Output JSON format|
|`capa -r <rules-dir>`|Use custom rules directory|
|`capa -t`|Run rule tests (for developers)|
|`capa -q`|Quiet mode — no banners or info|

---

### 📂 **Rule Components**

CAPA rules are YAML files composed of:

|Field|Purpose|
|---|---|
|`meta`|Rule name, author, description, scope|
|`scope: function`|Common for detecting function-level behavior|
|`features`|API calls, strings, byte patterns, etc.|
|`matches`|Reference other rules|
|`examples`|File samples this rule works on|

---

### 🔍 **Common Feature Types**

|Feature Type|Example|Description|
|---|---|---|
|`api:`|`api: CreateFile`|Windows API call|
|`string:`|`string: "MZ"`|ASCII or Unicode string|
|`bytes:`|`bytes: 90 90 90`|Raw byte pattern|
|`mnemonic:`|`mnemonic: push`|Assembly instruction mnemonic|
|`number:`|`number: 0x1000`|Numeric constants (sizes, addresses)|
|`offset:`|`offset: 0x200`|File offset|
|`characteristic:`|`characteristic: tight loop`|Generic code behaviors|

---

### 🔁 **Compositional Logic**

|Logic|Syntax Example|Description|
|---|---|---|
|`and:`|All conditions must match||
|`or:`|At least one condition must match||
|`not:`|Inverts condition||
|`optional:`|Does not affect logic but adds context||
|`count:`|Minimum number of matches required|`count: 3` (must match 3+ times)|

---

### 🧠 **Common Capabilities Detected**

|Capability|Description|
|---|---|
|`create or write file`|Writes to disk|
|`create process`|Spawns a new process|
|`inject into process`|Code injection behavior|
|`encrypt data`|Uses crypto functions|
|`persist via registry`|Registry-based persistence|
|`capture keystrokes`|Keylogger behavior|
|`implement custom protocol`|C2 or encoding schemes|
|`anti-analysis`|Detects debuggers, VMs, or sandboxes|

---

### 📋 **Writing Your Own Rule**

Example rule: detect `WinExec` usage

```yaml
rule:
  meta:
    name: use winexec
    author: yourname
    description: detect use of WinExec
    scope: function
  features:
    - api: WinExec
```

---

### 🧪 **Testing Your Rule**

```bash
capa -t tests/
```

Use the `tests/` directory to test your rule against known samples.

---

### 🗂️ **Rule Repositories**

|Repository|Description|
|---|---|
|[capa-rules](https://github.com/mandiant/capa-rules)|Official, curated rule set|
|Custom repo via `-r` flag|Point to your own local rule folder|

---

### ⚙️ **Integration Tips**

- Use with IDA Pro or Ghidra via [CAPA Explorer](https://mandiant.github.io/capa/explorer/#/analysis).
    
- Combine with tools like **Ghidra**, **IDA**, **FLARE-VM**, **YARA**, or **VT**.
    
- Good for triaging malware families or discovering new behaviors in unknown binaries.
    

---

### 🧠 Pro Tip:

Use `capa -vv <file>` and pipe to `less` to deep dive into all rule matches and their context:

```bash
capa -vv malware.exe | less
```

---

Would you like a printable or PDF version of this cheat sheet?