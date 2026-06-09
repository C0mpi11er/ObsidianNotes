## 🛰️ POP3 (Post Office Protocol 3) Command Cheat Sheet

> [!info] 🧠 What is POP3?
> 
> Text
> 
> ```unset
> Standard application-layer internet protocol used by email clients to retrieve and download emails from a remote mail server over an IP network
> ```
> 
> 💡 Allows clients to:
> 
> - Download email messages to a local computer.
> - Read messages offline without an active internet connection.
> - Clear mailbox space on the server by automatically removing downloaded messages.
> 
> ✔ Common in:
> 
> - Legacy email clients, automated mail processing scripts, and simple inbound mail retrieval configurations.

> [!tip] 🔧 Core Idea
> 
> Text
> 
> ```unset
> Client (Mail User Agent / MUA) ↔ Server (Mail Delivery Agent / MDA)
> ```
> 
> 💡 Key Ports:
> 
> - TCP 110 → Default unencrypted POP3 listening port.
> - TCP 995 → Secure POP3S listening port (POP3 over TLS/SSL).
> 
> ✔ Think of it as a digital physical mailbox where you pull all your letters out, put them in your pocket, and walk away

## 🌳 Structure: Session States & Server Interaction

> [!info] 📚 Session State Engine
> 
> Text
> 
> ```unset
> Authorization State → Transaction State → Update State
> ```
> 
> 💡 Function:
> 
> - Enforces strict transactional sequential steps: verify identity first, manipulate emails second, commit structural server changes last.

> [!tip] 📥 Data Movement: Network Streaming
> 
> Text
> 
> ```unset
> Single-Line Response   # Commences with '+OK' for success or '-ERR' for execution failure
> Multi-Line Response    # Transmits bulk text data stream terminated by a single dot '.' on its own line
> ```
> 
> 💡 Logic:
> 
> - Interaction relies entirely on raw plain-text commands issued directly at the start of a terminal line without prefixes or indicators.

## 🔐 Access Methods

|Authentication Type|Credentials Required|Risk Level|Notes|
|---|---|---|---|
|Plaintext Auth (USER/PASS)|Valid Username + Cleartext Password|HIGH|Transmits passwords in cleartext; vulnerable to network sniffing if not wrapped in TLS|
|APOP Authentication|Valid Username + Shared Secret MD5 Digest|MEDIUM|Prevents cleartext password transmission; requires the server to broadcast a timestamp challenge|
|SASL / STLS Transition|Valid Identity Token + TLS Handshake Encryption|LOW|Upgrades an unencrypted session securely before passing sensitive account authentication strings|

## 🔍 Footprinting & Connection Verification

> [!success] 📡 Key Verification Commands
> 
> Text
> 
> ```unset
> # 1. Establish an unencrypted raw TCP session stream
> nc -nv <target_ip> 110
> 
> # 2. Establish a secure encrypted TLS communication channel
> openssl s_client -connect <target_ip>:995 -crlf
> 
> # 3. Query supported operational extension features post-connection
> CAPA
> ```
> 
> 💡 Flag breakdown:
> 
> - `-crlf` converts your terminal's Enter keystrokes to `\r\n` network line-endings required by internet mail specifications.
> - `-nv` skips DNS resolution lookups and displays verbose connection diagnostics directly inside your command line.

> [!info] 🔍 Information Revealed
> 
> - Server Banner Details: Exposes software deployment names and explicit build versions on initial connection handshake.
> - CAPA Feature Extensions: Reveals support for advanced mechanisms like operational transport security (STLS) or specific login profiles (APOP).

## ⚠️ Common Misconfigurations

> [!danger] 🔓 Unencrypted Port exposure & Excessive Session Timeouts
> 
> Text
> 
> ```unset
> Network Exposure: External TCP 110 open without enforced TLS wrappers
> Session Parameter: Keep-Alive Timeouts configured past 10 minutes without inactivity teardowns
> ```
> 
> 💡 Impact:
> 
> - No Enforced TLS: Exposes raw administrative mail account passwords to cleartext packet capture interception on shared local pathways.
> - Bloated Timeouts: Allows abandoned or dead connection threads to persist, locking mailboxes from legitimate owner synchronization attempts.

## 💥 Protocol-Specific Actions

> [!warning] ⚙️ Mailbox Status Verification
> 
> Text
> 
> ```unset
> STAT
> ```
> 
> 💡 Impact:
> 
> - Requests an instant numeric summary statement containing total message volume alongside exact storage footprint sizes.
> - Returns structural details directly without parsing single email indexes (e.g., yielding response `+OK 1 1630`).

> [!danger] 🎯 Message Processing Retrieval (`RETR`)
> 
> Text
> 
> ```unset
> # 1. Query individual messages to capture unique database footprints
> UIDL
> # 2. Instruct the mailbox server to stream down message target index 1
> RETR 1
> ```
> 
> 💡 Risk:
> 
> - Downloads the complete multi-line email structure including tracing headers, routing histories, and raw multi-part body blocks.
> - Pulls potentially large attachments over network channels, requiring downstream parsing tools to extract raw payload text streams.

> [!danger] 🔑 Destructive Mail Deletion (`DELE`)
> 
> Text
> 
> ```unset
> # 1. Mark a specified item storage index number for permanent deletion
> DELE 1
> # 2. Terminate the active thread connection to commit file deletion updates
> QUIT
> ```
> 
> 💡 Risk:
> 
> - Marks designated message pointers inside file arrays for irreversible erasure once user logout states finish gracefully.
> - Can be undone using the `RSET` command, provided the action occurs before issuing the final transactional disconnect signal.

## ⚡ Real-World Workflow

> [!success] 🧠 Interacting Flow
> 
> Text
> 
> ```unset
> 1. Initialize an open terminal channel into TCP Port 110 or Port 995 using manual stream network clients.
> 2. Submit account credentials using the USER and PASS commands sequentially to pass authentication phases.
> 3. Issue the 'STAT' command to pull down overall file counts and current byte storage properties.
> 4. Query individual mail structures using 'LIST' or run 'UIDL' to extract unique session index tracking hashes.
> 5. Preview the beginning lines of target entries safely by leveraging the 'TOP [msg_id] [lines]' syntax layout.
> 6. Commit structural message drops with 'DELE', then invoke 'QUIT' to close out the session context cleanly.
> ```
> 
> 💡 Always review connection statuses using 'STAT' before initiating large loops to track dynamic data shifts on target hosts.

## 🧩 Mental Model

> [!quote] 🎯 Protocol Hierarchy
> 
> Text
> 
> ```unset
> Transaction State → The Core Vault
> USER / PASS → Slipping your physical identification badge through the access gate slit
> STAT / LIST → Checking the item tally sheet clipboard hanging on the wall rack
> RETR / TOP → Pulling an envelope down to open it or simply reading the address stamping on the front jacket
> DELE / QUIT → Dropping file items into a shred bin and locking the door behind you to finish processing
> ```
> 
> 💡 Once the update phase activates via a successful close command, marked structural modifications commit instantly. Manage message indices deliberately to avoid destroying operational production mail tracking targets.

---

Would you like to build an automated script to parse out these server responses, or should we look at how IMAP commands match up against this structure?