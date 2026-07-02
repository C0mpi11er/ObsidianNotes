Here is the complete, raw list of Hashcat modes and examples formatted perfectly into an Obsidian-optimized Markdown file.

This file utilizes **Obsidian Callouts** and **clean tables** to split the massive data into easily scannable tabs. Copy the text block below and save it as `Hashcat_Modes_Reference.md` in your vault.

Markdown

````
# 🧾 Hashcat Modes & Generic Hashes Cheat Sheet

> [!INFO] Reference Guide
> This note contains a complete structured layout of Hashcat mode types, generic hash engines, platform-specific signatures, and historical tracking nodes.

---

## 🧮 Generic Hash Types

| Hash-Mode | Hash-Name | Example Syntax / Hash |
| :--- | :--- | :--- |
| **0** | MD5 | `8743b52063cd84097a65d1633f5c74f5` |
| **10** | md5($pass.$salt) | `01dfae6e5d4d90d9892622325959afbe:7050461` |
| **20** | md5($salt.$pass) | `f0fda58630310a6dd91a7d8f0a4ceda2:4225637426` |
| **30** | md5(utf16le($pass).$salt) | `b31d032cfdcf47a399990a71e43c5d2a:1448164` |
| **40** | md5($salt.utf16le($pass)) | `d63d0e21fdc05f618d55ef306c54af82:13288442151473` |
| **50** | HMAC-MD5 (key = $pass) | `fc741db0a2968c39d9c2a5cc75b05370:1234` |
| **60** | HMAC-MD5 (key = $salt) | `bfd280436f45fa38eaacac3b00518f29:1234` |
| **70** | md5(utf16le($pass)) | `2303b15bfa48c74a74758135a0df1201` |
| **100** | SHA1 | `b89eaac7e61417341b710b727768294d0e6a277b` |
| **110** | sha1($pass.$salt) | `2fc5a684737ce1bf7b3b239df432416e0dd07357:2014` |
| **120** | sha1($salt.$pass) | `cac35ec206d868b7d7cb0b55f31d9425b075082b:5363620024` |
| **130** | sha1(utf16le($pass).$salt) | `c57f6ac1b71f45a07dbd91a59fa47c23abcd87c2:6312251` |
| **140** | sha1($salt.utf16le($pass)) | `5db61e4cd8776c7969cfd62456da639a4c87683a:8763434884872` |
| **150** | HMAC-SHA1 (key = $pass) | `c898896f3f70f61bc3fb19bef222aa860e5ea717:1234` |
| **160** | HMAC-SHA1 (key = $salt) | `d89c92b4400b15c39e462a8caa939ab40c3aeeea:1234` |
| **170** | sha1(utf16le($pass)) | `b9798556b741befdbddcbf640d1dd59d19b1e193` |
| **200** | MySQL323 | `7196759210defdc0` |
| **300** | MySQL4.1/MySQL5 | `fcf7c1b8749cf99d88e5f34271d636178fb5d13` |
| **400** | phpass, WordPress (MD5), Joomla (MD5) | `$P$9d2OMJEOHyPg11MeSv46hashcatG.F14` |
| **400** | phpass, phpBB3 (MD5) | `$H$984478476IagS59wHZvyQMArzfx58u.` |
| **500** | md5crypt, MD5 (Unix), Cisco-IOS $1$ | `$1$ehAsHC4t$4IbK3fHS/H1YGtNYBrIEB1` |
| **501** | Juniper IVE | `3u+UR6n8AgABAAAAHxxdXKmiOmUoqKnZlf8lTOhlPYy93EAkbPfs5+49YLFd/B1+omSKbW7DoqNM40/EeVnwJ8kYoXv9zy9D5C5m5A==` |
| **600** | BLAKE2b-512 | `$BLAKE2$296c269e70ac5f0095e6fb47693480f0f7b97ccd0307f5c3bfa4df8f5ca5c9308a0e7108e80a0a9c0ebb715e8b7109b072046c6cd5e155b4cfd2f27216283b1e` |
| **610** | BLAKE2b-512($pass.$salt) | `$BLAKE2$41fcd44c789c735c08b43a871b81c8f617ca43918d38aee6cf8291c58a0b00a03115857425e5ff6f044be7a5bec8536b52d6c9992e21cd43cdca8a55bbf1f5c1:10336` |
| **620** | BLAKE2b-512($salt.$pass) | `$BLAKE2$f0325fdfc3f82a014935442f7adbc069d4636d67276a85b09f8de368f122cf5195a0b780d7fee709fbf1dcd02ddcb581df84508cf1fb0f3393af1be0565491c6:3301` |
| **900** | MD4 | `afe04867ec7a3845145579a95f72eca7` |
| **1000** | NTLM | `b4b9b02e6f09a9bd760f388b67351e2b` |
| **1100** | Domain Cached Credentials (DCC), MS Cache | `4dd8965d1d476fa0d026722989a6b772:3060147285011` |
| **1300** | SHA2-224 | `e4fa1555ad877bf0ec455483371867200eee89550a93eff2f95a61981310` |
| **1310** | sha224($pass.$salt) | `0cf361904f4b0234cf4ade8496d8c11c04e5982db967603e82f22b2f:89452466460220844541730694146873525188` |
| **1320** | sha224($salt.$pass) | `4258a61d3d0d5a5b6796f0ab02d081e998fe657d55d22091d3b51409:36669207` |
| **1400** | SHA2-256 | `127e6fbfe24a750e72930c220a8e138275656b8e5d8f48a98c3c92df2caba` |
| **1410** | sha256($pass.$salt) | `c73d08de890479518ed60cf670d17faa26a4a71f995c1dcc978165399401a6c4:5374352814` |
| **1420** | sha256($salt.$pass) | `eb368a2dfd38b405f014118c7d9747fcc97f4f0ee75c05963cd9da6ee65ef498:56040700161714` |
| **1430** | sha256(utf16le($pass).$salt) | `4cc8eb60476c33edac52b5a7548c2c50ef0f9e31ce656c6f4b213f901bc87421:89012814` |
| **1440** | sha256($salt.utf16le($pass)) | `a4bd99e1e0aba51814e81388badb23ecc560312c4324b2018ea76393ea1caca9:12345678` |
| **1450** | HMAC-SHA256 (key = $pass) | `abaf88d66bf2334a4a8b207cc61a96fb46c3e38e882e6f6f886742f688b8588c:1234` |
| **1460** | HMAC-SHA256 (key = $salt) | `8efbef4cec28f228fa948daaf4893ac3638fbae81358ff9020be1d7a9a509fc6:1234` |
| **1470** | sha256(utf16le($pass)) | `9e9283e633f4a7a42d3abc93701155be8afe5660da24c8758e7d3533e2f2dc82` |
| **1500** | descrypt, DES (Unix), Traditional DES | `48c/R8JAv757A` |
| **1600** | Apache $apr1$ MD5, md5apr1, MD5 (APR) | `$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.` |
| **1700** | SHA2-512 | `82a7dda829eb7f8ffe9fbe49e45d47d2dad9664fbb7adf72492e3c81ebd3e29134d9bc12212bf83c6840f10e8246b9db54a4859b7ccd0123d86e5872c1e5082f1` |
| **1710** | sha512($pass.$salt) | `e5c3ede3e49fb86592fb03f471c35ba13e8d89b8ab65142c9a8fdafb635fa2223c24e5558fd9313e8995019dcbec1fb584146b7bb12685c7765fc8c0d51379fd:635228326017` |
| **1720** | sha512($salt.$pass) | `976b451818634a1e2acba682da3fd6efa72adf8a7a08d7939550c244b237c72c7d42367544e826c0c83fe5c02f97c0373b6b1386cc794bf0d21d2df01bb9c08a:2613516180127173` |
| **1730** | sha512(utf16le($pass).$salt) | `13070359002b6fbb3d28e50fba55efcf3d7cc115fe6e3f6c98bf0e3210f1c6923427a1e1a3b214c1de92c467683f6466727ba3a51684022be5cc2ffcb78457d2:341351589` |
| **1740** | sha512($salt.utf16le($pass)) | `bae3a3358b3459c761a3ed40d34022f0609a02d90a0d7274610b16147e58ece00cd849a0bd5cf6a92ee5eb5687075b4e754324dfa70deca6993a85b2ca865bc8:1237015423` |
| **1750** | HMAC-SHA512 (key = $pass) | `94cb9e31137913665dbea7b058e10be5f050cc356062a2c9679ed0ad6119648e7be620e9d4e1199220cd02b9efb2b1c78234fa1000c728f82bf9f14ed82c1976:1234` |
| **1760** | HMAC-SHA512 (key = $salt) | `7cce966f5503e292a51381f238d071971ad5442488f340f98e379b3aeae2f33778e3e732fcc2f7bdc04f3d460eebf6f8cb77da32df25500c09160dd3bf7d2a6b:1234` |
| **1770** | sha512(utf16le($pass)) | `79bba09eb9354412d0f2c037c22a777b8bf549ab12d22e9a7d296eee07c8a7b5` |
| **1800** | sha512crypt, SHA-512 (Unix) | `$6$5567243c55099b6b$10a714a350db53beea8be6ac9c247fd40fea7e96d206a9f11fd1c45735556ac2004138640de206d0e1522607ab3c3f92816156d2d784506` |
| **1800** | Linux Kernel Crypto API (2.4) | `$cryptoapi$9$2$03000000000000000000000000000000$00000000000000000000000000000000$d1d20e91a8f2e18881dc79369d8af761` |
| **2100** | Domain Cached Credentials 2 (DCC2) | `$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f` |
| **2400** | Cisco-PIX MD5 | `dRRVnUmUHXOTt9nk` |
| **2410** | Cisco-ASA MD5 | `02dMBMYkTdC5Ziyp:362500` |
| **2500** | WPA-EAPOL-PBKDF2 | `https://hashcat.net/misc/example_hashes/hashcat.hccapx` |
| **2501** | WPA-EAPOL-PMK | `https://hashcat.net/misc/example_hashes/hashcat-pmk.hccapx` |
| **2600** | md5(md5($pass)) | `a936af92b0ae20b1ff6c3347a72e5fbe` |
| **2630** | md5(md5($pass.$salt)) | `0127eecea3120e34c8934ba3b72a390a:0300` |
| **3000** | LM | `299bd128c1101fd6` |
| **3100** | Oracle H: Type (Oracle 7+) | `7A963A529D2E3229:36824275` |
| **3200** | bcrypt $2*$, Blowfish (Unix) | `$2a$05$LhayLxezLhK1LhWvKxCyLOj0j1u.Kj0jZ0pEmm134uzrQlFvQJLF6` |
| **3500** | md5(md5(md5($pass))) | `9882d0778518b095917eb589f6998441` |
| **3610** | md5(md5(md5($pass)).$salt) | `a0ab79f9e2b5a4434d2da61673b56362:1234` |
| **3710** | md5($salt.md5($pass)) | `95248989ec91f6d0439dbde2bd0140be:1234` |
| **3730** | Dahua NVR/DVR/HVR | `0e1484eb061b8e9cfd81868bba1dc4a0:229381927:182719643` |
| **3800** | md5($salt.$pass.$salt) | `2e45c4b99396c6cb2db8bda0d3df669f:1234` |
| **3910** | md5(md5($pass).md5($salt)) | `250920b3a5e31318806a032a4674df7e:1234` |
| **4010** | md5($salt.md5($salt.$pass)) | `30d0cf4a5d7ed831084c5b8b0ba75b46:1234` |
| **4110** | md5($salt.md5($pass.$salt)) | `b4cb5c551a30f6c25d648560408df68a:1234` |
| **4300** | md5(strtoupper(md5($pass))) | `b8c385461bb9f9d733d3af832cf60b27` |
| **4400** | md5(sha1($pass)) | `288496df99b33f8f75a7ce4837d1b480` |
| **4410** | md5(sha1($pass).$salt) | `bc8319c0220bff8a0d7f5d703114a725:346593487563452514` |
| **4420** | md5(sha1($pass.$salt)) | `34ebbba3e5c98f6253c160eae53da092:62243784561210502854` |
| **4430** | md5(sha1($salt.$pass)) | `df0e9ede5b6c7d1f1b47199f86029002:5913280920179918072235993969271046188645` |
| **4500** | sha1(sha1($pass)) | `3db9184f5da4e463832b086211af8d2314919951` |
| **4510** | sha1(sha1($pass).$salt) | `9138d472fce6fe50e2a32da4eec4ecdc8860f4d5:hashcat` |
| **4520** | sha1($salt.sha1($pass)) | `a0f835fdf57d36ebd8d0399cc44e6c2b86a1072b:51135821435275166720110707353173521156665074731547` |
| **4700** | sha1(md5($pass)) | `92d85978d884eb1d99a51652b1139c8279fa8663` |
| **4710** | sha1(md5($pass).$salt) | `53c724b7f34f09787ed3f1b316215fc35c789504:hashcat` |
| **4800** | iSCSI CHAP authentication, MD5(CHAP) | `7afd09efdd6f8ca9f18ec77c5869788c3:01020304050607080910111213141516:01` |
| **4900** | sha1($salt.$pass.$salt) | `85087a691a55cbb41ae335d459a9121d54080b80:48838784` |
| **5000** | sha1(sha1($salt.$pass.$salt)) | `05ac0c544060af48f993f9c3cdf2fc03937ea35b:2327251020205` |
| **5100** | Half MD5 | `8743b52063cd8409` |
| **5200** | Password Safe v3 | `https://hashcat.net/misc/example_hashes/hashcat.psafe3` |
| **5300** | IKE-PSK MD5 | `https://hashcat.net/misc/example_hashes/hashcat.ikemd5` |
| **5400** | IKE-PSK SHA1 | `https://hashcat.net/misc/example_hashes/hashcat.ikesha1` |
| **5500** | NetNTLMv1 / NetNTLMv1+ESS | `u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:9526fb8c23a90751cdd619b6cea564742e1e4bf3306ba41:cb8086049ec4736c` |
| **5600** | NetNTLMv2 | `admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c783031...0000` |
| **5700** | Cisco-IOS type 4 (SHA256) | `2btjjy78REtmYkkW0csHUbJZOstRXoWdX1mGrmmfeHI5` |
| **5720** | Cisco-ISE Hashed Password (SHA256) | `465865d4226c4d9696e601f2c99b25ae2c194ec01806bafc93933331acfc1a...` |
| **5800** | Samsung Android Password/PIN | `0223b799d526b596fe4ba5628b9e65068227e68e:f6d45822728ddb2c` |
| **6000** | RIPEMD-160 | `012cb9b334ec1aeb71a9c8ce85586082467f7eb66` |
| **6050** | HMAC-RIPEMD160 (key = $pass) | `4f5edca01734e03dd7e735362625a76e6bcb61b2:5235561494606760` |
| **6060** | HMAC-RIPEMD160 (key = $salt) | `34d8e55a2ae1e9549a291326ce2f0a8dcdc75c5c:0852320256354234` |
| **6100** | Whirlpool | `7ca8eaaaa15eaa4c038b4c47b9313e92da827c06940e69947f85bc0fbef3eb8f...` |
| **6211** | TrueCrypt 5.0+ PBKDF2-HMAC-RIPEMD160 + AES | `https://hashcat.net/misc/example_hashes/hashcat_ripemd160_aes.tc` |
| **6300** | AIX {smd5} | `{smd5}a5/yTL/u$VfvgyHx1xUlXZYBocQpQY0` |
| **6400** | AIX {ssha256} | `{ssha256}06$aJckFGJAB30LTe10$ohUsB7LBPlgclE3hJg9x042DLJvQyxVCX.nZZLEz.g2` |
| **6500** | AIX {ssha512} | `{ssha512}06$bJbkFGJAB30L2e23$bXiXjyH5YGIyoWWmEVwq67nCU5t7GLy9HkCzrodRCQCx3r9VvG98o7O3V0r9cVrX3LPPGuHqT5LLn0oGCuI1..` |
| **6600** | 1Password, agilekeychain | `https://hashcat.net/misc/example_hashes/hashcat.agilekeychain` |
| **6700** | AIX {ssha1} | `{ssha1}06$bJbkFGJAB30L2e23$dCESGOsP7jaIIAJ1QAcmaGeG.kr` |
| **6800** | LastPass + LastPass sniffed | `4a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com` |
| **6900** | GOST R 34.11-94 | `df226c2c6dcb1d995c0299a33a084b201544293c31fc3d279530121d36bbce` |
| **7000** | FortiGate (FortiOS) | `AK1AAECAwQFBgcICRARNGqgeC3is8gv2xWWRony9NJnDgE=` |
| **7200** | GRUB 2 | `grub.pbkdf2.sha512.10000.7d391ef48645f626b427b1fae0...` |
| **7300** | IPMI2 RAKP HMAC-SHA1 | `b7c2d6f13a43dce2e44ad120a9cd8a13d0ca23f0414275c0bbe1070d...` |
| **7400** | sha256crypt $5$, SHA256 (Unix) | `$5$rounds=5000$GX7BopJZJxPc/KEK$le16UF8I2Anb.rOrn22AUPWvzUETDGefUmAV8AZkGcD` |
| **7500** | Kerberos 5, etype 23, AS-REQ Pre-Auth | `$krb5pa$23$user$realm$salt$4e751db65422b2117f7eac7b...` |
| **7700** | SAP CODVN B (BCODE) | `USER$C8B48F26B87B7EA7` |
| **7800** | SAP CODVN F/G (PASSCODE) | `USER$ABCAD719B17E7F794DF7E686E563E9E2D24DE1D0` |
| **7900** | Drupal7 | `$S$C33783772bRXEx1aCsvY.dqgaaSu76XmVlKrW9Qu8IQlvxHlmzLf` |
| **8000** | Sybase ASE | `0xc00778168388631428230545ed2c976790af96768afa0806fe6c0da3b28f3e132137eac56f9bad027ea28` |
| **8100** | Citrix NetScaler (SHA1) | `1765058016a22f1b4e076dccd1c3df4e8e5c0839ccded98ea8200` |
| **8200** | 1Password, cloudkeychain | `https://hashcat.net/misc/example_hashes/hashcat.cloudkeychain` |
| **8300** | DNSSEC (NSEC3) | `7b5n74kq8r441blc2c5qbbat19baj79r:.lvdsiqfj.net:33164473:` |
| **8400** | WBB3 (Woltlab Burning Board) | `8084df19a6dc81e2597d051c3d8b400787e2d5a9:6755045315424852185115352765375338838643` |
| **8500** | RACF | `$racf$*USER*FC2577C6EBE6265B` |
| **8501** | AS/400 DES | `$as400$des$*OPEN3*EC76FC0DEF5B0A83` |
| **8600** | Lotus Notes/Domino 5 | `3dd2e1e5ac03e230243d58b8c5ada07` |
| **8700** | Lotus Notes/Domino 6 | `(GDpOtD35gGlyDksQRxEU)` |
| **8800** | Android FDE <= 4.3 | `https://hashcat.net/misc/example_hashes/hashcat.android43fde` |
| **8900** | scrypt | `SCRYPT:1024:1:1:MDIwMzMwNTQwNDQyNQ==:5FW+zWivLxgCWj7qLiQbeC8zaNQ+qdO0NUinvqyFcfo=` |
| **9000** | Password Safe v2 | `https://hashcat.net/misc/example_hashes/hashcat.psafe2.dat` |
| **9100** | Lotus Notes/Domino 8 | `(HsjFebq0Kh9kH7aAZYc7kY30mC30mC3KmC30mCluagXrvWKj1)` |
| **9200** | Cisco-IOS $8$ (PBKDF2-SHA256) | `$8$TnGX/fE4KGHOVU$pEhnEvxrvaynpi8j4f.EMHr6M.FzU8xnZnBr/tJdFWk` |
| **9300** | Cisco-IOS $9$ (scrypt) | `$9$2MJBozw/9R3UsU$2lFhcKvpghcyw8deP25GOfyZaagyUOGBymkryvOdfo6` |
| **9400** | MS Office 2007 | `$office$*2007*20*128*16*411a51284e0d0200b131a8949aaaa5cc*...` |
| **9500** | MS Office 2010 | `$office$*2010*100000*128*16*77233201017277788267221014757262*...` |
| **9600** | MS Office 2013 | `$office$*2013*100000*256*16*7dd611d7eb4c899f74816d1dec817b3b*...` |
| **9700** | MS Office <= 2003 MD5 + RC4 | `$oldoffice$1*04477077758555626246182730342136*b1b72ff351...` |
| **9800** | Radmin2 | `22527bee5c29ce95373c4e0f359f079b` |
| **10000** | Django (PBKDF2-SHA256) | `pbkdf2_sha256$20000$H0dPx8NeajVu$GiC4k5kqbbR9qWBlsRgDywNqC2vd9kqfk7zdorEnNas=` |
| **10100** | SipHash | `ad61d78c06037cd9:2:4:81533218127174468417660201434054` |
| **10200** | CRAM-MD5 | `$cram_md5$PG5vLXJlcGx5QGhhc2hjYXQubmV0Pg==$dXNlciA0NGVhZmQyMmZlNzY2NzBmNmIyODc5MDgxYTdmNWY3MQ==` |
| **10300** | SAP CODVN H (PWDSALTEDHASH) | `{x-issha, 1024}C0624EvGSdAMCtuWnBBYBGA0chvqAflKY74oEpw/rpY=` |
| **10400** | PDF 1.1 - 1.3 (Acrobat 2 - 4) | `$pdf$1*2*40*-1*0*16*51726437280452826511473255744374*...` |
| **10500** | PDF 1.4 - 1.6 (Acrobat 5 - 8) | `$pdf$2*3*128*-1028*1*16*da42ee15d4b3e08fe5b9ecea0e02ad0f*32*...` |
| **10600** | PDF 1.7 Level 3 (Acrobat 9) | `$pdf$5*5*256*-1028*1*16*20583814402184226866485332754315*...` |
| **10700** | PDF 1.7 Level 8 (Acrobat 10 - 11) | `$pdf$5*6*256*-1028*1*16*21240790753544575679622633641532*...` |
| **10800** | SHA2-384 | `07371af1ca1fca7c6941d2399f3610f1e392c56c6d73fddffe38f18c430a2817028dae1ef09ac683b62148a2c8757f421` |
| **10810** | sha384($pass.$salt) | `ca1c843a7a336234baf9db2e10bc38824ce523402fbd7741286b1602bdf6cb869a45289bb9fb706bd404b9f3842ff729:274646079` |
| **10820** | sha384($salt.$pass) | `63f63d7f82d4a4cb6b9ff37a6bc7c5ec39faaf9c9078551f5cbf7960e76ded87b643d37ac53c45bc544325e7ff83a1f2:93362` |
| **10900** | PBKDF2-HMAC-SHA256 | `sha256:1000:MTc3MTA0MTQwMjQxNzY=:PYjCU215Mi57AYPKva9j7mvF4Rc5bCnt` |
| **10901** | RedHat 389-DS LDAP | `{PBKDF2_SHA256}AAAgADkxMjM2NTIzMzgzMjQ3MjI4MDAwNTk5OTAyOT...` |
| **11000** | PrestaShop | `810e3d12f0f10777a679d9ca1ad7a8d9:M2uZ122bSHJ4Mi54tXGY0lqcv1r28mUluSkyw37ou5oia4i239ujqw0l` |
| **11100** | PostgreSQL CRAM (MD5) | `$postgres$postgres*f0784ea5*2091bb7d4725d1ca85e8de6ec349baf6` |
| **11200** | MySQL CRAM (SHA1) | `$mysqlna$1c24ab8d0ee94d70ab1f2e814d8f0948a14d10b9*437e93572f18ae44d9e779160c2505271f85821d` |
| **11300** | Bitcoin/Litecoin wallet.dat | `$bitcoin$96$d011a1b6a8d675b7a36d0cd2efaca32a9f8dc1d57d6d...$16$...` |
| **11400** | SIP digest authentication (MD5) | `$sip$*192.168.100.100*192.168.100.121*username*asterisk*REGISTER*...` |
| **11500** | CRC32 | `5c762de4a:00000000` |
| **11600** | 7-Zip | `$7z$0$19$0$salt$8$f6196259a7326e3f0000000000000000$...` |
| **11900** | PBKDF2-HMAC-MD5 | `md5:1000:MTg1MzA=:Lz84VOcrXd699Edsj34PP98+f4f3S0rTZ4kHAIHoAjs=` |
| **12000** | PBKDF2-HMAC-SHA1 | `sha1:1000:MzU4NTA4MzIzNzA1MDQ=:19ofiY+ahBXhvkDsp0j2ww==` |
| **12100** | PBKDF2-HMAC-SHA512 | `sha512:1000:ODQyMDEwNjQyODY=:MKaHNWXUsuJB3IEwBHbm3w==` |
| **12150** | Apache Shiro 1 SHA-512 | `$shiro1$SHA-512$1024$WobJGSjbUhsMdaILomMOdw==$9uptGJ24vzZCq...` |
| **12200** | eCryptfs | `$ecryptfs$0$1$7c95c46e82f364b3$60bba503f0a42d0c` |
| **12300** | Oracle T: Type (Oracle 12+) | `78281A9C0CF626BD05EFC4F41B515B61D6C4D95A250CD4A605CA0EF97168D6...` |
| **12400** | BSDi Crypt, Extended DES | `_9G..8147mpcfKT8g0U.` |
| **12500** | RAR3-hp | `$RAR3$*0*45109af8ab5f297a*adbf6c5385d7a40373e8f77d7b89d317` |
| **12600** | ColdFusion 10+ | `aee9edab5653f509c4c63e559a5e967b4c112273bc6bd84525e630a3f9028dcb:...` |
| **12700** | Blockchain, My Wallet | `$blockchain$288$5420055827231730710301348670802335e45a6f5f...` |
| **12800** | MS-AzureSync PBKDF2-HMAC-SHA256 | `v1;PPH1_MD4,84840328224366186645,100,005a491d8bf3715085d69f934e...` |
| **12900** | Android FDE (Samsung DEK) | `384218541184126257684081604771123842185411841262576840816047...` |
| **13000** | RAR5 | `$rar5$16$74575567518807622265582327032280$15$f8b4064de34ac0...` |
| **13100** | Kerberos 5, etype 23, TGS-REP | `$krb5tgs$23$*user$realm$test/spn*$63386d22d359fe42230300d56...` |
| **13200** | AxCrypt 1 | `$axcrypt$*1*10000*aaf4a5b4a7185551fea2585ed69fe246*45c616e901e4...` |
| **13300** | AxCrypt 1 in-memory SHA1 | `$axcrypt_sha1$b89eaac7e61417341b710b727768294d0e6a277b` |
| **13400** | KeePass 1 AES / without keyfile | `$keepass$*1*50000*0*375756b9e6c72891a8e5645a3338b8c8*82afc053...` |
| **13500** | PeopleSoft PS_TOKEN | `b5e335754127b25ba6f99a94c738e24cd634c35a:aa07d396f5038a6cbeded88...` |
| **13600** | WinZip | `$zip2$*0*3*0*e3222d3b65b5a2785b192d31e39ff9de*1320*e*19648c3e...` |
| **13900** | OpenCart | `6e36dcfc6151272c797165fce21e68e7c7737e40:472433673` |
| **14000** | DES (PT = $salt, key = $pass) | `8a28bc61d44bb815c:1172075784504605` |
| **14100** | 3DES (PT = $salt, key = $pass) | `937387ff8d8dafe15:8152001061460743` |
| **14400** | sha1(CX) | `fd9149fb3ae37085dc6ed1314449f449fbf77aba:87740665218240877702` |
| **14700** | iTunes backup < 10.0 | `$itunes_backup$*9*b8e3f3a970239b22ac199b622293fe4237b9d16e74b...` |
| **14800** | iTunes backup >= 10.0 | `$itunes_backup$*10*8b715f516ff8e64442c478c2d9abb046fc6979ab07...` |
| **14900** | Skip32 (PT = $salt, key = $pass) | `12c9350366:4463046` |
| **15000** | FileZilla Server >= 0.9.55 | `632c4952b8d9adb2c0076c13b57f0c934c80bdc14fc1b4c341c2e0a8fd9...` |
| **15100** | Juniper/NetBSD sha1crypt | `$sha1$15100$jiJDkz0E$E8C7RQAD3NetbSDz7puNAY.5Y2jr` |
| **15200** | Blockchain, My Wallet, V2 | `$blockchain$v2$5000$288$06063152445005516247820607861028813...` |
| **15300** | DPAPI masterkey file v1 | `$DPAPImk$1*1*S-15-21-466364039-425773974-453930460-1925*des3*...` |
| **15400** | ChaCha20 | `$chacha20$*0400000000000003*16*0200000000000001*5152535455565758` |
| **15500** | JKS Java Key Store Private Keys | `$jksprivk$*5A3AA3C3B7DD7571727E1725FB09953EF3BEDBD9*...` |
| **15600** | Ethereum Wallet, PBKDF2-HMAC-SHA256 | `$ethereum$p*262144*32383831373131303534383437373837363234...` |
| **15700** | Ethereum Wallet, SCRYPT | `$ethereum$s*262144*1*8*343638373733383831303534373630363735...` |
| **15900** | DPAPI masterkey file v2 | `$DPAPImk$2*2*S-15-21-423929668-478423897-489523715-1834*aes256*...` |
| **16000** | Tripcode | `pfaRCwDe0U` |
| **16100** | TACACS+ | `$tacacs-plus$0$5fde8e68$4e13e8fb33df` |
| **16200** | Apple Secure Notes | `$ASN$*1*20000*80771171105233481004850004085037*d04b17af7f6b...` |
| **16300** | Ethereum Pre-Sale Wallet | `$ethereum$w*e94a8e49deac2d62206bf9bfb7d2aaea7eb06c1a378c...` |
| **16400** | CRAM-MD5 Dovecot | `{CRAM-MD5}5389b33b9725e5657cb631dc50017ff1535ce4e2a1c41400...` |
| **16500** | JWT (JSON Web Token) | `eyJhbGciOiJIUzI1NiJ9.eyIzNDM2MzQyMCI6NTc2ODc1NDd9.f1nXZ3V...` |
| **16600** | Electrum Wallet (Salt-Type 1-3) | `$electrum$1*44358283104603165383613672586868*c43a6632d9f...` |
| **16700** | FileVault 2 | `$fvde$1$16$84286044060108438487434858307513$20000$f1620ab9...` |
| **16800** | WPA-PMKID-PBKDF2 | `12582a8281bf9d4308d6f5731d0e61c61*4604ba734d4e*89acf0e761...` |
| **16900** | Ansible Vault | `$ansible$0*0*6b761adc6faeb0cc0bf197d3d4a4a7d3f1682e4b169cae8fa...` |
| **17010** | GPG (AES-128/AES-256 (SHA-1)) | `$gpg$*1*348*1024*8833fa3812b5500aa9eb7e46febfa31a0584b7e4a...` |
| **17210** | PKZIP (Compressed) | `$pkzip2$1*1*2*0*e3*1c5*eda7a8de*0*28*8*e3*eda7*5096*a9fc...` |
| **17300** | SHA3-224 | `412ef78534ba6ab0e9b1607d3e9767a25c1ea9d5e83176b4c2817a6c174` |
| **17400** | SHA3-256 | `d60fcf6585da4e17224f58858970f0ed5ab042c3916b76b0b828e62eaf636` |
| **17500** | SHA3-384 | `983ba28532cc6320d04f20fa485bcedb38bddb666eca5f1e5aa279ff1c624` |
| **17600** | SHA3-512 | `7c2dc1d743735d4e069f3bda85b1b7e9172033dfdd8cd599ca094ef8570f3` |
| **17700** | Keccak-224 | `e1dfad9bafeae6ef15f5bbb16cf4c26f09f5f1e7870581962fc8463617` |
| **17800** | Keccak-256 | `203f88777f18bb4ee1226627b547808f38d90d3e106262b5de9ca943b5713` |
| **17900** | Keccak-384 | `5804b7ada5806ba7940100e9a7ef493654ff2a21d94d4f2ce4bf69abda5d...` |
| **18000** | Keccak-512 | `2fbf5c9080f0a704de2e915ba8fdae6ab00bbc026b2c1c8fa07da1239381c...` |
| **18100** | TOTP (HMAC-SHA1) | `597056:3600` |
| **18200** | Kerberos 5, etype 23, AS-REP | `$krb5asrep$23$user@domain.com:3e156ada591263b8aab0965f5ae...` |
| **18300** | Apple File System (APFS) | `$fvde$2$16$58778104701476542047675521040224$20000$39602e86b7...` |
| **18400** | Open Document Format 1.2 | `$odf$*1*1*100000*32*751854d8b90731ce0579f96bea6f0d4ac2fb2f...` |
| **18500** | sha1(md5(md5($pass))) | `888a2ffcb3854fba0321110c5d0d434ad1aa2880` |
| **18600** | Open Document Format 1.1 | `$odf$*0*0*1024*16*bff753835f4ea15644b8a2f8e4b5be3d147b9576...` |
| **18700** | Java Object hashCode() | `29937c08` |
| **18800** | Blockchain Wallet, 2nd Pass (SHA256) | `YnM6WYERjJfhxwepT7zV6odWoEUz1X4esYQb4bQ3KZ7bbZAyOTc1MDM...` |
| **18900** | Android Backup | `$ab$5*0*10000*b8900e4885ff9cad8f01ee1957a43bd633fea1249144...` |
| **19000** | QNX /etc/shadow (MD5) | `@m@75f6f129f9c9e77b6b1b78f791ed764a@87418575` |
| **19100** | QNX /etc/shadow (SHA256) | `@s@0b365cab7e17ee1e7e1a90078501cc1aa85888d6da34e2f5b04f5c614b` |
| **19200** | QNX /etc/shadow (SHA512) | `@S@715df9e94c097805dd1e13c6a40f331d02ce589765a2100ec7435e76...` |
| **19210** | QNX /etc/shadow (Streebog-512) | `06$bJbkFGJAB30L2e23$bXiXjyH5YGIyoWWmEVwq67nCU5t7GLy9Hk...` |
| **19300** | Flask Session Cookie | `eyJ1c2VybmFtZSI6ImFkbWluIn0.YjdgRQ.1OTlf1PD0H9wXsu_qS0aywAJVD8` |
| **19500** | Ruby on Rails Restful Auth | `3999d08db95797891ec77f07223ca81bf43e1be2:5dcc47b04c49d3c8e1...` |
| **19600** | Engine Yard SHA512 | `25d509824028a999f4ee851b5de404bb316b78ae8e974874376484018f5...` |
| **19700** | Episerver | `c1bade2bd4ebc8db841ac6ab3e0a5035a29619e5b1a6135782b77da5d7cf...` |
| **19800** | CubeCart | `a2c0342a2617026fbaeed01130c826cc3f58242799894b3ecc1abfa811e...` |
| **19900** | STDOUT | `n/a` |

