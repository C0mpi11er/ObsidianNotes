
Command injection is also often known as “Remote Code Execution” (RCE) because of the ability to remotely execute code within an application. These vulnerabilities are often the most lucrative to an attacker because it means that the attacker can directly interact with the vulnerable system. For example, an attacker may read system or user files, data, and things of that nature.  

For example, being able to abuse an application to perform the command `whoami` to list what user account the application is running will be an example of command injection.

Command injection was one of the top ten vulnerabilities reported by Contrast Security’s AppSec intelligence report in 2019. ([Contrast Security AppSec., 2019](https://www.contrastsecurity.com/security-influencers/insights-appsec-intelligence-report)). Moreover, the OWASP framework constantly proposes vulnerabilities of this nature as one of the top ten vulnerabilities of a web application ([OWASP framework](https://owasp.org/www-project-top-ten/)).




You can often determine whether or not command injection may occur by the behaviours of an application, as you will come to see in the practical session of this room.

Applications that use user input to populate system commands with data can often be combined in unintended behaviour. **For example, the shell operators `;`, `&` and `&&` will combine two (or more) system commands and execute them both**. If you are unfamiliar with this concept, it is worth checking out the [Linux fundamentals module](https://tryhackme.com/module/linux-fundamentals) to learn more about this.

Command Injection can be detected in mostly one of two ways:

1. Blind command injection
2. Verbose command injection

I have defined these two methods in the table below, where the two sections underneath will explain these in greater detail.

|            |                                                                                                                                                                                                                                                                               |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Method** | **Description**                                                                                                                                                                                                                                                               |
| Blind      | This type of injection is where there is no direct output from the application when testing payloads. You will have to investigate the behaviours of the application to determine whether or not your payload was successful.                                                 |
| Verbose    | This type of injection is where there is direct feedback from the application once you have tested a payload. For example, running the `whoami` command to see what user the application is running under. The web application will output the username on the page directly. |

Detecting Blind Command Injection

Blind command injection is when command injection occurs; however, there is no output visible, so it is not immediately noticeable. For example, a command is executed, but the web application outputs no message.

For this type of command injection, we will need to use payloads that will cause some time delay. For example, the `ping` and `sleep` commands are significant payloads to test with. Using `ping` as an example, the application will hang for _x_ seconds in relation to how many _pings_ you have specified.

Another method of detecting blind command injection is by forcing some output. This can be done by using redirection operators such as `>`. If you are unfamiliar with this, I recommend checking out the [Linux fundamentals module](https://tryhackme.com/module/linux-fundamentals). For example, we can tell the web application to execute commands such as `whoami` and redirect that to a file. We can then use a command such as `cat` to read this newly created file’s contents.

Testing command injection this way is often complicated and requires quite a bit of experimentation, significantly as the syntax for commands varies between Linux and Windows.

The `curl` command is a great way to test for command injection. This is because you are able to use `curl` to deliver data to and from an application in your payload. Take this code snippet below as an example, a simple curl payload to an application is possible for command injection.

`curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`


Useful payloads

I have compiled some valuable payloads for both Linux & Windows into the tables below.

Linux

|   |   |
|---|---|
|**Payload**|**Description**|
|whoami|See what user the application is running under.|
|ls|List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.|
|ping|This command will invoke the application to hang. This will be useful in testing an application for blind command injection.|
|sleep|This is another useful payload in testing an application for blind command injection, where the machine does not have `ping` installed.|
|nc|Netcat can be used to spawn a reverse shell onto the vulnerable application. You can use this foothold to navigate around the target machine for other services, files, or potential means of escalating privileges.|

  

Windows

|             |                                                                                                                                                                                        |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Payload** | **Description**                                                                                                                                                                        |
| whoami      | See what user the application is running under.                                                                                                                                        |
| dir         | List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things. |
| ping        | This command will invoke the application to hang. This will be useful in testing an application for blind command injection.                                                           |
| timeout     | This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the `ping` command is not installed.                |