Here’s a **detailed yet concise summary** of essential **normal (non-networking) CMD commands** for file management, system operations, and basic tasks:

---

### **📂 File & Directory Management**  
| **Command** | **Purpose**                          | **Syntax & Usage**                     | **Example**                          |
|-------------|--------------------------------------|----------------------------------------|--------------------------------------|
| **`dir`**   | Lists files/folders in current dir.  | `dir`                                  | `dir /s` (Recursive list)            |
| **`cd`**    | Changes directory.                   | `cd <path>`                            | `cd C:\Users` or `cd ..` (Go back)   |
| **`md`/`mkdir`** | Creates a new folder.          | `mkdir <folder_name>`                  | `mkdir "New Folder"`                 |
| **`rd`/`rmdir`** | Deletes a folder *(empty)*.    | `rmdir <folder_name>`                  | `rmdir /s /q OldFolder` (Force delete) |
| **`copy`**  | Copies file(s).                      | `copy <source> <destination>`          | `copy file.txt C:\Backup\`           |
| **`xcopy`** | Advanced file/folder copying.        | `xcopy <source> <dest> /E /H`          | `xcopy C:\Data D:\Backup /E /H`      |
| **`move`**  | Moves or renames files/folders.      | `move <source> <destination>`          | `move file.txt C:\Docs\`             |
| **`del`**   | Deletes file(s).                     | `del <filename>`                       | `del *.tmp /s` (Delete all `.tmp` files) |
| **`ren`**   | Renames a file/folder.               | `ren <old_name> <new_name>`            | `ren old.txt new.txt`                |
| **`type`**  | Displays file content.               | `type <filename>`                      | `type notes.txt`                     |
| **`tree`**  | Shows folder structure as a tree.    | `tree` or `tree /F` (Files included)   | `tree C:\Projects`                   |

---

### **⚙️ System & Utility Commands**  
| **Command**   | **Purpose**                          | **Syntax & Usage**                     | **Example**                          |
|---------------|--------------------------------------|----------------------------------------|--------------------------------------|
| **`cls`**     | Clears the CMD screen.               | `cls`                                  | Reset terminal clutter.              |
| **`echo`**    | Displays text or toggles echo.       | `echo <text>`                          | `echo Hello World!`                  |
| **`date`**    | Shows/sets system date.              | `date` or `date /T`                    | `date 2024-05-20` (Set date)         |
| **`time`**    | Shows/sets system time.              | `time` or `time /T`                    | `time 14:30:00` (Set time)           |
| **`ver`**     | Displays Windows version.            | `ver`                                  | Quick OS check.                      |
| **`hostname`**| Shows the PC name.                   | `hostname`                             | Identify computer name.              |
| **`whoami`**  | Displays current user.               | `whoami`                               | `whoami /priv` (Check privileges)    |
| **`tasklist`**| Lists running processes.             | `tasklist`                             | `tasklist \| findstr "chrome"`       |
| **`taskkill`**| Ends a process.                      | `taskkill /IM <exe> /F`                | `taskkill /IM notepad.exe /F`        |
| **`shutdown`**| Shuts down/restarts PC.              | `shutdown /s /t 0` (Instant off)       | `shutdown /r` (Restart)              |
| **`start`**   | Opens a file/program.                | `start <file>`                         | `start notepad.exe`                  |
| **`attrib`**  | Changes file attributes.             | `attrib +H <file>` (Hide)              | `attrib -R +H secret.txt`            |

---

### **📝 Text & Scripting Commands**  
| **Command** | **Purpose**                          | **Syntax & Usage**                     | **Example**                          |
|-------------|--------------------------------------|----------------------------------------|--------------------------------------|
| **`find`**  | Searches for text in files.          | `find "text" <file>`                   | `find "error" log.txt`               |
| **`findstr`**| Advanced text search (regex).       | `findstr "pattern" <file>`             | `findstr /i "warning" *.log`         |
| **`more`**  | Displays output page-by-page.        | `type longfile.txt \| more`            | `dir /s \| more`                     |
| **`sort`**  | Sorts text output.                   | `sort <file>`                          | `dir \| sort`                        |
| **`set`**   | Manages environment variables.       | `set` (List vars)                      | `set PATH=C:\Tools;%PATH%`           |
| **`>>`**    | Appends output to a file.            | `echo "text" >> file.txt`              | `dir >> dirlist.txt`                 |
| **`>`**     | Overwrites a file with output.       | `echo "text" > file.txt`               | `ipconfig > network.txt`             |

---

### **🔹 Key Use Cases**  
1. **File Management** → `dir`, `cd`, `copy`, `del`, `ren`  
2. **System Info** → `ver`, `hostname`, `whoami`, `tasklist`  
3. **Batch Scripting** → `echo`, `set`, `>>`, `findstr`  
4. **Quick Fixes** → `cls`, `attrib`, `shutdown`  

---

### **📌 Cheat Sheet for Daily Use**  
| **Task**                | **Command**                     |
|--------------------------|---------------------------------|
| **List files**           | `dir`                           |
| **Navigate folders**     | `cd <path>`                     |
| **Copy a file**          | `copy file.txt C:\Backup\`      |
| **Delete a file**        | `del old.txt`                   |
| **Rename a file**        | `ren old.txt new.txt`           |
| **Clear screen**         | `cls`                           |
| **Check Windows version**| `ver`                           |
| **Kill a process**       | `taskkill /IM notepad.exe /F`   |
| **Restart PC**           | `shutdown /r`                   |

---

### **💡 Pro Tips**  
✔ Use **Tab** to auto-complete file/folder names.  
✔ Combine commands with **`&&`** (e.g., `cd C:\Data && dir`).  
✔ Redirect output with **`>`** (overwrite) or **`>>`** (append).  

