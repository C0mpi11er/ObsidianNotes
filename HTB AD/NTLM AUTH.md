
---

# 🔑 AD Authentication: NTLM, LM, NTLMv1/v2 & DCC

Besides Kerberos/LDAP, Active Directory supports legacy and fallback authentication mechanisms that are frequently abused in attacks.

---

## 📊 Protocol Comparison (High Level)

|Mechanism|Mutual Auth|Uses Hash|Trusted Party|Notes|
|---|---|---|---|---|
|**NTLM / v1 / v2**|❌|NT / LM|Domain Controller|Challenge–response|
|**Kerberos**|✅|Ticket-based|KDC/DC|Preferred & more secure|

---

## 🧓 LM Hash (LANMAN)

Legacy password hash (pre–Vista).

**Weaknesses**

- Max 14 chars, upper-case only
    
- Split into two 7-char chunks
    
- Uses DES + static string (`KGS!@#$%`)
    
- Easy GPU cracking
    
- No salt
    

**Storage**

- SAM (local)
    
- NTDS.dit (DC)
    

⚠️ Disabled by default since Vista/2008 but still found in legacy estates.

---

## 🔐 NT Hash (NTLM)

Modern Windows password hash.

**Algorithm**

```
MD4(UTF-16-LE(password))
```

**Used in**

- NTLM challenge–response
    
- Pass-the-Hash attacks
    

**Properties**

- No salt
    
- Crackable offline
    
- Supports full Unicode
    

**NTLM Dump Format**

```
user:RID:LMhash:NThash:::
```

---

## 🔁 NTLM Authentication Flow

1. NEGOTIATE
    
2. CHALLENGE
    
3. AUTHENTICATE
    

**Abuses**

- Pass-the-Hash
    
- Offline cracking
    
- Relay attacks
    

---

## ⚠️ NTLMv1 (Net-NTLMv1)

Legacy network auth protocol.

**Details**

- Uses LM + NT hashes
    
- Server sends 8-byte challenge
    
- Client returns 24-byte DES response
    
- Crackable offline
    
- ❌ Cannot be pass-the-hashed
    

---

## 🛡️ NTLMv2 (Net-NTLMv2)

Default since Windows Server 2000.

**Improvements**

- HMAC-MD5
    
- Client + server challenges
    
- Timestamp & domain included
    
- Harder to crack
    

**Still vulnerable to**

- Relay attacks
    
- Offline cracking with weak passwords
    

---

## 💾 Domain Cached Credentials (MSCache / DCC2)

Used when DC is unreachable.

**Stored In**

- Registry: `HKLM\Security\Cache`
    
- Last 10 domain logons
    

**Properties**

- Cannot be pass-the-hashed
    
- Very slow to crack
    
- Requires local admin to dump
    

**Format**

```
$DCC2$10240#user#hash
```

---

## 📝 Key Takeaways

- Kerberos is preferred whenever possible
    
- LM = extremely weak
    
- NTLM hashes enable Pass-the-Hash
    
- NTLMv1 is obsolete & crackable
    
- NTLMv2 is stronger but still abused
    
- DCC hashes are painful to crack → low ROI
    

---

