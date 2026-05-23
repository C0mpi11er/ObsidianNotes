# Key Exchange Mechanisms

> [!info] Overview  
> Key exchange mechanisms allow two parties to securely exchange cryptographic keys over an insecure network.

## Purpose

- Establish shared secret keys
    
- Enable encrypted communication
    
- Secure protocols like:
    
    - TLS
        
    - VPNs
        
    - SSH
        

---

# Diffie-Hellman (DH)

> [!important] Diffie-Hellman  
> DH allows two parties to generate a shared secret key without previously sharing private information.

---

# DH Characteristics

|Feature|Description|
|---|---|
|Type|Key Exchange|
|Security|Secure with strong parameters|
|Weakness|Vulnerable to MITM attacks|
|Performance|Slower than ECDH|

---

# DH Usage

Commonly used in:

- TLS
    
- VPNs
    
- Secure tunnels
    

---

> [!warning] MITM Vulnerability  
> Classic DH alone does not authenticate communicating parties, making it vulnerable to:
> 
> ```bash
> Man-in-the-Middle (MITM)
> ```

---

# RSA (Rivest–Shamir–Adleman)

> [!important] RSA  
> RSA is a public-key cryptographic algorithm based on large prime number mathematics.

---

# RSA Functions

|Purpose|Description|
|---|---|
|Encryption|Protect data confidentiality|
|Digital Signatures|Verify authenticity|
|Authentication|Verify users/devices|

---

# RSA Usage

Used in:

- SSL/TLS
    
- Digital certificates
    
- Kerberos PKINIT
    
- Secure email
    
- File encryption
    

---

# RSA Characteristics

|Feature|Description|
|---|---|
|Security|Strong with large key sizes|
|Performance|Computationally heavy|
|Basis|Prime factorization|

---

# ECDH (Elliptic Curve Diffie-Hellman)

> [!important] ECDH  
> ECDH is a more efficient version of DH using:
> 
> ```bash
> Elliptic Curve Cryptography (ECC)
> ```

---

# ECDH Advantages

|Advantage|Description|
|---|---|
|Faster|Better performance|
|Smaller Keys|Same security with smaller sizes|
|Forward Secrecy|Protects past sessions|

---

# ECDH Usage

Commonly used in:

- TLS
    
- VPNs
    
- IKE
    
- Secure messaging
    

---

# ECDSA (Elliptic Curve Digital Signature Algorithm)

> [!info] ECDSA  
> ECDSA uses ECC to create digital signatures for authentication and integrity verification.

---

# ECDSA Benefits

|Benefit|Description|
|---|---|
|Efficient|Faster than RSA|
|Strong Security|Uses ECC|
|Smaller Keys|Reduced overhead|

---

# Algorithm Comparison

|Algorithm|Purpose|Notes|
|---|---|---|
|DH|Key Exchange|Vulnerable to MITM|
|RSA|Encryption & Signatures|Heavy but widely used|
|ECDH|Key Exchange|Faster & more secure|
|ECDSA|Digital Signatures|Efficient ECC signatures|

---

# IKE (Internet Key Exchange)

> [!important] IKE  
> IKE is used to establish secure VPN tunnels and exchange cryptographic keys securely.

---

# IKE Uses

- VPN tunnels
    
- Authentication
    
- Security negotiation
    
- Secure session establishment
    

---

# IKE Components

|Technology|Purpose|
|---|---|
|DH/ECDH|Key exchange|
|RSA/ECDSA|Authentication|
|AES|Data encryption|

---

# IKE Modes

## Main Mode

> [!info] Main Mode  
> More secure but slower.

### Characteristics

- 3 phases
    
- Identity protection
    
- More message exchanges
    

---

## Aggressive Mode

> [!warning] Aggressive Mode  
> Faster but less secure.

### Characteristics

- 2 phases
    
- Reduced exchanges
    
- No identity protection
    

---

# Pre-Shared Keys (PSK)

> [!important] PSK  
> A shared secret used for authentication during IKE negotiation.

---

# PSK Advantages

|Advantage|Description|
|---|---|
|Simple|Easy setup|
|Authentication|Verifies parties|

---

# PSK Weaknesses

> [!warning] PSK Weaknesses
> 
> - Difficult secure exchange
>     
> - Vulnerable if intercepted
>     
> - Weak PSKs can be brute-forced
>     

---

# Forward Secrecy

> [!info] Forward Secrecy  
> Ensures old encrypted sessions remain secure even if long-term private keys are compromised later.

Usually provided by:

- ECDH
    
- Ephemeral DH
    

---

# Key Takeaways

> [!summary]
> 
> - DH enables secure shared key generation
>     
> - RSA provides encryption and digital signatures
>     
> - ECDH is faster and more efficient than DH
>     
> - ECDSA provides efficient digital signatures
>     
> - IKE establishes secure VPN sessions
>     
> - Main Mode = more secure
>     
> - Aggressive Mode = faster but weaker
>     
> - PSKs add authentication but must be protected
>     
> - ECC provides strong security with smaller keys
>