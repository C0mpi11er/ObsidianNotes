2^h -2
host in bit
so 192.1.2.3/26 so network has 255 255 255 0
each octet is 8 bits 8x4=32
32-26 =7
2^7-2= .... 

| CIDR | Subnet Mask     | # of Hosts | Network Bits | Host Bits |
| ---- | --------------- | ---------- | ------------ | --------- |
| /8   | 255.0.0.0       | 16,777,214 | 8            | 24        |
| /16  | 255.255.0.0     | 65,534     | 16           | 16        |
| /24  | 255.255.255.0   | 254        | 24           | 8         |
| /25  | 255.255.255.128 | 126        | 25           | 7         |
| /26  | 255.255.255.192 | 62         | 26           | 6         |
| /27  | 255.255.255.224 | 30         | 27           | 5         |
| /28  | 255.255.255.240 | 14         | 28           | 4         |
| /29  | 255.255.255.248 | 6          | 29           | 3         |
| /30  | 255.255.255.252 | 2          | 30           | 2         |


> [!IMPORTANT]  
> Because 2 addresses are reserved:

- Network address
- Broadcast address

---

## Apply it

```
h = 62^6 = 6464 - 2 = 62 usable hosts
```

---

# 🧠 So for `/26`

|Value|Result|
|---|---|
|Host bits|6|
|Total IPs|64|
|Usable hosts|**62**|

---

# 🔍 Subnet mask for `/26`

Convert bits:

```
11111111.11111111.11111111.11000000
```

=

```
255.255.255.192
```

---

# 🧩 Understanding the blocks

Each `/26` subnet has **64 addresses**

So ranges go like:

```
0–6364–127128–191192–255
```

---

# 🧪 Example: 192.168.1.0/26

|Type|Address|
|---|---|
|Network|192.168.1.0|
|First host|192.168.1.1|
|Last host|192.168.1.62|
|Broadcast|192.168.1.63|

---

# 🔁 Next subnet

```
192.168.1.64/26
```

|Type|Address|
|---|---|
|Network|192.168.1.64|
|Broadcast|192.168.1.127|

---

# 🧠 Why 2^h?

> [!NOTE]  
> Each host bit can be:

```
0 or 1
```

So combinations =

```
2 × 2 × 2 × ... (h times) = 2^h
```

---

# ⚡ Quick intuition

|Host bits|Total IPs|Usable|
|---|---|---|
|2|4|2|
|3|8|6|
|4|16|14|
|6|64|62|

---

# ❌ Where you went wrong

You did:

```
32 - 26 = 7 ❌
```

Correct is:

```
32 - 26 = 6 ✅
```

---

# 🧠 Final mental model

```
CIDR → gives network bits32 - CIDR → host bits2^hostbits - 2 → usable IPs
```

---

# 🔥 One-line summary

> [!SUCCESS]  
> Subnetting is just splitting 32 bits into **network + host**, then counting