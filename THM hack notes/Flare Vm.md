Foresnsic logic Analysisi and Reverse Engineering
## Reverse Engineering & Debugging

Reverse engineering is like solving a puzzle backward: you take a finished product apart to understand how it works. Debugging is identifying errors, understanding why they happen, and correcting the code to prevent them.

- **Ghidra** - NSA-developed open-source reverse engineering suite.  
    
- **x64dbg** - Open-source debugger for binaries in x64 and x32 formats.  
    
- **OllyDbg** - Debugger for reverse engineering at the assembly level.  
    
- **Radare2** - A sophisticated open-source platform for reverse engineering.
- **Binary Ninja** - A tool for disassembling and decompiling binaries.  
    
- **PEiD** - Packer, cryptor, and compiler detection tool.

## Disassemblers & Decompilers

Disassemblers and Decompilers are crucial tools in malware analysis. They help analysts understand malicious software’s behaviour, logic, and control flow by breaking it into a more understandable format. The tools mentioned below are commonly used in this category.

- **CFF Explorer** - A PE editor designed to analyze and edit Portable Executable (PE) files.
- **Hopper Disassembler** - A Debugger, disassembler, and decompiler.
- **RetDec** - Open-source decompiler for machine code.

## Static & Dynamic Analysis

Static and dynamic analysis are two crucial methods in cyber security for examining malware. Static analysis involves inspecting the code without executing it, while dynamic analysis involves observing its behaviour as it runs. The tools mentioned below are commonly used in this category.

- **Process Hacker** - Sophisticated memory editor and process watcher.
- **PEview** - A portable executable (PE) file viewer for analysis.
- **Dependency Walker** - A tool for displaying an executable’s DLL dependencies.
- **DIE (Detect It Easy)** - A packer, compiler, and cryptor detection tool.

## Forensics & Incident Response

Digital Forensics involves the collection, analysis, and preservation of digital evidence from various sources like computers, networks, and storage devices. At the same time, Incident Response focuses on the detection, containment, eradication, and recovery from cyberattacks. The tools mentioned below are commonly used in this category.

- **Volatility** - RAM dump analysis framework for memory forensics.
- **Rekall** - Framework for memory forensics in incident response.
- **FTK Imager** - Disc image acquisition and analysis tools for forensic use.

## Network Analysis

Network Analysis includes different methods and techniques for studying and analysing networks to uncover patterns, optimize performance, and understand the underlying structure and behaviour of the network.

- **Wireshark** - Network protocol analyzer for traffic recording and examination.  
    
- **Nmap** - A vulnerability detection and network mapping tool.  
    
- **Netcat** - Read and write data across network connections with this helpful tool.

## File Analysis

File Analysis is a technique used to examine files for potential security threats and ensure proper file permissions.

- **FileInsight** - A program for looking through and editing binary files.
- **Hex Fiend** - Hex editor that is light and quick.
- **HxD** - Binary file viewing and editing with a hex editor.

## Scripting & Automation

Scripting and Automation involve using scripts such as PowerShell and Python to automate repetitive tasks and processes, making them more efficient and less prone to human error.

- **Python** - Mainly automation-focused on Python modules and tools.
- **PowerShell Empire** - Framework for PowerShell post-exploitation.

## Sysinternals Suite

The Sysinternals Suite is a collection of advanced system utilities designed to help IT professionals and developers manage, troubleshoot, and diagnose Windows systems.

- **Autoruns** - Shows what executables are configured to run during system boot-up.
- **Process Explorer** - Provides information about running processes.
- **Process Monitor** -Monitors and logs real-time process/thread activity.

  

Have you checked all the categories? There are a lot, right? Worry not, as we won't tackle all those tools right now—it could take months to finish! We want to present you with the concept of having a single box filled with tools for different purposes, giving you an idea of what could work best for the specific task.

In the next task, we will discuss the standard tools used for investigation

| **Tool**         | **Investigative Value**                                                                                                           |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Procmon          | A helpful tool for tracking system activity, especially regarding malware research, troubleshooting, and forensic investigations. |
| Process Explorer | Allows you to see the Process of the Parent-child relationship, DLLs loaded, and its path.                                        |
| HxD              | Malicious files can be examined or altered via hex editing.                                                                       |
| Wireshark        | Observing and investigating network traffic to look for unusual activity.                                                         |
| CFF Explorer     | Can generate file hashes for integrity verification, authenticate the source of system files, and validate their validity.        |
| PEStudio         | Static analysis or studying executable file properties without running the files.                                                 |
| FLOSS            | Extracts and de-obfuscates all strings from malware programs using advanced static analysis techniques.                           |
step of flare
1. static analysis
  

## Analysis using PEStudio
For the computed MD5 `9FDD4767DE5AEC8E577C1916ECC3E1D6` and SHA-1 `A1BC55A7931BFCD24651357829C460FD3DC4828F` hashes, comparisons with established databases like [VirusTotal](https://www.virustotal.com/gui/) are recommended.

If there are no known detections, there's a greater chance that it's a fresh or undiscovered malware campaign.

The absence of a **rich header**  indicates that the file is potentially packed or obfuscated to avoid detection by static analysis tools. This is typical behaviour of sophisticated malware that tries to evade detection by altering critical sections of its PE file.

The **function** tabs list **API calls** that the file has imported. This is also known as the IAT (Import Address Table). By clicking on the blacklist tab, PeStudio will sort the API by moving all the blacklisted functions to the top. This is useful because it enables us to understand how malware may behave once it compromises a host. The image below shows what API has been imported.


## Analysis using FLOSS

Open PowerShell and go to the directory where our file is, which is **C:\Users\Administrator\Desktop\Sample**. Note that it might take a while before the PowerShell prompt appears. Run the command `FLOSS.exe .\windows.exe > windows.txt`. This will run the tool **floss.exe** and output it to a file named windows.txt in the same directory.

### Another Example
We will try to figure out if the file is making any network connectivity to any possible c2 server.  Let's get started! Run the file **cobaltstrike.exe** and open **Process Explorer** on the desktop or from the taskbar. You can also search for it in the Windows search bar. If you manually click the binary **Explorer.exe** would be the parent process, and cobaltstrike.exe would be the child process. Let's see if that is indeed the case.


It is, as you can see from the screenshot above! Remember, this information is crucial, but let's focus on our goal to determine whether this process is making any network connectivity and to what destination. The **process ID is 4756**. Note that the Process ID might be different from your machine. Right-click on the process, select **Properties** and go to the **TCP/IP** tab. We should be able to determine the destination it connects to and the state it sends.


We will use another tool to determine if the information is correct. Stop the process and rerun it. This time, we will use **Procmon** or **Proces Monitor**. This is also located on the desktop or in the taskbar.

When we open Procmon, looking for the binary is challenging as it will list all active processes. What we are going to do is filter it. The image below points to the filter icon. Or you can press **CRTL + L**.


Now, using this filter involves several steps. This includes:

1. Select the **Process Name**
2. Select **contains**
3. type any value containing any word related to the process. In this case, **cobalt**
4. Click **include**
5. And then **Add** and Click on Apply
6. You should be able to see the conditions added.