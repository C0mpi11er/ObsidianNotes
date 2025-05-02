In the [Cryptography Basics](https://tryhackme.com/r/room/cryptographybasics) room, we explained the modulo operation and said it plays a significant role in cryptography. In the following simplified numerical example, we see the RSA algorithm in action:

1. Bob chooses two prime numbers: _p_ = 157 and _q_ = 199. He calculates _n_ = _p_ × _q_ = 31243.
2. With _ϕ_(_n_) = _n_ − _p_ − _q_ + 1 = 31243 − 157 − 199 + 1 = 30888, Bob selects _e_ = 163 such that _e_ is relatively prime to _ϕ_(_n_); moreover, he selects _d_ = 379, where _e_ × _d_ = 1 mod _ϕ_(_n_), i.e., _e_ × _d_ = 163 × 379 = 61777 and 61777 mod 30888 = 1. The public key is (_n_,_e_), i.e., (31243,163) and the private key is $(n,d), i.e., (31243,379).
3. Let’s say that the value they want to encrypt is _x_ = 13, then Alice would calculate and send _y_ = _x__e_ mod _n_ = 13163 mod 31243 = 16341.
4. Bob will decrypt the received value by calculating _x_ = _y__d_ mod _n_ = 16341379 mod 31243 = 13. This way, Bob recovers the value that Alice sent.

- p and q are large prime numbers
- n is the product of p and q
- The public key is n and e
- The private key is n and d
- m is used to represent the original message, i.e., plaintext
- c represents the encrypted text, i.e., ciphertext



Deffie Hellman cryp analogy
Alright — I’ll explain this to you like you’re hearing it for the first time, **in a very simple way**:

---

### 🎭 Story of Alice and Bob: Secret Key Exchange (Like Passing Notes in Class)

- Imagine **Alice** and **Bob** want to share **a secret** — but they're sitting across the room, and **everyone can hear** what they say.
    
- So they come up with a **smart plan** to **create a secret together**, even though people (like Eve the eavesdropper) are listening.
    

---

### Step-by-Step:

|Step|What Happens|Example|
|:--|:--|:--|
|**1. Agree on some public numbers**|Alice and Bob agree on two numbers that **everyone can see**: a prime number `p` and a small number `g`.|They pick `p = 29` and `g = 3`.|
|**2. Each picks a secret**|They each **choose a secret number** that they **don't tell anyone**.|Alice picks `a = 13`, Bob picks `b = 15`.|
|**3. Create a “Public Key”**|They each **do a little math** with their secret and the public numbers to create a **public key**.|Alice calculates `A = 3^13 mod 29 = 19`. Bob calculates `B = 3^15 mod 29 = 26`.|
|**4. Exchange Public Keys**|They **shout out** their public keys across the room. Everyone hears `19` and `26`, but not their secret numbers.|Alice sends `19`, Bob sends `26`.|
|**5. Create the Shared Secret**|Each person uses **the other’s public key** and **their own secret** to calculate the **shared secret**.|Alice does `26^13 mod 29 = 10`, Bob does `19^15 mod 29 = 10`.|
|**Result**|They **both end up with the same secret number** — without ever actually sending the secret itself!|The shared secret is `10`.|

---

### 🧠 Why This Works

- Even though everyone sees `p`, `g`, `A`, and `B`,
    
- Without knowing **the private secrets** (like `a` or `b`),
    
- **It’s almost impossible** to figure out the final secret number.
    

Because **reversing** that math (finding the secret from the public key) is **super hard** without knowing the secret — especially with big numbers (in real life, not small numbers like 29).

---

### 🧩 Simple Analogy

> It's like **mixing paint colors**:
> 
> - Alice and Bob **agree on a basic color** (like yellow).
>     
> - Each mixes **their own secret color** (like blue or red) with it.
>     
> - They **share the mixed color**.
>     
> - Using their **own secret color** and the **other person's mixed color**, they **recreate the same final color**.
>     
> - But nobody else can figure out what the secret colors were just by seeing the mixed paint!
>     

---

### ✨ Final Summary in One Line:

> **Alice and Bob both end up with the same secret key, without ever sending it directly — even if someone is spying on their conversation.**

---

Would you like me to also draw a **simple diagram** of it for you? 🎨 (really helps to see it visually!)