- **DSA (Digital Signature Algorithm)** is a public-key cryptography algorithm specifically designed for digital signatures.
- **ECDSA (Elliptic Curve Digital Signature Algorithm)** is a variant of DSA that uses elliptic curve cryptography to provide smaller key sizes for equivalent security.
- **ECDSA-SK (ECDSA with Security Key)** is an extension of ECDSA. It incorporates hardware-based security keys for enhanced private key protection.
- **Ed25519** is a public-key signature system using EdDSA (Edwards-curve Digital Signature Algorithm) with Curve25519.
- **Ed25519-SK (Ed25519 with Security Key)** is a variant of Ed25519. Similar to ECDSA-SK, it uses a hardware-based security key for improved private key protection.



---

# 🔐 Cryptography Topics: How They Are Utilized

---

## 📜 RSA (Rivest–Shamir–Adleman)

> **Purpose**: Securely encrypt small pieces of data (like keys), authenticate identity (digital signatures).

|Use Case|How it Works|
|:--|:--|
|**Encrypt data**|Encrypt a secret (like a symmetric key) with the receiver’s public RSA key. Only they can decrypt it with their private key.|
|**Sign data**|Sign a hash of a document using your private key. Anyone can verify the signature with your public key.|
|**SSL/TLS Handshakes**|Used to exchange session keys securely between browsers and websites during HTTPS connections.|

---

## 🔄 Diffie-Hellman Key Exchange

> **Purpose**: Securely create a **shared secret** between two people who have never met before.

|Use Case|How it Works|
|:--|:--|
|**Secure channel creation**|Two parties agree on a public prime and base, each picks a private number, exchanges partial keys, and compute the same secret key without directly sending it.|
|**VPN Connections**|Used in protocols like IPSec to create shared session keys for encrypting traffic.|
|**TLS (HTTPS)**|Part of the handshake process in modern websites (called DHE — Diffie-Hellman Ephemeral).|

---

## 🔑 SSH Key Pairs

> **Purpose**: Log in to servers without typing a password (more secure).

|Use Case|How it Works|
|:--|:--|
|**Server authentication**|User creates a key pair. Public key is uploaded to the server (in `~/.ssh/authorized_keys`). Private key stays on the user’s machine.|
|**Automation**|Used in scripts or DevOps pipelines where secure, non-interactive access to servers is needed.|
|**Remote backups / Git operations**|SSH keys allow pushing and pulling code securely without password prompts.|

---

## ✍️ Digital Signatures

> **Purpose**: Verify **who sent a message** and **that it hasn’t changed**.

|Use Case|How it Works|
|:--|:--|
|**Software distribution**|Developers sign software packages. You can verify the signature to be sure the package is legit and safe.|
|**Document verification**|Legal documents, contracts, and PDFs are digitally signed. Verifying the signature proves authenticity.|
|**Secure emails (S/MIME, PGP)**|Ensures the sender really sent the email and that it wasn’t tampered with.|

---

## 🎖️ Digital Certificates

> **Purpose**: Prove that a public key belongs to a specific entity (website, person, company).

|Use Case|How it Works|
|:--|:--|
|**HTTPS websites**|When you visit `https://example.com`, your browser checks the website’s SSL/TLS certificate issued by a trusted CA.|
|**Secure online transactions**|Certificates ensure you are connecting to the real bank website, not an imposter.|
|**Email signing and encryption**|Certificates can also be used to secure and sign emails via protocols like S/MIME.|

---

## 📨 OpenPGP (Pretty Good Privacy)

> **Purpose**: Encrypt, sign, and verify emails, files, and messages easily using key pairs.

|Use Case|How it Works|
|:--|:--|
|**Encrypt emails**|Encrypt an email with the recipient’s public key. Only they can decrypt it with their private key.|
|**Sign emails**|Sign an email with your private key. Others can verify you as the sender.|
|**Secure file sharing**|Encrypt files before sending them over insecure channels (e.g., cloud storage).|
|**GPG CLI usage**|Use tools like `gpg --encrypt` and `gpg --sign` in the terminal to handle files securely.|

---

# 📌 General Summary

|Topic|Key Use|
|:--|:--|
|RSA|Encrypt keys, sign data, secure web sessions|
|Diffie-Hellman|Securely create shared session keys|
|SSH Keys|Passwordless and secure server login|
|Digital Signatures|Verify authenticity and integrity|
|Certificates|Prove identity of websites or users|
|OpenPGP|Encrypt and sign emails, files|

---

Awesome — here’s a **Quick Commands Cheat Sheet** for GPG you can paste into your **OpenPGP** section in Obsidian:  
(minimal, clean, and actionable 🚀)

---

# 📜 GPG Quick Commands Cheat Sheet

---

## 🔑 Key Management

|Task|Command|
|:--|:--|
|Generate a new key pair|`gpg --full-generate-key`|
|List your keys|`gpg --list-keys`|
|List secret (private) keys|`gpg --list-secret-keys`|
|Export your public key|`gpg --export --armor your@email.com > publickey.asc`|
|Import a public key|`gpg --import publickey.asc`|
|Delete a public key|`gpg --delete-key key_ID`|
|Delete a private key|`gpg --delete-secret-key key_ID`|

---

## 🛡️ Encrypt and Decrypt

|Task|Command|
|:--|:--|
|Encrypt a file for someone|`gpg --encrypt --recipient user@email.com file.txt`|
|Decrypt a file|`gpg --decrypt file.txt.gpg > file.txt`|

---

## ✍️ Sign and Verify

|Task|Command|
|:--|:--|
|Sign a file (detached signature)|`gpg --detach-sign file.txt`|
|Verify a signed file|`gpg --verify file.txt.sig file.txt`|

---

## 📦 Encrypt + Sign

|Task|Command|
|:--|:--|
|Encrypt and sign a file at once|`gpg --encrypt --sign --recipient user@email.com file.txt`|

---

## 📨 Encrypt and Send Email (with GPG manually)

|Steps|Description|
|:--|:--|
|Step 1|Export your recipient’s public key.|
|Step 2|Encrypt your email body: `gpg --encrypt --armor --recipient user@email.com message.txt`|
|Step 3|Paste the encrypted text into the email body and send.|

---

# ⚡ Useful Flags

|Flag|Purpose|
|:--|:--|
|`--armor`|Output in readable text (instead of binary)|
|`--recipient`|Specify the email address or key ID|
|`--sign`|Sign the message|
|`--encrypt`|Encrypt the message|
|`--decrypt`|Decrypt the message|

---

