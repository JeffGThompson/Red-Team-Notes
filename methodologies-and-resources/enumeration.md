# Enumeration



## **Ports**

### **FTP**

TCP port 21

Login (try using anonymous:anonymous, anonymous:password, guest:guest, etc.).

```
ftp $TARGET 21
```

List files.

```
ls
```

List files (using Curl).

```
curl ftp://anonymous:anonymous@$TARGET:21
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
wget -m ftp://anonymous:anonymous@$TARGET:21 -nd
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
telnet $TARGET 25
HELO
VRFY root
QUIT
```

```
sudo nmap $TARGET -p25 --script smtp-commands -oN scans/$TARGET-nmap-scripts-smtp-commands
```

```
sudo nmap $TARGET -p25 --script smtp-enum-users --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} -oN scans/$TARGET-nmap-scripts-smtp-enum-users
```

```
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t $TARGET
```

```
sudo nmap $TARGET -p25 --script smtp-vuln* -oN scans/mailman-nmap-scripts-smtp-vuln
```

### **HTTP(s)**

HTTP TCP port 80 / HTTP TCP 443

Add things from this room: [https://tryhackme.com/room/contentdiscovery](https://tryhackme.com/room/contentdiscovery)

```
dirb http://$TARGET
dirb http://$TARGET:$PORT/ -o scans/$TARGET-dirb-$PORT-common
dirb http://$TARGET:80 /usr/share/wordlists/dirb/big.txt -z10 -o scans/$NAME-dirb-big-80
```

```
dirsearch -u $TARGET:$PORT -o $FULLPATH/$NAME-dirsearch-80
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
nikto -h $TARGET -T 2 -Format txt -o scans/$TARGET-nikto-80-misconfig
```

Scan for SQL injection vulnerabilities.

```
nikto -h $TARGET -T 9 -Format txt -o scans/$TARGET-nikto-80-sqli
```

Check if the target is vulnerable to Shellshock

```
sudo nmap $TARGET -p80 --script http-shellshock -oN scans/$TARGET-nmap-scripts-80-http-shellshock
```

Check if these files exist to gather info

```
$TARGET/robots.txt
$TARGET/sitemap.xml
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
telnet $TARGET 110
USER root
PASS root
RETR 1
QUIT
```

### **RPC**

TCP port 135

```
rpcclient -U '' $TARGET
srvinfo
netshareenum # print the real file-path of shares; good for accurate RCE
```

### **NetBIOS**

TCP port 139

```
nbtscan $TARGET
```

### **SMB**

TCP port 445

```
smbclient -L //$TARGET/ # list shares
smbclient -L //$TARGET/ -p $PORT # specify non-standard SMB/Samba port
```

The SMB shares discovered have the following permissions.

```
smbmap -H $TARGET
smbmap -H $TARGET -P $PORT
```

```
smbget -R smb://$TARGET/$SHARE
smbget -R smb://$TARGET:$PORT/$SHARE
```

Download files.

```
cd loot
smbclient \\\\$TARGET\\$SHARE
prompt
mget *
```

```
# check if vulnerable to EternalBlue
sudo nmap $TARGET -p445 --script smb-vuln-ms17-010 -oN scans/$NAME-nmap-scripts-smb-vuln-ms17-010
```

```
# check if vulnerable to SambaCry
sudo nmap $TARGET -p445 --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version -oN scans/$NAME-nmap-scripts-smb-vuln-cve-2017-7494
```

### **Rsync**

TCP port 873

```
sudo nmap $TARGET -p873 --script rsync-list-modules
rsync -av rsync://$TARGET/$SHARE --list-only
rsync -av rsync://$TARGET/$SHARE loot
```

### **NFS**

TCP port 2049

```
sudo nmap $TARGET -p111 --script-nfs*
showmount -e $TARGET

sudo mkdir /mnt/FOO
sudo mount //$TARGET:/$SHARE /mnt/FOO

sudo adduser demo
sudo sed -i -e 's/1001/5050/g' /etc/passwd
cat /mnt/FOO/loot.txt
```

### **SQL**

TCP port 3306

```
mysql -u $USER -h $TARGET
```

### **RDP**

TCP port 3389

```
sudo nmap $TARGET -p3389 --script rdp-ntlm-info -oN scans/$NAME-nmap-scripts-rdp-ntlm-info
```

```
rdesktop -u administrator $TARGET
```

### **Postgres**

TCP port 5437

```
psql -U postgres -p 5437 -h $TARGET # postgres:postgres
SELECT pg_ls_dir('/');
```

### **WinRM**

TCP port 5985

```
evil-winrm -u $USER -p $PASSWORD -i $TARGET
```

### **IRC**

TCP port 6667

```
irssi -c $TARGET -p $PORT
```

####

\