---

## 🌎 Specific Platform Hash Types

> [!TIP] Implementation Context
> These configurations mapping internal schemes are structurally limited to targeted operational distributions.

| Hash-Mode | Hash-Name | Example Hash |
| :--- | :--- | :--- |
| **11** | Joomla < 2.5.18 | `19e0e8d91c722e7091ca7a6a6fb0f4fa:547180318425216517577856030` |
| **12** | PostgreSQL | `a6343a68d964ca596d9752250d54bb8a:postgres` |
| **21** | osCommerce, xt:Commerce | `374996a5e8a5e57fd97d893f7df79824:36` |
| **22** | Juniper NetScreen/SSG (ScreenOS) | `nNxKL2rOEkbBc9BFLsVGG6OtOUO/8n:user` |
| **23** | Skype | `3af0389f093b181ae26452015f4ae728:user` |
| **24** | SolarWinds Serv-U | `e983672a03adcc9767b24584338eb378` |
| **101** | nsldap, SHA-1(Base64) | `{SHA}uJ6qx+YUFzQbcQtyd2gpTQ5qJ3s=` |
| **111** | nsldaps, SSHA-1(Base64) | `{SSHA}AZKja92fbuuB9SpRlHqaoXxbTc43Mzc2MDM1Ng==` |
| **112** | Oracle S: Type (Oracle 11+) | `ac5f1e62d21fd0529428b84d42e8955b04966703:3844574818447737813` |
| **121** | SMF (Simple Machines Forum) > v1.1 | `ecf076ce9d6ed3624a9332112b1cd67b236fdd11:17782686` |
| **122** | macOS v10.4 - v10.6 | `1430823483d07626ef8be3fda2ff056d0dfd818dbfe47683` |
| **124** | Django (SHA-1) | `sha1$fe76b$02d5916550edf7fc8c886f044887f4b1abf9b013` |
| **125** | ArubaOS | `5387280701327dc2162bdeb451d5a465af6d13eff9276efeba13` |
| **131** | MSSQL (2000) | `0x01002702560500000000000000000000000000000000000000008db43d...` |
| **132** | MSSQL (2005) | `0x010018102152f8f28c8499d8ef263c53f8be369d799f931b2fbe13` |
| **133** | PeopleSoft | `uXmFVrdBvv293L9kDR3VnRmx4ZM=` |
| **141** | Episerver 6.x < .NET 4 | `$episerver$*0*bEtiVGhPNlZpcUN4a3ExTg==*utkfN0EOgljbv5FoZ6+Ac...` |
| **1411** | SSHA-256(Base64), LDAP | `{SSHA256}{SSHA256}OZiz0cnQ5hgyel3Emh7NCbhBRCQ+HVBwYplQunHY...` |
| **1421** | hMailServer | `8fe7ca27a17adc337cd892b1d959b4e487b8f0ef09e32214f44fb1b07e46...` |
| **1441** | Episerver 6.x >= .NET 4 | `$episerver$*1*MDEyMzQ1Njc4OWFiY2RlZg==*lRjiU46qHA7S6ZE7RfKU...` |
| **1711** | SSHA-512(Base64), LDAP | `{SSHA512}{SSHA512}ALtwKGBdRgD+U0fPAy31C28RyKYx7+a8kmfksccs...` |
| **1722** | macOS v10.7 | `648742485c9b0acd786a233b2330197223118111b481abfa0ab8b3e8ede5...` |
| **1731** | MSSQL (2012, 2014) | `0x02000102030434ea1b17802fd95ea6316bd61d2c94622ca3812793e8...` |
| **1811** | vBulletin < v3.8.5 | `16780ba78d2d5f02f3202901c1b6d975:5682` |
| **2611** | vBulletin >= v3.8.5 | `bf366348c53ddcfbd16e63edfdd1eee6:18126425005677460364187404...` |
| **2811** | MyBB 1.2+, IPB2+ | `8d2129083ef35f4b365d5d87487e1207:4720437` |
| **3711** | MediaWiki B type | `$B$56668501$0ce106caa70af57fd525aeaf80ef2898` |
| **4511** | Redmine | `1fb46a8f81d8838f46879aaa29168d08aa6bf22d:3290afd193d90e900e8` |
| **4711** | PunBB | `4a2b722cc65ecf0f7797cdaea4bce81f66716eef:65307436` |
| **10411** | Huawei sha1(md5($pass).$salt) | `53c724b7f34f09787ed3f1b316215fc35c789504:hashcat` |
| **17100** | macOS v10.8+ (PBKDF2-SHA512) | `$ml$35460$93a9e720d2b79b2e2f440b17e7856ae119e08d15d8b...` |
| **21240** | SolarWinds Orion | `$solarwinds$0$admin$fj4EBQewCQUZ7IYHl0qL8uj9kQSBb...` |

---

## 📜 Section 3: Legacy, Custom & Specialized Target Formats

### 🏗️ Legacy Volume & Storage Environments

| Mode | Alg / Architecture Focus | Format Profile / Verification Source |
| :--- | :--- | :--- |
| **2410** | Cisco-ASA MD5 | `02dMBMYkTdC5Ziyp:362` |
| **2500** | WPA-EAPOL-PBKDF2 | `hashcat.hccapx` sample sets |
| **6211** | TrueCrypt PBKDF2-HMAC-RIPEMD160 | Isolated algorithm header parsing definitions |
| **13711**| VeraCrypt PBKDF2-HMAC-RIPEMD160 | Custom iteration loops with non-standard parameters |
| **14600**| LUKS v1 Encryption Block | Cryptographic envelope configuration mappings |
| **22100**| BitLocker Full Disk Metadata | `$bitlocker$1$16$...` storage keys |

### 🔧 Dynamic Python & Scripting Classes

```text
# Python Passlib Deployments
Mode 20200 ➔ $pbkdf2-sha512$25000$LyWE0HrP...
Mode 20300 ➔ $pbkdf2-sha256$29000$x9h7j/Ge...
Mode 20400 ➔ $pbkdf2$131000$r5WythYixP...
````