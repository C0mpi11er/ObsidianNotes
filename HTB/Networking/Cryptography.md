

> [!info] Overview  
> Cryptography protects data confidentiality, integrity, and authenticity using mathematical encryption algorithms.

Used for:

- Secure communication
- Authentication
- Data protection
- Digital signatures

---

# Encryption Types

|Type|Uses|
|---|---|
|Symmetric Encryption|Same key for encryption/decryption|
|Asymmetric Encryption|Public & private key pair|

---

# Symmetric Encryption

> [!important] Symmetric Encryption  
> Uses:
> 
> ```
> One shared secret key
> ```
> 
> for both encryption and decryption.

---

# Symmetric Characteristics

|Feature|Description|
|---|---|
|Fast|Efficient for large data|
|Simple|Single shared key|
|Key Exchange Problem|Secure key sharing required|

---

# Symmetric Encryption Risks

> [!warning] Key Security  
> If the shared key is:
> 
> - Leaked
> - Intercepted
> - Stolen
> 
> The encrypted data becomes compromised.

---

# Common Symmetric Algorithms

|Algorithm|Notes|
|---|---|
|AES|Modern secure standard|
|DES|Outdated and weak|
|3DES|Improved DES|

---

# AES (Advanced Encryption Standard)

> [!important] AES  
> AES is the modern encryption standard used worldwide.

---

# AES Key Sizes

|Type|Key Length|
|---|---|
|AES-128|128-bit|
|AES-192|192-bit|
|AES-256|256-bit|

---

# AES Advantages

|Advantage|Description|
|---|---|
|Fast|Efficient processing|
|Secure|Strong encryption|
|Widely Used|Industry standard|

---

# AES Usage

Used in:

- WPA2/WPA3
- SSH
- VPNs
- TLS
- OpenSSL
- IPsec

---

# DES (Data Encryption Standard)

> [!warning] DES  
> Older symmetric block cipher using:
> 
> ```
> 56-bit keys
> ```

Now considered insecure.

---

# DES Characteristics

|Feature|Description|
|---|---|
|Block Size|64-bit|
|Effective Key Length|56-bit|
|Weakness|Vulnerable to brute force|

---

# 3DES (Triple DES)

> [!info] 3DES  
> Applies DES encryption:
> 
> ```
> Three times
> ```

More secure than DES but slower than AES.

---

# Asymmetric Encryption

> [!important] Asymmetric Encryption  
> Uses:
> 
> - Public key
> - Private key

---

# Asymmetric Process

|Key|Purpose|
|---|---|
|Public Key|Encrypt data|
|Private Key|Decrypt data|

---

# Asymmetric Advantages

|Advantage|Description|
|---|---|
|Secure Key Exchange|No shared secret required|
|Authentication|Supports digital signatures|
|Scalability|Easier key distribution|

---

# Common Asymmetric Algorithms

|Algorithm|Purpose|
|---|---|
|RSA|Encryption & signatures|
|ECC|Efficient cryptography|
|PGP|Secure messaging|

---

# Asymmetric Usage

Used in:

- SSL/TLS
- SSH
- VPNs
- PKI
- Cloud security
- Digital signatures

---

# RSA

> [!important] RSA  
> Public-key algorithm based on:
> 
> ```
> Large prime number mathematics
> ```

---

# ECC (Elliptic Curve Cryptography)

> [!important] ECC  
> Provides strong security with:
> 
> - Smaller keys
> - Faster performance
> - Lower resource usage

---

# Digital Signatures

> [!info] Digital Signatures  
> Used for:
> 
> - Authentication
> - Integrity verification
> - Non-repudiation

---

# Cipher Modes

> [!important] Cipher Modes  
> Define how block ciphers encrypt data blocks.

---

# Common Cipher Modes

|Mode|Description|
|---|---|
|ECB|Basic but insecure|
|CBC|Chained block encryption|
|CFB|Stream-like encryption|
|OFB|Real-time stream encryption|
|CTR|Counter-based encryption|
|GCM|Encryption + integrity|

---

# ECB Mode

> [!warning] ECB  
> Weak because:
> 
> - Does not hide patterns
> - Vulnerable to analysis

Generally not recommended.

---

# CBC Mode

> [!info] CBC  
> Each block depends on the previous block.

Commonly used in:

- TLS
- Disk encryption
- VeraCrypt

---

# CFB Mode

> [!info] CFB  
> Suitable for:
> 
> - Real-time streams
> - Network traffic
> - File transfer encryption

---

# OFB Mode

> [!info] OFB  
> Stream encryption mode optimized for:
> 
> - Continuous data streams
> - Real-time communication

Used in:

- SSH
- PKCS

---

# CTR Mode

> [!important] CTR  
> Counter-based encryption mode used for:
> 
> - IPsec
> - Disk encryption
> - Real-time communication

---

# GCM Mode

> [!important] GCM  
> Provides:
> 
> - Encryption
> - Integrity verification

Common in:

- VPNs
- Wireless security
- Secure protocols

---

# Symmetric vs Asymmetric

|Feature|Symmetric|Asymmetric|
|---|---|---|
|Keys Used|One|Two|
|Speed|Faster|Slower|
|Key Exchange|Difficult|Easier|
|Common Use|Bulk encryption|Authentication|

---

# Security Goals

|Goal|Purpose|
|---|---|
|Confidentiality|Protect secrecy|
|Integrity|Prevent modification|
|Authentication|Verify identity|
|Non-Repudiation|Prevent denial|

---

# Key Takeaways

> [!summary]
> 
> - Cryptography secures communication and data
> - Symmetric encryption uses one shared key
> - AES is the current encryption standard
> - DES is outdated and insecure
> - Asymmetric encryption uses public/private keys
> - RSA and ECC are common asymmetric algorithms
> - Digital signatures provide authentication
> - Cipher modes affect encryption behavior and security
> - GCM provides both confidentiality and integrity