| **Objective**                | **Explanation**                                                                                                                                                                                                                                                                                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Anti-Behavioral Analysis** | Malware attempts to avoid detection by hindering behavioural analysis using tools like sandboxes or debuggers.                                                                                                                                                                                                                                           |
| **Anti-Static Analysis**     | Malware attempts to obstruct or add complexity to static analysis, making it more challenging for security professionals to identify and understand its malicious behaviours and intentions.                                                                                                                                                             |
| **Collection**               | Malware focuses on identifying and gathering information from the targeted machine or network.                                                                                                                                                                                                                                                           |
| **Command and Control**      | Malware typically establishes communication with compromised systems through various methods such as command and control servers, peer-to-peer networks, or other means. This communication allows the malware to control the compromised systems, enabling the attackers to execute commands, exfiltrate data, or carry out other malicious activities. |
| **Credential Access**        | The primary aim of malware is to steal account credentials, such as usernames and passwords.                                                                                                                                                                                                                                                             |
| **Defense Evasion**          | The malware aims to bypass and circumvent the various detection and security mechanisms present within the system to avoid being detected or mitigated.                                                                                                                                                                                                  |

**View More Objective**

|                          |                                                                                                                                                                                                                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Discovery**            | Malware Seeks to collect detailed information about the configuration and setup of the system or network environment, including hardware, software, and network infrastructure.                                                                                                  |
| **Execution**            | Malware is designed to execute unauthorized commands or code on a targeted computer system without the user’s consent. This can include a wide range of harmful activities, such as stealing personal information, damaging files, or gaining unauthorized access to the system. |
| **Exfiltration**         | Malware is designed to infiltrate computer systems or networks to steal and extract sensitive data. This can include personal information, financial details, and any other valuable data stored on the targeted system or network.                                              |
| **Impact**               | Malware aims to manipulate, disrupt, or damage computer systems and data. It can enter computers through infected emails, compromised websites, and other deceptive means, leading to financial loss, privacy breaches, and system instability.                                  |
| **Lateral Movement**     | Malware seeks to spread through the network, either actively through machine access or passively, such as via malicious emails.                                                                                                                                                  |
| **Persistence**          | Malware is intentionally developed with the capability to remain undetected and operational on a computer system for an extended period.                                                                                                                                         |
| **Privilege Escalation** | Malware seeks to infiltrate a computer system or network to gain elevated permissions or control. Once inside the target environment, malware can seek to escalate its privileges, access sensitive information, or take control of system resources for malicious purposes.     |
### Micro-Objective
Micro-objectives are associated with micro-behaviors, which refer to an action or actions exhibited by potentially malicious software that isn't necessarily malicious and may serve various objectives. Example binaries are such as those in messaging apps. However, it's important to **note that these behaviours are typically abused.** That's why CAPA might have flagged this behaviour.

