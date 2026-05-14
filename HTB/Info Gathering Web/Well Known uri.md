
---

# 🌐 Well-Known URIs (.well-known) — Web Recon Cheat Notes

> [!ABSTRACT]  
> The `.well-known` directory (RFC 8615) is a standardized endpoint used to expose structured metadata about a domain. It often leaks configuration, authentication, and security-related files useful in reconnaissance.

---

## 📌 What is `.well-known`?

> [!INFO]  
> A reserved path under:

```txt
https://target.com/.well-known/
```

It centralizes:

- Authentication configs
    
- Security policies
    
- Identity provider metadata
    
- App verification files
    

Think of it as a **public config registry for a website**.

---

## 🧠 Why it matters in Web Recon

> [!IMPORTANT]  
> `.well-known` often exposes **high-value attack surface metadata**

### Common recon value:

- 🔍 API and auth endpoints
    
- 🔐 OAuth / OpenID configs
    
- 📱 Mobile app verification files
    
- 📧 Email security policies
    
- 🧭 Hidden infrastructure hints
    

---

## 📂 Key `.well-known` Endpoints

> [!NOTE]

|Endpoint|Purpose|Recon Value|
|---|---|---|
|`security.txt`|Security contact info|Bug bounty / reporting paths|
|`openid-configuration`|OAuth/OIDC metadata|Auth endpoints + token flow|
|`assetlinks.json`|Android app verification|App ↔ domain linkage|
|`mta-sts.txt`|Email security policy|Mail infra details|
|`change-password`|Password reset entry point|Account takeover surface|

---

## 🔥 High-Value Recon Target

### OpenID Configuration

> [!EXAMPLE]

```txt
https://target.com/.well-known/openid-configuration
```

### Typical response:

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks"
}
```

---

## 🧨 What you extract from it

> [!CHECK]  
> From OpenID metadata, you can map:

- 🔑 Authentication entry points
    
- 🎟️ Token issuance endpoints
    
- 👤 User info APIs
    
- 🔐 Signing keys (JWKS)
    
- ⚙️ OAuth flow type (code, token, id_token)
    

---

## 🧭 Recon Methodology

> [!TIP]  
> Use this workflow:

### 1. Manual check

```bash
curl -s https://target.com/.well-known/
```

### 2. Target known endpoints

```bash
curl -s https://target.com/.well-known/openid-configuration
curl -s https://target.com/.well-known/security.txt
```

### 3. Parse output

```bash
curl -s URL | jq
```

---

## ⚠️ Security Reality

> [!WARNING]  
> Even though `.well-known` is “standardized”, it often leaks:

- Internal auth architecture
    
- Hidden APIs
    
- Misconfigured identity providers
    
- Forgotten services
    

---

## 🧠 Recon Insight

> [!TLDR]  
> `.well-known` = **structured OSINT goldmine for authentication + infrastructure discovery**

---

If you want, I can next turn this into:

- 🧾 **OSCP-style checklist for `.well-known` enumeration**
    
- ⚡ **One-liner curl + ffuf fuzzing commands for it**
    
- 🧠 Or integrate it into your full **web recon pipeline cheat sheet (DNS → VHOST → CT logs → crawling → fingerprinting)**