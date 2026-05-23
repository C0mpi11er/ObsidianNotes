


---

# 🌐 IP Addresses

> [!info] Overview  
> Every device on a network is identified using a **MAC address** at the local level and an **IP address** at the network level.  
> MAC works inside a LAN, while IP enables communication across different networks (including the Internet).

---

## 🧭 MAC vs IP Concept

> [!note]
> 
> - **MAC Address** → identifies a device within the same local network
>     
> - **IP Address** → identifies a device across different networks
>     

### 📌 Analogy

> [!example]
> 
> - IP address = building address + district
>     
> - MAC address = exact apartment location
>     

---

## 🌍 IP Addressing Concept

> [!important]  
> IP addresses ensure data reaches the correct destination across networks.

- IP = Network + Host identification
    
- Works for both small LANs and the entire Internet
    
- Each IP must be unique within a network
    

---

## 📦 IPv4 Structure

> [!info]  
> IPv4 is a **32-bit address system** divided into 4 octets (8 bits each).

Format:

```
192.168.10.39
```

Binary representation:

```
11000000.10101000.00001010.00100111
```

### 🧠 Key Facts

- 4 octets × 8 bits = 32 bits
    
- Range per octet: 0–255
    
- Total addresses: ~4.29 billion
    

---

## 🏗️ Network & Host Structure

> [!note]  
> An IP address is split into:

- **Network portion** → identifies network
    
- **Host portion** → identifies device inside network
    

Control is defined by the subnet mask.

---

## 🧱 IPv4 Address Classes (Legacy System)

> [!warning]  
> Class-based addressing is outdated but still useful for understanding structure.

|Class|Range|Subnet Mask|CIDR|Use|
|---|---|---|---|---|
|A|1.0.0.0 – 127.255.255.255|255.0.0.0|/8|Large networks|
|B|128.0.0.0 – 191.255.255.255|255.255.0.0|/16|Medium networks|
|C|192.0.0.0 – 223.255.255.255|255.255.255.0|/24|Small networks|
|D|224.0.0.0 – 239.255.255.255|Multicast|-|Multicast traffic|
|E|240.0.0.0 – 255.255.255.255|Reserved|-|Experimental|

---

## 🧩 Subnet Mask

> [!important]  
> The subnet mask defines how an IP is divided into:

- Network part
    
- Host part
    

Example:

```
255.255.255.0 (/24)
```

### 🧠 Meaning

- 1 bits → network portion
    
- 0 bits → host portion
    

---

## 🚪 Network & Broadcast Addresses

> [!warning]  
> Every subnet reserves two special addresses:

### 📡 Network Address

- First address in subnet
    
- Identifies the network itself
    

### 📢 Broadcast Address

- Last address in subnet
    
- Sends data to all devices in network
    

---

## 🌐 Default Gateway

> [!info]  
> The **default gateway** is the router that connects your local network to other networks.

- Usually first or last usable IP in subnet
    
- Routes traffic outside local network
    

---

## 🔁 Broadcast Communication

> [!note]  
> Broadcast sends a message to all devices in a network.

- No response required
    
- Used for discovery and announcements
    
- Uses broadcast IP (last address in subnet)
    

---

## 🧠 Binary System (IPv4 Foundation)

> [!info]  
> Computers use binary (0s and 1s), while humans use decimal.

Each octet:

- 8 bits
    
- Each bit has a value:
    

```
128 64 32 16 8 4 2 1
```

---

## 🔢 Binary Example

IPv4:

```
192.168.10.39
```

Binary:

```
11000000 . 10101000 . 00001010 . 00100111
```

---

## 🔄 CIDR Notation

> [!important]  
> CIDR replaces old class-based addressing.

Format:

```
IP / prefix length
```

Example:

```
192.168.10.39/24
```

### 🧠 Meaning

- `/24` = first 24 bits are network bits
    
- Remaining bits = host portion
    

---

## 🧠 Key Takeaways

> [!summary]
> 
> - MAC = local device identification
>     
> - IP = global network identification
>     
> - IPv4 = 32-bit addressing system
>     
> - Subnet mask defines network vs host
>     
> - Gateway connects networks
>     
> - CIDR replaces old IP classes
>     
> - Broadcast reaches all devices in subnet
>     

---

