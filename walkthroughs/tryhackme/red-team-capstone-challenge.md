# Red Team Capstone Challenge



<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

**Project Registration**

The Trimento government mandates that all red teamers from TryHackMe participating in the challenge must register to allow their single point of contact for the engagement to track activities. As the island's network is segregated, this will also provide the testers access to an email account for communication with the government and an approved phishing email address, should phishing be performed.

To register, you need to get in touch with the government through its e-Citizen communication portal that uses SSH for communication. Here are the SSH details provided:

| <p>SSH Username<br></p> | <p>e-citizen<br></p>                |
| ----------------------- | ----------------------------------- |
| <p>SSH Password<br></p> | <p>stabilitythroughcurrency<br></p> |
| <p>SSH IP<br></p>       | X.X.X.250                           |

**Kali**

```
ssh e-citizen@X.X.X.250
Password: stabilitythroughcurrency
```

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>



## Scanning

## Web - Scanning&#x20;

**Kali**

```
nmap -A $WEB
```

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WEB
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Web - HTTP port 80

This ran for the majority of the time I was working on the box, I found the wordpress and checked robots.txt manually and the scan didn't really find anything of interest.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

Looking for php files we found the info file which can be used to find info about the server.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt -X .php
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

Manually looking around there is a meet the team page, when clicking on the images we can see the folder holding all their pictures with their names, potentially could be usernames.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**Output**

```
antony.ross.jpeg
ashley.chan.jpeg
brenda.henderson.jpeg
charlene.thomas.jpeg
christopher.smith.jpeg
emily.harvey.jpeg
keith.allen.jpeg
laura.wood.jpeg
leslie.morley.jpeg
lynda.gordon.jpeg
martin.savage.jpeg
mohammad.ahmed.jpeg
october.pn
october.png
paula.bailey.jpeg
rhys.parsons.jpeg
roy.sims.jpeg
theme-preview.png
```





**Admin Panel**

[http://10.200.119.13/october/index.php/backend/backend/auth/signin](http://10.200.119.13/october/index.php/backend/backend/auth/signin)

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

**STOPPED HERE**

1:01:52&#x20;

\[List.RulesL myrules]

&#x20;^._(?=.{8,})(?=._\[0-9])(?=._\[!@#$%^&_]).\*$



## WebMail - Scanning&#x20;

**Kali**

```
nmap -A $WebMail
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WebMail
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### WebMail - HTTP port 80

**Kali**

```
dirb http://$WebMail:80 /usr/share/wordlists/dirb/big.txt
```









aaa

## VPN - Scanning&#x20;

**Kali**

```
nmap -A $VPN 
```

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

OpenVPN port potentially found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VPN 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### VPN - HTTP port 80



**Kali**

```
dirb http://$VPN:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

vpn folder has a .ovpn file.

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

I don't think it is useful. May just be a default file as the IP address are set as .X.X

**Kali**

```
openvpn corpUsername.ovpn
```

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

If I type my username and password provided I get a login error but if I try with a fake account with no password set I can bypass the login and download a ovpn file

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

It appears to work

**Kali**

```
openvpn fake.ovpn
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ip a
```

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sP 12.100.1.1-255
```

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

12.100.1.10

## WebMail - Scanning&#x20;

**Kali**

```
nmap -A 12.100.1.10
```



### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 12.100.1.10
</strong></code></pre>



