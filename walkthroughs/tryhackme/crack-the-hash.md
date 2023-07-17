# Crack the hash

**Room Link:** [https://tryhackme.com/room/crackthehash](https://tryhackme.com/room/crackthehash)



### Hash Cracking Websites

1. [https://hashes.com/en/decrypt/hash   ](https://hashes.com/en/decrypt/hashhttps://crackstation.net/)
2. [https://crackstation.net/](https://hashes.com/en/decrypt/hashhttps://crackstation.net/)

### Hash Identifier Website

1. [https://www.onlinehashcrack.com/hash-identification.php](https://www.onlinehashcrack.com/hash-identification.php)

### Hash ID

Great tool that the room provides, use it to identify the hash type when John can't identify the hash by itself.

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
```

## Level 1

### 48bb6e862e54f2a795ffc4e541caed4d

```
python hash-id.py 48bb6e862e54f2a795ffc4e541caed4d
```

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-md5 hash.txt 
```



### CBFDAC6008F9CAB4083784CBD1874F76618D2A97

```
python hash-id.py CBFDAC6008F9CAB4083784CBD1874F76618D2A97 
```

<figure><img src="../../.gitbook/assets/image (81) (3).png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-sha1 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```



### 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

```
python hash-id.py 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
```

![](<../../.gitbook/assets/image (2) (1) (1).png>)

```
john --format=raw-sha256 hash.txt
```

### $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

```
python hash-id.py $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
```

<figure><img src="../../.gitbook/assets/image (30) (2).png" alt=""><figcaption></figcaption></figure>

hash-id couldn't find it so I went to hashcats website and looked at possible hashes. I also filtered for only four letter words from rockyou as mentioned in the hint.

[https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example\_hashes)

```
grep -Eow '\b\w{4}\b' /usr/share/wordlists/rockyou.txt > new.txt
john --format=bcrypt hash.txt --wordlist=new.txt
```



### 279412f945939ba78ce0758d3fd83daa

```
python hash-id.py 279412f945939ba78ce0758d3fd83daa
```

<figure><img src="../../.gitbook/assets/image (66) (2).png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-md4 hash.txt --wordlist=password
```

## Level 2

### F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

```
python hash-id.py F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
```

![](<../../.gitbook/assets/image (95) (1).png>)

```
john --format=raw-sha256 hash.txt 
```

### 1DFECA0C002AE40B8619ECF94819CC1B

Hash was a NTLM

```
python hash-id.py 1DFECA0C002AE40B8619ECF94819CC1B
```

<figure><img src="../../.gitbook/assets/image (3) (1) (2) (4).png" alt=""><figcaption></figcaption></figure>

Used [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hashhttps://crackstation.net/) to solve



### &#x20;$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

**Salt:** aReallyHardSalt

Used [https://www.onlinehashcrack.com/hash-identification.php](https://www.onlinehashcrack.com/hash-identification.php) to identify the hash&#x20;

<figure><img src="../../.gitbook/assets/image (6) (3).png" alt=""><figcaption></figcaption></figure>

**hash.txt**&#x20;

The hash is already in the hash so didn't change anything.

<figure><img src="../../.gitbook/assets/image (134) (1).png" alt=""><figcaption></figcaption></figure>

```
john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```



\---



### e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Salt: tryhackme

```
python hash-id.py e5d8870e5bdd26602cab8dbe07a942c8669e56d6
```

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

**hash.txt**

<figure><img src="../../.gitbook/assets/image (48) (4).png" alt=""><figcaption></figcaption></figure>

```
hashcat -m 160 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 160 hash.txt --show
```
