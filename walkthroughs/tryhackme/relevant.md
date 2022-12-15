# Relevant

**Room Link:** [https://tryhackme.com/room/relevant](https://tryhackme.com/room/relevant)&#x20;



## Scanning

<pre><code><strong>nmap -A 10.10.145.102
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### 135/TCP - msrpc

```
nbtscan 10.10.145.102
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### TCP/445 - microsoft-ds

There is a share but we can't access the one non default one there with no credentials

```
smbclient -L 10.10.145.102
smbget -R smb://10.10.145.102/nt4wrksv
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



