# Overpass 2 - Hacked

**Room Link:** [https://tryhackme.com/room/overpass2hacked](https://tryhackme.com/room/overpass2hacked)



### Forensics - Analyze the PCAP

What was the URL of the page they used to upload a reverse shell?

**Wireshark**

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**TCPDump**

```
tcpdump -r overpass2.pcapng | grep GET
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**What payload did the attacker use to gain access?**

**Wireshark**

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**TCPDump**

```
tcpdump -vvv -r overpass2.pcapng | grep -i payload.php -A 5
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



**What password did the attacker use to privesc?**

I realized I can just change the steam to find this result.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**How did the attacker establish persistence?**

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

**Using the fasttrack wordlist, how many of the system passwords were crackable?**

In the same stream for the previous question we can see the attacker cat the shadow file, I took the results of the command and saved them to a file called dump.txt then ran john against it.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```
sudo john --wordlist=/usr/share/wordlists/fasttrack.txt dump.txt
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### Research - Analyze the code

**What's the default hash for the backdoor?**

It's in the code on github

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**What's the hardcoded salt for the backdoor?**

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

**What was the hash that the attacker used? - go back to the PCAP for this!**

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

**Crack the hash using rockyou and a cracking tool of your choice. What's the password?**

****

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

```
hashcat -m 1710 -w /usr/share/wordlists/rockyou.txt hash.txt
```

### Attack - Get back in!
