# Overpass 2 - Hacked

**Room Link:** [https://tryhackme.com/room/overpass2hacked](https://tryhackme.com/room/overpass2hacked)



### Forensics - Analyze the PCAP

What was the URL of the page they used to upload a reverse shell?

**Wireshark**

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

**TCPDump**

```
tcpdump -r overpass2.pcapng | grep GET
```

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

**What payload did the attacker use to gain access?**

**Wireshark**

<figure><img src="../../.gitbook/assets/image (15) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>

**TCPDump**

```
tcpdump -vvv -r overpass2.pcapng | grep -i payload.php -A 5
```

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>



**What password did the attacker use to privesc?**

I realized I can just change the steam to find this result.

<figure><img src="../../.gitbook/assets/image (43) (1).png" alt=""><figcaption></figcaption></figure>

**How did the attacker establish persistence?**

<figure><img src="../../.gitbook/assets/image (6) (4).png" alt=""><figcaption></figcaption></figure>

**Using the fasttrack wordlist, how many of the system passwords were crackable?**

In the same stream for the previous question we can see the attacker cat the shadow file, I took the results of the command and saved them to a file called dump.txt then ran john against it.

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

```
sudo john --wordlist=/usr/share/wordlists/fasttrack.txt dump.txt
```

<figure><img src="../../.gitbook/assets/image (11) (2).png" alt=""><figcaption></figcaption></figure>

### Research - Analyze the code

**What's the default hash for the backdoor?**

It's in the code on github

<figure><img src="../../.gitbook/assets/image (3) (5).png" alt=""><figcaption></figcaption></figure>

**What's the hardcoded salt for the backdoor?**

<figure><img src="../../.gitbook/assets/image (9) (3).png" alt=""><figcaption></figcaption></figure>

**What was the hash that the attacker used? - go back to the PCAP for this!**

<figure><img src="../../.gitbook/assets/image (14) (3) (1).png" alt=""><figcaption></figcaption></figure>

**Crack the hash using rockyou and a cracking tool of your choice. What's the password?**

****

<figure><img src="../../.gitbook/assets/image (27) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (3).png" alt=""><figcaption></figcaption></figure>

```
hashcat -m 1710 -w /usr/share/wordlists/rockyou.txt hash.txt
hashcat -m 1710 hash.txt --show
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>



### Attack - Get back in!

**The attacker defaced the website. What message did they leave as a heading?**

<figure><img src="../../.gitbook/assets/image (1) (6).png" alt=""><figcaption></figcaption></figure>

**Using the information you've found previously, hack your way back in!**

The development page doesn't exist so we can't get the initial shell like the attacker did so we can just use the information we found in wireshark and login with james credentials

```
nmap -A 10.10.167.112
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>



For some reason james password doesn't work and it was the other password that we cracked. SSH was also running on port 22 and 2222 but the credentials only worked for 2222. I guess it has to do with the exploit the attacker used.

```
ssh james@10.10.167.112 -p2222
Password: november16
```



```
ls -lah
./.suid_bash -p
```

There is a hidden file in james home directory owned by root. When we execute it we become root. -p flag is to turn on privilege's mode. Without it we still are james when we execute the script.

<figure><img src="../../.gitbook/assets/image (16) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>
