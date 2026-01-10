### 📌 What is Shellcode?

Shellcode is a **small sequence of crafted machine instructions** injected into a vulnerable program to:

- Alter execution flow
    
- Execute attacker-controlled code
    
- Often spawn a shell or reverse shell
    

It is typically written in **Assembly**, then converted to **hexadecimal opcodes**.

---

### 🎯 Why Custom Shellcode?

- Evades signature-based AV detection
    
- More flexible and stealthy
    
- Requires strong low-level expertise
    

---

### 🧩 Skills Required

- x86 / x64 CPU architecture
    
- Assembly language
    
- C programming
    
- Linux & Windows internals
    

---

## 🐧 Linux x64 Shellcode Example (sys_write + sys_exit)

### 🔹 Objective

Print the string **"THM, Rocks!"** and exit cleanly.

---

### 🔧 Linux x64 Syscall Convention

|Register|Purpose|
|---|---|
|`rax`|Syscall number|
|`rdi`|1st argument|
|`rsi`|2nd argument|
|`rdx`|3rd argument|

#### Relevant Syscalls

|Syscall|`rax`|Description|
|---|---|---|
|`sys_write`|`0x1`|Write to file descriptor|
|`sys_exit`|`0x3c`|Exit program|

---

### 🧪 Assembly Logic (High-Level)

- Use **JMP–CALL–POP** technique to reference string
    
- Load registers for `sys_write`
    
- Execute `sys_exit` after output
    

---

### 🛠️ Build Process

#### Assemble & Link

`nasm -f elf64 thm.asm ld thm.o -o thm ./thm`

---

## 🔍 Extracting Shellcode

### 1️⃣ Disassemble Binary

`objdump -d thm`

### 2️⃣ Extract `.text` Section

`objcopy -j .text -O binary thm thm.text`

### 3️⃣ Convert to Hex (C-style)

`xxd -i thm.text`

Result: raw **hex shellcode bytes** ready for injection.

---

## 🧬 Shellcode Validation (C Injection)

### Key Technique

- Store shellcode in a byte array
    
- Cast to function pointer
    
- Disable NX protection
    

#### Compile with Executable Stack

`gcc -g -Wall -z execstack thm.c -o thmx ./thmx`

✅ Output confirms shellcode execution.

---

## ⚠️ Important Notes

- NX (No-eXecute) protection must be disabled
    
- Shellcode must be **position-independent**
    
- Encoding & encryption often required for real exploits
    

---

## 🎓 Key Takeaway

Understanding **how shellcode is written, extracted, and executed** is foundational for:

- Exploit development
    
- Payload encoding
    
- Advanced post-exploitation techniques