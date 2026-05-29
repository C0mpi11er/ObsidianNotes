> [!TLDR] A master reference guide for standardizing note-taking in Obsidian using custom callout tags. This system creates a visual hierarchy across general reference, methodology execution, hazard warnings, and specialized code documentation.

🧠 What is the Callout System?

A structured framework for organizing and highlighting notes inside Obsidian 💡 Allows administrators to:

- Maintain a clean, readable visual hierarchy
    
- Track attacker methodology in real-time during labs and CTFs
    
- Safeguard operational environments by clearly marking hazards and destructive commands
    

✔ Common in:

- Professional penetration testing notes, OSCP preparation logs, and technical code documentation
    

🔧 Core Idea

Visual Elements ↔ Rapid Triage

💡 Key Ports (Categories):

- **Blue/Cyan** → General Reference & Summaries
    
- **Green/Yellow** → Action, Verification, & Troubleshooting
    
- **Orange/Red** → Warnings, Failures, & Structural Hazards
    
- **Purple/Grey** → Specialized Payload & Code Snippets
    

✔ Think of it as a tactical heads-up display (HUD) for your documentation

🌳 Structure: Callout Tags & Schema

📚 1. General Reference Standard informational blocks used for the foundational data in your notes. 💡 Callout Tags:

- **`[!INFO]`** (Blue) → General information or metadata about a lab/machine.
    
- **`[!NOTE]`** (Blue) → Basic observations or side notes.
    
- **`[!ABSTRACT]`** (Cyan) → Summaries, TL;DRs, or tool descriptions (e.g., "What is Dirsearch?").
    
- **`[!TODO]`** (Cyan) → Tasks you haven't finished in a TryHackMe room.
    

📚 2. Action & Results Perfect for documenting your active "Attacker Methodology." 💡 Callout Tags:

- **`[!SUCCESS]`** (Green) → When a payload hits or you find a flag.
    
- **`[!CHECK]`** (Green) → Verification steps (e.g., "Checking if `Object.prototype` is polluted").
    
- **`[!DONE]`** (Green) → Completed tasks.
    
- **`[!QUESTION]`** (Yellow) → Things you don't understand yet (e.g., "Why is there a backslash here?").
    
- **`[!HELP]`** (Yellow) → For when you're stuck and need to refer to a walkthrough.
    

📚 3. Warnings & Hazards CRITICAL for security notes to avoid "nuking" a target or your own environment. 💡 Callout Tags:

- **`[!WARNING]`** (Orange) → "Don't run this exploit on a production server."
    
- **`[!CAUTION]`** (Orange) → Tricky syntax areas (like the `eval` breakout).
    
- **`[!ATTENTION]`** (Orange) → Important details you might miss.
    
- **`[!FAILURE]`** (Red) → Documenting why an exploit _didn't_ work (essential for OSCP prep).
    
- **`[!DANGER]`** (Red) → Commands that are destructive or irreversible.
    
- **`[!ERROR]`** (Red) → Specific console errors or kernel panics.
    

📚 4. Special Formatting Great for specialized content, technical snippets, and raw captures. 💡 Callout Tags:

- **`[!BUG]`** (Red) → Documenting a CVE or a specific bug in your Unreal Engine C++ code.
    
- **`[!EXAMPLE]`** (Purple) → For code snippets and payload strings.
    
- **`[!QUOTE]`** (Grey) → For copying text directly from a lab's instructions or a man page.
    
- **`[!TLDR]`** (Cyan) → For a high-level overview of a complex topic like Prototype Pollution.
    

🔐 Protocol Implementations SyntaxExamplesNotesStandard Info Block`> [!INFO]` Use for machine IPs, names, and environment detailsMethodology Check`> [!CHECK]` Track specific vulnerability validationsExploit Failure Log`> [!FAILURE]` Critical for post-mortem analysis and OSCP prep