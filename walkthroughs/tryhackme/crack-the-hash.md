# Crack the hash

**Room Link:** [https://tryhackme.com/room/crackthehash](https://tryhackme.com/room/crackthehash)



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

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-md5 hash.txt 
```



### CBFDAC6008F9CAB4083784CBD1874F76618D2A97

```
python hash-id.py CBFDAC6008F9CAB4083784CBD1874F76618D2A97 
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-sha1 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```



### 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

```
python hash-id.py 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
```

![](<../../.gitbook/assets/image (2).png>)

```
john --format=raw-sha256 hash.txt
```

### $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

```
python hash-id.py $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

```
john --format=raw-md4 hash.txt --wordlist=password
```

## Level 2

### F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

```
python hash-id.py F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
```



```
john --format=raw-md5 hash.txt 
```

### 1DFECA0C002AE40B8619ECF94819CC1B

```
python hash-id.py 1DFECA0C002AE40B8619ECF94819CC1B
```

###

```
john --format=raw-md5 hash.txt
```

### &#x20;$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Salt: aReallyHardSalt

```
python hash-id.py $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.
```



```
john --format=raw-md5 hash.txt
```



\---

$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

### 7

```
python hash-id.py 48bb6e862e54f2a795ffc4e541caed4d
```



```
john --format=raw-md5 hash.txt 
```
