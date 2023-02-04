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

### Welcome to Attacktive Directory

**Initial Scan**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

