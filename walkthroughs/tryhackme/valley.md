# Valley

**Room Link:** [https://tryhackme.com/room/valleype](https://tryhackme.com/room/valleype)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

ftp found on port 37370

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (350).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gobuster dir --url http://$VICTIM/static -w /usr/share/dirb/wordlists/big.txt -l
```

<figure><img src="../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ffuf -u http://$VICTIM/static/FUZZ -w /usr/share/dirb/wordlists/big.txt
```

<figure><img src="../../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (349).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>



```
Username: siemDev
Password: california
```

<figure><img src="../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>



### TCP/37370 - FTP

**Kali**

```
ftp $VICTIM 37370
Username: siemDev
Password: california
```



<figure><img src="../../.gitbook/assets/image (354).png" alt=""><figcaption></figcaption></figure>



### TCP/22 - SSH

**Kali**

```
ssh valleyDev@$VICTIM
Password: ph0t0s1234
```

<figure><img src="../../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>































