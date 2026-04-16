### Basic Tools (Pentesting Essentials)

- **SSH (Secure Shell)**
    - Remote access protocol (port 22).
    - Supports password & key-based authentication.
    - Stable shell access → used for persistence, pivoting, file transfer, and port forwarding.
    - Example:
        
        ssh user@target_ip
        
- **Netcat (nc)**
    - Network utility for interacting with TCP/UDP ports.
    - Uses:
        - Banner grabbing (identify services)
        - Connecting to shells
        - File transfer
    - Example:
        
        nc target_ip port
        
    - Related tool: **socat** → more advanced (TTY upgrade, port forwarding).
- **Tmux**
    - Terminal multiplexer (multiple sessions/windows in one terminal).
    - Key features:
        - Session management
        - Window/pane splitting
        - Useful for multitasking & logging during engagements
    - Basics:
        - `Ctrl + B, C` → new window
        - `Ctrl + B, %` → vertical split
        - `Ctrl + B, "` → horizontal split
- **Vim**
    - Terminal-based text editor (common on compromised systems).
    - Modes:
        - Normal → navigation
        - Insert (`i`) → editing
        - Command (`:`) → save/quit
    - Common commands:
        - `:w` save
        - `:q` quit
        - `:wq` save & quit
        - `dd` delete line, `yy` copy line, `p` paste



   >[!TIP] show keys- tmux show-options -gw | grep mode-keys

- **`Ctrl + b`**, then **`[`**

2. Select and Copy

- **Move cursor**: Use **Arrow Keys** or **`Ctrl + n`** (down), **`Ctrl + p`** (up), **`Ctrl + f`** (right), **`Ctrl + b`** (left).
- **Start Selection**: **`Ctrl + Space`** (Sets the "mark").
- **Copy Selection**: **`Alt + w`** (This copies the text to the tmux buffer and exits copy mode).

3. Paste

- **`Ctrl + b`**, then **`]`**