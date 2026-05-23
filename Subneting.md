# 🌐 Subnetting Quick Reference

|CIDR|Subnet Mask|Group Size|Usable Hosts|
|---|---|---|---|
|/30|255.255.255.252|4|2|
|/29|255.255.255.248|8|6|
|/28|255.255.255.240|16|14|
|/27|255.255.255.224|32|30|
|/26|255.255.255.192|64|62|
|/25|255.255.255.128|128|126|
|/24|255.255.255.0|256|254|
|/23|255.255.254.0|512|510|
|/22|255.255.252.0|1024|1022|
|/21|255.255.248.0|2048|2046|
|/20|255.255.240.0|4096|4094|

---

# 🧠 What Group Size Means

> [!info]  
> Group size = subnet increment / network jump

Example:

|CIDR|Group Size|Networks|
|---|---|---|
|/26|64|0,64,128,192|
|/27|32|0,32,64,96...|
|/28|16|0,16,32,48...|

---

# 🔥 Golden Rule

```text
256 - interesting octet = group size
```

Example:

```text
255.255.255.224
256 - 224 = 32
```

Group size = `32`


>[!NOTE]     
>things to find:
>Network IP
>Broadcast IP
>Cidr/Subnet
>First host
>Last host
>Next network ip
>Num Usable IP

faster cheat
time group size by 10 then double size or trriple etc


start at a lesser submask and add group size


start from 128 because all at one point will touch it then add group size


start from 128 and subtract group size


remeber the number above the ip  must be in correspondense to group size or calculation wunt work