| **Micro-Objective** | **Description**                                                                                                                                          |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **PROCESS**         | exhibiting behaviours related to processes such as but not limited to Creating Process, Setting Thread Context, Terminating Process, and Checking Mutex. |
| **MEMORY**          | exhibiting behaviours such as, but not limited to, Allocating Memory, Changing Memory Protection, and Freeing Memory.                                    |
| **COMMUNICATION**   | exhibiting behaviours such as (not limited to (DNS, FTP, HTTP, ICMP, SMTP) network traffic.                                                              |
| **DATA**            | exhibiting behaviours such as but not limited to Checking strings, compressing, decoding and encoding data                                               |
### MBC Behaviors

The column MBC Behaviors contains behaviours and Micro-behaviors with or without its methods and identifiers. Please check the link [MBC Summary](https://github.com/MBCProject/mbc-markdown/blob/main/mbc_summary.md) for a listing of all MBC content.

Below is a compiled version of Behaviors/Micro-behaviors and its Identifier.

|**Objective**|**Behavior**|Identifiers|Explanation|
|---|---|---|---|
|ANTI-BEHAVIORAL ANALYSIS|**Virtual Machine Detection**|**B0009**|The malware checks to see if it is running in a virtual environment. During its system reconnaissance, the malware examines various user and system artifacts.|
|ANTI-STATIC ANALYSIS|**Executable Code Obfuscation**|**B0032**|Executable code has been intentionally obscured to prevent static code analysis. This is a specific behavior related to the executable code of a malware sample, including its data and text sections.|
|EXECUTION|**Command and Scripting Interpreter**|**E1059**|Malware can exploit command and script interpreters to run malicious commands, scripts, or binaries. It targets built-in interpreters like cmd.exe or PowerShell on Windows or Bash on Unix-like systems. Attackers may also use other scripting languages like Python, Perl, or JavaScript.|
|DISCOVERY|**File and Directory Discovery**|**E1083**|Malware has the capability to search for specific files in particular locations by enumerating files and directories.|
|ANTI-STATIC ANALYSIS, DEFENSE EVASION|**Obfuscated Files or Information**|**E1027**|Malware can obfuscate files or information by encoding, encrypting, or otherwise, making them hard to analyze. It can also encode or encrypt malware samples itself.|

###### Micro-Behavior

The term "low-level behaviors" in malware analysis refers to actions exhibited by malware that aren't necessarily malicious and may serve various objectives. These behaviors are often documented as "micro-behaviors" in the Malware Behavior Characteristics (MBC) analysis. Examples of such low-level behaviors include the creation of TCP sockets and evaluating specific conditions within strings. It's important to **note that just because a behavior is categorized as low-level does not mean it is harmless,** as it may still be part of a larger malicious scheme.

|**Micro-Objective**|**Micro-Behaviors**|Identifiers|Explanation|
|---|---|---|---|
|MEMORY|**Allocate Memory**|**C0007**|Malware frequently utilizes memory allocation as part of its strategy to unpack itself and execute its malicious activities.|
|PROCESS|**Create Process**|**C0017**|Malware creates a process via WMI or shellcode. It can also create a suspended process.|
|COMMUNICATION|**HTTP Communication**|**C0002**|Malware is capable of initiating HTTP communications.|
|DATA|**Check String**|**C0019**|Malware can inspect a string to identify specific characteristics, such as ASCII content, credit card numbers, and string length.|

**View More Tables**

|DATA|**Encode Data**|**C0026**|Malware has the capability to encode data using base64 and XOR.|
|---|---|---|---|
|FILE SYSTEM|**Create Directory**|**C0046**|Malware can create a directory.|
|---|---|---|---|
|FILE SYSTEM|**Delete File**|**C0047**|Malware has the capability to delete a file.|
|---|---|---|---|
|FILE SYSTEM|**Read File**|**C0051**|Malware can read a file.|
|---|---|---|---|
|FILE SYSTEM|**Writes File**|**C0052**|Malware has the capability to write to a file.|
|---|---|---|---|

  

Note that in the final output of CAPA, Behavior and Micro-Behavior are shown only under the **Behavior column**.  

###   

### Methods

Lastly, let’s check the **METHODS**. Below are some methods included in the results from the previous sample. Methods are tied to behaviors; therefore, to fully see all methods, please refer to each specific behavior/micro behavior of interest.

|Behavior|Methods or sub-technique|Identifier|Explanation|
|---|---|---|---|
|Executable Code Obfuscation|**Argument Obfuscation**|B0032.020|Simple number or string arguments to API calls are calculated at runtime, making analysis more difficult.|
|Executable Code Obfuscation|**Stack Strings**|B0032.017|Build and decrypt strings on the stack at each use, then discard to avoid obvious references.|
|HTTP Communication|**Read Header**|C0002.014|HTTP read header.|
|Encode Data|**Base64**|C0026.001|Malware may encode data using Base64.|
|Encode Data|**XOR**|C0026.002|Malware may use XOR to encode data.|
|Obfuscated Files or Information|**Encoding-Standard Algorithm**|E1027.m02|Encoding malware samples, files, or other information uses a standard algorithm (e.g., base64).|

  

Now that we have a good overview and understanding of the MBC's contents, we should be able to explain the result of the previous sample. Therefore, let's do a quick recap using one of the results. Shall we?