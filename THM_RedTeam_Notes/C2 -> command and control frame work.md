

![[c2.png]]
A C2 (command-and-control) framework is like a **central coach** (server) controlling a team of players (agents) over radios (network channels). It’s more advanced than a single Netcat listener because it manages many agents, speaks different “languages” (payloads/protocols), and has tools for packaging those agents.

listeners http,dns etc
beacons ->> process of communicating back to c2 server
stageless payload are full
stage payloads are in batches and preferred very easy to obfuscated and less detected by anti virus 
jitters are yous to randomize the beaconing process to reduce alert from blue teams 
dropper->> first payload of a staged payload

SMb_beacon->> allows communication to restricted networks through unrestricted networks,the unrestricted serves as a proxy for communication between restricted and c2 servers

pay load format include PS,c#,vb script,office docs,jscript,etc

placing infrastructure in plain sight...is a challenge most red teamers face ...more like camouflage the whole process to avoid detection
some techniques include 
1.domain fronting -> using cloud fare as a proxy
2.c2 profiles more like a switch statement for proxies routed through a known server e.g cloud fare then cloud fare forwards request to hidden c2 server.....which check for request finger print and sends a return page that is diffrent based on the finger printed request or not

# C2 frameworks

free c2 frame works -> more detectable e.g msfconsole,armitage visualizes it.
> powershell empire 
> convenant mostly used for smb and http
> silver

premiuim less likely and give more feautures e.g cobalt strike
>brute ratel, (#note ensure to check this one out looks cool)

check this vm out https://howto.thec2matrix.com/lab-infrastructure/virtual-machines-with-c2s





# setting up c2 server 
>clone armitage
>run package script
>start prosgrql and check the status
>Lastly, set the `MSF_DATABASE_CONFIG` environment variable to the location of your Metasploit **database.yml** file, 
>which in our case is at `/root/.msf4/database.yml`

```shell-session
root@kali$ export MSF_DATABASE_CONFIG=/root/.msf4/database.yml
```





# Listeners
smb listeners -> popular method of choice because it allows for pivoting 

dns listeners->  mostly used for exfiltration

https listeners -> most used for domain fonting techniques

standard listeners -> send commands in clear text





# redirectors
more ore less load balancers that redirect https/http request base on the body of the req 

if a redirector is not deployed between a victim and c2 command line the c2 will be easily finger printed,reported and taken down 



# setting up apache for redirection

certain modules are necessary for setting up apache for redirection e.g 
- rewrite
- proxy
- proxy_http
- headers
