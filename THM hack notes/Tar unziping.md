
Here’s a **cheat sheet for unzipping `.tar`, `.tar.gz`, `.tgz`, `.tar.bz2`, and `.tar.xz` files** on Linux 🔧

---

## 🗂️ TAR Extraction Cheat Sheet

### 📦 `.tar`

```bash
tar -xvf file.tar
```

### 📦 `.tar.gz` or `.tgz`

```bash
tar -xzvf file.tar.gz
tar -xzvf file.tgz
```

### 📦 `.tar.bz2`

```bash
tar -xjvf file.tar.bz2
```

### 📦 `.tar.xz`

```bash
tar -xJvf file.tar.xz
```

---

## 🔤 Flag Breakdown

| Flag | Meaning                   |
| ---- | ------------------------- |
| `-x` | extract files             |
| `-v` | verbose (show file names) |
| `-f` | file archive name follows |
| `-z` | filter through gzip       |
| `-j` | filter through bzip2      |
| `-J` | filter through xz         |

---

## 📁 Extract to Specific Directory

```bash
tar -xvf file.tar -C /path/to/target/
```

---

## 🧪 Test Mode (List Contents Without Extracting)

```bash
tar -tvf file.tar
```

---


