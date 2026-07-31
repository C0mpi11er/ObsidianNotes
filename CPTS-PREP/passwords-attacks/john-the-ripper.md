---
# 🛰️ John the Ripper (JtR)

> [!ABSTRACT] Overview
>
> **John the Ripper** is a password cracking tool that supports various hashing algorithms and methods such as rule-based, dictionary attacks, and brute-force.

---

## Basic Usage

```bash
john <hash_file>
```

Show cracked passwords:

```bash
john --show <hash_file>
```

---

## Cracking Modes

### Single Crack Mode

- Rule-based, uses username/GECOS info
- Best for Linux credentials
```bash
[[John the Ripper]] --single passwd
```

### Wordlist Mode

- Dictionary attack with wordlist
```bash
[[John the Ripper]] --wordlist=/usr/share/wordlists/rockyou.txt <hash_file>
john --wordlist=<wordlist> --rules <hash_file>  # With rules
```

### Incremental Mode

- Brute-force with statistical model (Markov chains)
- Most exhaustive but slowest
```bash
[[John the Ripper]] --incremental <hash_file>
john --incremental=ASCII <hash_file>  # Custom charset
```

---

## Hash Format Detection

Identify hash format:

```bash
hashid -j <hash>
```

Specify hash format and crack:

```bash
john --format=<format> <hash_file>
```

---

## Common Hash Formats

| Hash Format | Description |
|---|---|
| `raw-md5` | MD5 hashes |
| `raw-sha1` | SHA1 hashes |
| `nt` | Windows NT hashes |
| `mscash/mscash2` | Windows cached credentials |
| `crypt` | Unix crypt(3) hashes |
| `mysql` | MySQL password hashes |

---

## File Cracking Tools

Convert files to JtR format:

```bash
zip2john archive.zip > zip.hash
ssh2john id_rsa > ssh.hash
pdf2john document.pdf > pdf.hash
keepass2john database.kdbx > keepass.hash
office2john document.docx > office.hash
```

Then crack the hashes using John the Ripper:

```bash
[[John the Ripper]] zip.hash
```

---

## Key Options

- `--format=<format>` - Specify hash format
- `--rules` - Apply transformation rules
- `--show` - Display cracked passwords
- `--wordlist=<file>` - Use specific wordlist
- `--incremental` - Brute-force mode
- `--single` - Single crack mode

---

## Configuration

Main configuration file:

```
/etc/john/john.conf
```

Custom charsets and rules can be defined in the configuration. Incremental modes with different character sets (ASCII, UTF8) are also supported.

---