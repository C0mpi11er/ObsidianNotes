## ⚙️ Why Use Public Shellcode Generators?

### ✅ Advantages

- No need for Assembly expertise
    
- Fast payload generation
    
- Compatible with multiple languages and formats
    
- Built-in support from popular C2 frameworks
    

### ❌ Disadvantages

- Payloads are **well-known**
    
- Easily detected by AV/EDR solutions
    
- Limited stealth without encoding or encryption
    

---

## 🎯 Use Case Example

**Objective:** Generate Windows shellcode to execute `calc.exe`  
**Purpose:** Proof of concept for shellcode execution

---

## 🛠️ Generating Shellcode with `msfvenom`

### 🔹 C-Formatted Shellcode (x86 Windows)

`msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -f c`

- Architecture: x86
    
- Platform: Windows
    
- Payload: `windows/exec`
    
- Output format: C array
    

✔ Result: Shellcode that launches **Windows Calculator**

---

## 💉 Shellcode Injection (Windows)

### 🔹 Injection Concept

Shellcode is injected into:

- A running process, or
    
- A newly created thread
    

The execution flow is modified to run attacker-controlled code.

---

### 🔹 C Loader for Shellcode Execution

Key steps:

1. Store shellcode in a byte array
    
2. Use `VirtualProtect()` to mark memory as executable
    
3. Cast shellcode to a function pointer
    
4. Execute it
    

---

## 🧱 Compiling the Payload (Windows EXE)

### 🔹 Cross-Compile from Linux

`i686-w64-mingw32-gcc calc.c -o calc-MSF.exe`

✔ Produces a Windows executable containing injected shellcode

---

## 📤 Payload Transfer to Windows

### 🔹 Using SMB

`smbclient -U thm '//10.65.156.37/Tools' put calc-MSF.exe`

- File copied to: `C:\Tools\`
    
- AV must be disabled for execution
    

---

## ✅ Execution Result

- Running the EXE spawns **calc.exe**
    
- Confirms successful shellcode execution
    

---

## 📦 Generating Raw Shellcode (`.bin`)

### 🔹 Create Raw Binary Shellcode

`msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -f raw > example.bin`

- Output format: raw binary
    
- Common format used by C2 frameworks
    

---

## 🔄 Converting Raw Shellcode to C Format

### 🔹 Using `xxd`

`xxd -i example.bin`

✔ Outputs:

- Hexadecimal byte array
    
- Ready for direct use in C programs
    

---

## 🔎 Validation

- Raw `.bin` shellcode matches the previously generated C-format shellcode
    
- Confirms consistency across formats
    

---

## 🧠 Key Takeaways

- `msfvenom` is a powerful shellcode generator
    
- Supports multiple payloads, formats, and platforms
    
- Public shellcode is **fast but detectable**
    
- Ideal for learning, PoC, and controlled lab environments
    
- Advanced use requires **encoding, encryption, or custom loaders**
    

---

## 📌 Next Steps

- Experiment with:
    
    - Meterpreter shellcode
        
    - Encoders (`-e`)
        
    - Polymorphic payloads
        
    - In-memory execution techniques