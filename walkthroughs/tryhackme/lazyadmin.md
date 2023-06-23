# LazyAdmin

**Room Link:** [https://tryhackme.com/room/lazyadmin](https://tryhackme.com/room/lazyadmin)



### Scanning

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```



<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
searchsploit sweet rice
searchsploit -m php/webapps/40718.txt
```

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>





