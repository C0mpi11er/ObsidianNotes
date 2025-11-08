->> mostly usinh hashcat to to decode hashes
->> hash_identifer is the best bet 
-> adding show reveals the cracked hash
->>hashcat -a 0 -m 0 F806FC5A2A0D5BA2471600758452799C /usr/share/wordlists/rockyou.txt --show
f806fc5a2a0d5ba2471600758452799c:rockyou


for bruteforce ?d means using integer check hash helpe for more info
hashcat -m 0 -a 3 e48e13207341b6bffb7fb1622282247b '?d?d?d?d' 
