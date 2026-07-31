# 🛰️ Penetration Testing Documentation Guide

## 🔍 Overview & Setup

### 📝 Notetaking Tools
- **Obsidian**    # Encrypted notes, flexible linking
- **CherryTree**  # Hierarchical notes with images and tables
- **Standard Notes**    # Encrypted notes
- **Evernote**          # Traditional note-taking

## 📺 Tmux Logging Setup

### Installation
```bash
# Clone Tmux Plugin Manager
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# Create configuration file
cat > ~/.tmux.conf << EOF
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-logging'

set -g history-limit 50000

run '~/.tmux/plugins/tpm/tpm'
EOF

# Apply configuration
tmux source ~/.tmux.conf
```

### Usage
```bash
# Start new session
tmux new -s assessment

# Install plugins (first time)
# Press: Ctrl+B, Shift+I

# Start logging current session
# Press: Ctrl+B, Shift+P

# Stop logging
# Press: Ctrl+B, Shift+P (again)

# Retroactive logging (save current pane)
# Press: Ctrl+B, Alt+Shift+P

# Screen capture of current pane
# Press: Ctrl+B, Alt+P

# Clear pane history
# Press: Ctrl+B, Alt+C
```

### Key Bindings
```cmd
# Essential Tmux commands:
Ctrl+B, Shift+%     # Split panes vertically
Ctrl+B, Shift+"     # Split panes horizontally  
Ctrl+B, O           # Switch between panes
Ctrl+B, Shift+P     # Start/stop logging
Ctrl+B, Alt+Shift+P # Retroactive logging
Ctrl+B, Alt+P       # Screen capture
```

## 📊 Evidence Collection

### What to Capture
```cmd
# High-priority evidence:
- Command execution and output
- Screenshots of GUI applications
- Network scan results
- Vulnerability scanner output
- Successful exploitation attempts
- Failed attempts (for thoroughness)
- System information and configuration
- Credential discoveries
```

### Screenshot Best Practices
```cmd
# Technical guidelines:
- Include address bar in browser screenshots
- Crop to relevant information only
- Add minimal border for document contrast
- Use annotations (arrows, boxes) for clarity
- Redact credentials and PII properly

# Redaction methods:
✅ Solid black bars (secure)
❌ Pixelation/blurring (reversible)
❌ CSS/HTML styling (easily bypassed)
```

### Terminal Output Formatting
```cmd
# Preferred: Copy-paste terminal text
# Benefits:
- Easier redaction and highlighting
- Smaller file sizes
- Copy-paste friendly for client reproduction
- Professional appearance

# Format example:
┌─[htb-student]─[10.10.14.3]─[~/tools]
└──╼ $ crackmapexec smb 172.16.5.5 -u administrator -p '<REDACTED>'
SMB    172.16.5.5    445    DC01    [+] INLANEFREIGHT.LOCAL\administrator:<REDACTED> (Pwn3d!)
```

## 📝 Artifact Tracking

### Payload Documentation
```cmd
# Essential tracking information:
- Timestamp of payload deployment
- Target host IP/hostname
- File path on target system
- File hash (SHA256/MD5)
- Cleanup status (removed/needs cleanup)
- Purpose/functionality of payload
```

### System Modifications
```cmd
# Required documentation:
- Host IP/hostname where change was made
- Timestamp of modification
- Description of change made
- Location of change on host
- Application/service affected
- Account created (if applicable)
- Reversion status and procedures
```

### Sample Tracking Format
```markdown
## Payload Log

| Timestamp | Host | Path | Hash | Status | Notes |
|-----------|------|------|------|--------|-------|
| 2025-01-15 14:30 | 10.10.10.50 | C:\temp\shell.exe | a1b2c3d4... | Removed | Reverse shell payload |
| 2025-01-15 15:45 | 10.10.10.51 | /var/www/html/cmd.php | e5f6g7h8... | Needs cleanup | Web shell |

## Account Modifications

| Timestamp | Host | Change | Account | Status |
|-----------|------|--------|---------|--------|
| 2025-01-15 16:00 | DC01 | User created | testuser | Removed |
| 2025-01-15 16:15 | WEB01 | Added to Admins | htb-user | Reverted |
```

## 🎯 HTB Academy Lab Solutions

### Lab Questions
```bash
# Question 1: Session logging tool
# Answer: tmux

# Question 2: Vertical pane split key combination
# Answer: [Ctrl] + [B] + [Shift] + [%]
```

### Practical Exercises
```bash
# Optional lab access:
xfreerdp /v:10.129.203.82 /u:htb-student /p:HTB_@cademy_stdnt!

# Activities:
1. Explore Obsidian sample notebook
2. Practice Tmux logging setup
3. Test pane splitting and navigation
4. Experiment with evidence organization
```

## 🔄 Assessment Workflow

### Pre-Assessment Setup
```bash
# 1. Create project directory structure
mkdir -p CLIENT-ASSESSMENT/{Admin,Deliverables,Evidence/{Findings,Scans/{Vuln,Service,Web,'AD Enumeration'},Notes,OSINT,'Logging output','Misc Files'}}

# 2. Initialize notetaking tool (Obsidian/CherryTree)
# 3. Configure Tmux logging
# 4. Set up evidence collection templates
```

### During Assessment
```cmd
# Continuous documentation:
- Log all commands and output
- Screenshot significant findings
- Track credentials in centralized location
- Document failed attempts for thoroughness
- Maintain activity timeline
- Track all uploaded files and modifications
```

### Post-Assessment
```cmd
# Report preparation:
- Organize evidence by findings
- Redact sensitive information
- Verify command reproducibility
- Clean up temporary files
- Archive complete assessment data
```

## ⚠️ Data Handling Guidelines

### What NOT to Collect
```cmd
# Avoid collecting:
- Unredacted PII (personal information)
- Potentially criminal content
- Legally discoverable documents
- Sensitive file contents (screenshot directory listing instead)
- Client proprietary information beyond scope
```

### Compliance Considerations
```cmd
# Legal obligations:
- GDPR compliance for EU clients
- Data retention policies
- Secure storage requirements
- Client data handling agreements
- Evidence chain of custody
```

## 💡 Key Takeaways

1. **Structured approach** essential for comprehensive documentation.
2. **Tmux logging** provides complete session recording.
3. **Evidence organization** saves time during reporting.
4. **Proper redaction** protects sensitive information.
5. **Terminal output preferred** over screenshots when possible.
6. **Artifact tracking** critical for professional assessments.
7. **Tool selection** should match company policies and client requirements.

---