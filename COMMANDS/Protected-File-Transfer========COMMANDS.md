
---

>[!ABSTRACT] **File Encryption Methodology**

When exfiltrating sensitive data (like `NTDS.dit`), encryption is mandatory to prevent data leakage if the transfer is intercepted. This section covers **AES-256** implementation on both Windows and Linux.

---

>[!EXAMPLE] **Windows: Invoke-AESEncryption.ps1 (Full Script)**

> [!Note] **Attacker / Preparer (Target Side)**
> 
> Save the following code as `Invoke-AESEncryption.ps1` on the target machine.

PowerShell

```
function Invoke-AESEncryption {
    [CmdletBinding()]
    Param (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,
        [Parameter(Mandatory = $true)]
        [String]$Key,
        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,
        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )
    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }
    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))
        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }
                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    return "File encrypted to $outPath"
                }
            }
            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }
                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    return "File decrypted to $outPath"
                }
            }
        }
    }
}
```

---

>[!TIP] **Windows Usage Commands**

> [!Note] **Attacker / Preparer (Target Side)**

PowerShell

```
# 1. Import the function
Import-Module .\Invoke-AESEncryption.ps1

# 2. Encrypt a file (Output: file.txt.aes)
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

# 3. Decrypt the file
Invoke-AESEncryption -Mode Decrypt -Key "p4ssw0rd" -Path .\scan-results.txt.aes
```

---

>[!TIP] **Linux Usage: OpenSSL**

> [!Note] **Attacker / Preparer (Target Side)**

Bash

```
# Encrypting /etc/passwd
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
```

> [!SUCCESS] **Decryption (Retrieval Side)**

Bash

```
# Decrypting on your Arch Linux machine
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```

---

>[!CAUTION] **Operational Security (OpSec)**

- **Password Policy:** Use unique passwords per client. Leaked passwords from one test should not compromise another.
    
- **Metadata:** This script maintains the `LastWriteTime` of the original file, which helps avoid basic forensic detection by timestamp mismatch.
    
- **PII/Financials:** Never exfiltrate real PII. Use dummy data to demonstrate exfiltration capability to the client.
    

---

>[!CHECK] **Verification Checklist**

- [ ] Is the `.aes` or `.enc` file created?
    
- [ ] Is the original file deleted after encryption? (Crucial for OpSec).
    
- [ ] Can you decrypt a sample file on your MSI Raider locally?
    

