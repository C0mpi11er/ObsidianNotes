Got it ✅  
Here’s a **well-structured Linux `find` command cheat sheet** that’s fast to use, detailed, and copy-paste friendly (great for Obsidian notes).

---

# 🐧 Linux `find` Cheat Sheet

`find [path] [options] [expression] [action]`

---

## 📂 Basic Usage

```bash
find . -name file.txt      # Find file by exact name
find /etc -name "*.conf"   # Find all .conf files in /etc
find . -type d             # Find directories only
find . -type f             # Find files only
```

---

## 🔍 Search by Name

```bash
find . -name "file.txt"          # Case-sensitive
find . -iname "file.txt"         # Case-insensitive
find . -not -name "*.txt"        # Exclude pattern
find . -regex ".*\.txt"          # Regex match
```

---

## 📅 Search by Time

```bash
find . -mtime -1      # Modified in last 1 day
find . -mtime +7      # Modified more than 7 days ago
find . -atime -2      # Accessed in last 2 days
find . -ctime -3      # Changed (metadata) in last 3 days
find . -newer file1   # Modified more recently than file1
```

---

## 📏 Search by Size

```bash
find . -size +100M    # Larger than 100 MB
find . -size -10k     # Smaller than 10 KB
find . -size 1G       # Exactly 1 GB
```

---

## 👤 Search by Ownership & Permissions

```bash
find . -user root             # Owned by root
find . -group www-data        # Belongs to group
find . -perm 644              # Exactly 644 perms
find . -perm -4000            # SUID bit set
find . -perm -u=x             # User has execute permission
```

---

## ⚡ Combine Conditions

```bash
find . -type f -and -name "*.log"
find . -type f -o -type d
find . \( -name "*.sh" -o -name "*.py" \) -executable
```

---

## 🚀 Actions

```bash
find . -name "*.log" -print        # Default action
find . -name "*.log" -delete       # Delete found files
find . -type f -exec cat {} \;     # Run command on each result
find . -type f -exec rm -i {} \;   # Interactive delete
find . -type f -exec ls -lh {} +   # Run ls on results (batched)
```

---

## 📑 Special Uses

```bash
find / -xdev -name "*.conf"     # Stay on same filesystem
find . -empty                   # Find empty files/dirs
find . -maxdepth 1 -name "*.sh" # Limit recursion depth
find . -mindepth 2 -name "*.py" # Skip top-level
```

---

## ⚔️ Real-World Examples

```bash
find /var/log -type f -mtime +30 -delete  
# Delete log files older than 30 days

find . -type f -size +500M -exec ls -lh {} \;  
# List details of files >500MB

find / -perm -4000 -ls 2>/dev/null  
# Find all SUID binaries (good for pentesting)

find /etc -type f -exec grep -H "password" {} \;  
# Search for "password" inside files
```

---

