# Page 1

**Room Link:** [https://tryhackme.com/room/netsecchallenge](https://tryhackme.com/room/netsecchallenge)



**What is the highest port number being open less than 10,000?**

```
nmap -sV -sT -O -p 1-10000 $VICTIM
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**There is an open port outside the common 1000 ports; it is above 10,000. What is it?**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**How many TCP ports are open?**

6

**What is the flag hidden in the HTTP server header?**

```
curl http://$VICTIM -I
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

**What is the flag hidden in the SSH server header?**

```
ssh -v $VICTIM
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

**We have an FTP server listening on a nonstandard port. What is the version of the FTP server?**

```
vsftpd 3.0.3
```



