


## 📌 What is Laudanum?

> [!NOTE]  
> **Laudanum** is a collection of prebuilt webshells used in penetration testing

It allows you to:

- Execute commands on a target
    
- Get reverse shells
    
- Control a system via browser
    

Supports multiple languages:

- PHP
    
- ASP / ASPX
    
- JSP
    
- and more
    

> [!IMPORTANT]  
> It’s a **standard toolkit** for web exploitation

---

## 📂 Location (Kali / Parrot)

```bash
/usr/share/laudanum
```

> [!TIP]  
> Already installed on Kali & Parrot  
> Other distros → download manually

---

## ⚙️ Preparing a Webshell

### 1. Copy shell to working directory

```bash
cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```

---

### 2. Modify the shell

> [!IMPORTANT]  
> You MUST edit the file before use

Key changes:

- Add your attacker IP to:
    
    ```text
    allowedIps
    ```
    
- Remove:
    
    - Comments
        
    - ASCII art
        

> [!WARNING]  
> Extra content can trigger AV/signatures

---

## 🎯 Exploitation Process

### Step 1: Find upload functionality

> [!NOTE]  
> Look for:

- File upload forms
    
- Import config features
    
- Any place files can be uploaded
    

---

### Step 2: Upload your webshell

- Select modified shell
    
- Click upload
    

> [!SUCCESS]  
> If successful → server shows file path

---

### Step 3: Locate uploaded file

Example path:

```text
status.inlanefreight.local/files/demo.aspx
```

> [!IMPORTANT]  
> Sometimes:

- Filenames change
    
- Directory is hidden
    
- Access is restricted
    

---

### Step 4: Access the shell

Open in browser:

```text
http://status.inlanefreight.local/files/demo.aspx
```

---

## 💻 Using the Webshell

> [!NOTE]  
> You get a command interface in the browser

Example:

```cmd
systeminfo
```

> [!SUCCESS]  
> You now have **remote command execution (RCE)**

---

## ⚠️ Important Details

> [!WARNING]  
> Upload behavior varies:

- Some apps randomize filenames
    
- Some restrict execution
    
- Some hide directories
    

---

## 🧠 Key Concepts

> [!IMPORTANT]  
> Webshell = **bridge between attacker and server**

```text
Upload → Access → Execute commands → Gain control
```

---

## ⚡ Quick Flow

```text
Copy shell → Modify IP → Upload → Find path → Execute commands
```

---

## 🧠 Takeaways

> [!SUCCESS]
> 
> - Laudanum simplifies web exploitation
>     
> - Upload functionality = major vulnerability
>     
> - Always modify payload before use
>     
> - Webshell gives immediate command execution
>     

---

If you want next:

👉 I can give you a **webshell vs reverse shell vs bind shell breakdown**  
👉 or a **real-world detection & evasion tips (how defenders catch this)**



> [!NOTE] Web Shells to Check out 
> https://github.com/WhiteWinterWolf/wwwolf-php-webshell/blob/master/webshell.php 
>https://github.com/samratashok/nishang/tree/master


