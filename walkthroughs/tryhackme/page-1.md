# Page 1

**Room Link:** [https://tryhackme.com/room/johntheripper0](https://tryhackme.com/room/johntheripper0)

### Hash ID

Use when John can't identify the hash

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
```

## Cracking Basic Hashes

**What type of hash is hash1.txt?**

According to hash-id MD5 is the most likely format.

```
cat hash1.txt
python3 hash-id.py 2e728dd31fb5949bc39cac5a9f066498
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**What is the cracked value of hash1.txt?**

```
john --format=raw-md5 hash1.txt 
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash2.txt?**

SHA-1 is the most likely possible hash.

```
cat hash2.txt
python hash-id.py 1A732667F3917C0F4AA98BB13011B9090C6F8065
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**What is the cracked value of hash2.txt**

```
john --format=raw-sha1 hash2.txt
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash3.txt?**

```
cat hash3.txt 
python hash-id.py D7F4D3CCEE7ACD3DD7FAD3AC2BE2AAE9C44F4E9B7FB802D73136D4C53920140A
```

****![](<../../.gitbook/assets/image (13).png>)****

**What is the cracked value of hash3.txt**

```
john --format=raw-sha256 hash3.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash4.txt?**

```
cat hash4.txt 
python hash-id.py c5a60cc6bbba781c601c5402755ae1044bbf45b78d1183cbf2ca1c865b6c792cf3c6b87791344986c8a832a0f9ca8d0b4afd3d9421a149d57075e1b4e93f90bf
```

****![](<../../.gitbook/assets/image (8).png>)****

**What is the cracked value of hash4.txt**

```
john --format=raw-sha512 hash4.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

SHA-512 didn't work.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Hash was Whirlpool which hash-id also thought it could be.

```
john --format=whirlpool hash4.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
