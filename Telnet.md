Telnet is a network protocol that enables communication with remote devices over TCP/IP networks. While its use has declined due to security concerns, it remains valuable for network diagnostics and troubleshooting.

---

## 🛠️ **Basic Usage**

### 📌 **Establishing a Telnet Connection**

To initiate a Telnet session, open your command-line interface and ente:

```bash
telnet <hostname> <port>
```

If the port is omitted, Telnet defaults to port 2. citeturn0search1

---

## 🏷️ **Common Applications**

### 📌 *_Checking Open Ports_

Telnet can verify if a specific port on a server is oen:

````bash
telnet <server-IP> <port>
``


For instance, to check if port 22 is oen:

```bash
telnet 192.168.1.1 22
``


A successful connection indicates the port is oen. citeturn0search3

### 📌 **Testing Mail Server**

You can connect to mail servers to test their responsiveess:

```bash
telnet smtp.example.com 25
``


This command connects to an SMTP server on por 25. citeturn0search5

### 📌 **Retrieving Web Pags**

Telnet allows manual HTTP requests to web severs:

```bash
telnet www.example.com 80
GET / HTTP/1.1
Host: www.example.com
``


This retrieves the homepage's HTML sourcecode. citeturn0search5

---

## 🎛️ **Telnet Commads**

Within a Telnet session, several commands manage the connction:

- `open` o `o`: Establish a connection to  host.
- `close` o `c`: Terminate the current connction.
- `quit` o `q`: Exit the Telnet liet.

For a comprehensive list of commands, refer to Microsoft's Telnet documenation. citeturn0search2

---

## 🔒 **Security Consideratons**

Telnet transmits data, including credentials, in plaintext, making it susceptible to interetion. For secure communications, it's advisable to use SSH (Secure Shell) nstead.

---

## 📜 **Conclsion**

While Telnet's role has diminished due to security vulnerabilities, it remains a useful tool for specific network troubleshootigtasks. Exercise caution and consider more secure alternatives when appopriate. 
````