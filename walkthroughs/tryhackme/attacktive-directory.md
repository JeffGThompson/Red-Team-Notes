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

**Initial Scan**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### netbios-ssn port 139 & microsoft-ds port 445

```
enum4linux $VICTIM
```

The NetBIOS-Domain Name of the machine

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Enumerating Users via Kerberos

```
kerbrute/dist/kerbrute_linux_386 userenum --dc=$VICTIM -d=spookysec.local. userlist.txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
