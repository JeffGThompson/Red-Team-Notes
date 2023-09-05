# Agent T

**Room Link:** [https://tryhackme.com/room/agentt](https://tryhackme.com/room/agentt)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>





### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>





### TCP/80 - HTTP



Dirb wasn't working so I went to the url and it brought me to the admin page, but nothing actually worked.

<figure><img src="../../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

In the burp request we can see it is powered by PHP/8.1.0-dev which has a rce exploit.

<figure><img src="../../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>





## **Exploit**

```
git clone https://github.com/flast101/php-8.1.0-dev-backdoor-rce.git
cd php-8.1.0-dev-backdoor-rce/  
python backdoor_php_8.1.0-dev.py
```

<figure><img src="../../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

















































































