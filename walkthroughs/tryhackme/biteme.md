# biteme

**Room Link:** [https://tryhackme.com/room/biteme](https://tryhackme.com/room/biteme)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (748).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (749).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



<figure><img src="../../.gitbook/assets/image (753).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (752).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (750).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (751).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/console -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x phps
```

<figure><img src="../../.gitbook/assets/image (754).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (757).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (755).png" alt=""><figcaption></figcaption></figure>





