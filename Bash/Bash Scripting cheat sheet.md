Here’s a **high-value Bash scripting cheat sheet** tailored for practical use—focused on flags, patterns, and things you actually need when writing scripts (not fluff).

---

# 🧠 Bash Scripting Cheat Sheet (Practical + Fast Recall)

---

## 🚀 Script Basics

```bash
#!/bin/bash        # Shebang (use /usr/bin/env bash for portability)
set -e             # Exit on error
set -u             # Treat unset variables as errors
set -o pipefail    # Catch pipeline errors
```

👉 Common combo:

```bash
set -euo pipefail
```

---

## 📥 Variables

```bash
name="John"        # No spaces!
echo "$name"       # Always quote variables
```

### Special Variables

```bash
$0    # Script name
$1-$9 # Positional args
$#    # Number of args
$@    # All args (SAFE)
$*    # All args (less safe)
$$    # PID
$?    # Last exit status
```

---

## ⚙️ Input Handling

```bash
read -p "Enter name: " name
read -s password     # Silent (for passwords)
```

---

## 🧪 Conditionals

```bash
if [[ condition ]]; then
  echo "true"
elif [[ condition ]]; then
  echo "else if"
else
  echo "false"
fi
```

### Common Tests

```bash
[[ -f file ]]   # File exists
[[ -d dir ]]    # Directory exists
[[ -z var ]]    # Empty string
[[ -n var ]]    # Not empty
[[ "$a" == "$b" ]]
[[ "$a" != "$b" ]]
[[ "$a" -gt 5 ]]  # Greater than (numbers)
```

👉 ALWAYS use `[[ ... ]]` instead of `[ ... ]` (safer)

---

## 🔁 Loops

### For Loop

```bash
for i in {1..5}; do
  echo "$i"
done
```

```bash
for file in *.txt; do
  echo "$file"
done
```

### While Loop

```bash
while read line; do
  echo "$line"
done < file.txt
```

---

## 🔄 Case Statement

```bash
case "$1" in
  start) echo "Starting" ;;
  stop)  echo "Stopping" ;;
  *)     echo "Unknown" ;;
esac
```

---

## 📤 Functions

```bash
my_func() {
  echo "Hello $1"
}

my_func "World"
```

---

## 🧰 Command Substitution

```bash
result=$(ls)
```

👉 Avoid backticks:

```bash
`ls`  # ❌ old
```

---

## 📦 Arrays

```bash
arr=(one two three)

echo "${arr[0]}"
echo "${arr[@]}"   # All elements
echo "${#arr[@]}"  # Length
```

---

## 🔌 Exit Codes

```bash
command
if [[ $? -ne 0 ]]; then
  echo "Error"
fi
```

👉 Better:

```bash
if ! command; then
  echo "Error"
fi
```

---

## 🧯 Debugging

```bash
set -x   # Debug mode (prints commands)
set +x   # Turn off
```

---

## 🧵 Redirection & Pipes

```bash
command > file      # stdout
command >> file     # append
command 2> error    # stderr
command &> all.log  # stdout + stderr
```

```bash
cmd1 | cmd2         # Pipe
```

---

## 📌 Important Flags (You MUST Know)

### `set` Flags

|Flag|Meaning|
|---|---|
|`-e`|Exit on error|
|`-u`|Undefined variable = error|
|`-x`|Debug mode|
|`-o pipefail`|Fail if any pipe fails|

---

### `read` Flags

|Flag|Meaning|
|---|---|
|`-p`|Prompt|
|`-s`|Silent input|
|`-r`|Raw (no escape processing)|

---

### `echo` Flags

```bash
echo -e "Line1\nLine2"   # Enable escapes
echo -n "No newline"
```

---

### `getopts` (Argument Parsing)

```bash
while getopts "u:p:" opt; do
  case $opt in
    u) user=$OPTARG ;;
    p) pass=$OPTARG ;;
    *) echo "Invalid option" ;;
  esac
done
```

---

## ⚠️ Common Pitfalls (Critical)

### ❌ Unquoted variables

```bash
rm $file   # BAD
rm "$file" # GOOD
```

---

### ❌ Using `ls` in scripts

```bash
for f in $(ls)   # BAD
for f in *       # GOOD
```

---

### ❌ Spaces in assignment

```bash
var = 5   # WRONG
var=5     # CORRECT
```

---

## 🔐 File & Permission Checks

```bash
[[ -r file ]]  # Readable
[[ -w file ]]  # Writable
[[ -x file ]]  # Executable
```

---

## 🧪 One-Liners You’ll Actually Use

```bash
# Check if command exists
command -v nmap >/dev/null || echo "Not installed"

# Infinite loop
while true; do echo "Running"; sleep 1; done

# Timestamp
date +"%Y-%m-%d %H:%M:%S"
```

---

## 🧠 Pro Tips (From Real-World Use)

- Always start scripts with:
    
    ```bash
    set -euo pipefail
    ```
    
- Use functions for modular scripts
    
- Validate input early
    
- Log everything in automation scripts
    
- Combine with tools (curl, grep, awk, sed) → real power
    

---

## 🔥 Mini Template (Production Ready)

```bash
#!/usr/bin/env bash
set -euo pipefail

log() {
  echo "[$(date +'%H:%M:%S')] $*"
}

main() {
  if [[ $# -lt 1 ]]; then
    echo "Usage: $0 <target>"
    exit 1
  fi

  target="$1"
  log "Scanning $target"

  # Example
  ping -c 1 "$target" >/dev/null && log "Host is up" || log "Host down"
}

main "$@"
```

---

If you want, I can build you a **Bash scripting cheat sheet specifically for pentesting workflows (automation, recon, brute force pipelines, logging, parallel execution)**—that would align perfectly with your offensive security work.