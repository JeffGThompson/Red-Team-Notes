# CMesS

**Room Link**: [https://tryhackme.com/room/cmess](https://tryhackme.com/room/cmess)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A cmess.thm
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (556).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 cmess.thm
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (557).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://cmess.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



Mostly junk

<figure><img src="../../.gitbook/assets/image (559).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (558).png" alt=""><figcaption></figcaption></figure>



## Fuzzing Domains

rerun next time

**Kali**

```
wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u 'http://cmess.thm/' -H "Host: FUZZ.cmess.thm" > results.txt
```







