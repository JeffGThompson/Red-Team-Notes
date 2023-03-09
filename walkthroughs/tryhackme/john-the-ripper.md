# John The Ripper

**Room Link:** [https://tryhackme.com/room/johntheripper0](https://tryhackme.com/room/johntheripper0)

### Hash ID

Great tool that the room provides, use it to identify the hash type when John can't identify the hash by itself.

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
```

## Walkthrough

### Cracking Basic Hashes

**What type of hash is hash1.txt?**

According to hash-id MD5 is the most likely format.

```
cat hash1.txt
python3 hash-id.py 2e728dd31fb5949bc39cac5a9f066498
```

<figure><img src="../../.gitbook/assets/image (7) (5).png" alt=""><figcaption></figcaption></figure>

**What is the cracked value of hash1.txt?**

```
john --format=raw-md5 hash1.txt 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash2.txt?**

SHA-1 is the most likely possible hash.

```
cat hash2.txt
python hash-id.py 1A732667F3917C0F4AA98BB13011B9090C6F8065
```

<figure><img src="../../.gitbook/assets/image (1) (9).png" alt=""><figcaption></figcaption></figure>

**What is the cracked value of hash2.txt**

```
john --format=raw-sha1 hash2.txt
```

<figure><img src="../../.gitbook/assets/image (4) (2) (1) (2).png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash3.txt?**

```
cat hash3.txt 
python hash-id.py D7F4D3CCEE7ACD3DD7FAD3AC2BE2AAE9C44F4E9B7FB802D73136D4C53920140A
```

****![](<../../.gitbook/assets/image (13) (1) (5).png>)****

**What is the cracked value of hash3.txt**

```
john --format=raw-sha256 hash3.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What type of hash is hash4.txt?**

```
cat hash4.txt 
python hash-id.py c5a60cc6bbba781c601c5402755ae1044bbf45b78d1183cbf2ca1c865b6c792cf3c6b87791344986c8a832a0f9ca8d0b4afd3d9421a149d57075e1b4e93f90bf
```

****![](<../../.gitbook/assets/image (8) (4) (2).png>)****

**What is the cracked value of hash4.txt**

```
john --format=raw-sha512 hash4.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

SHA-512 didn't work.

<figure><img src="../../.gitbook/assets/image (15) (6).png" alt=""><figcaption></figcaption></figure>

Hash was Whirlpool which hash-id also thought it could be.

```
john --format=whirlpool hash4.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (23) (1) (3).png" alt=""><figcaption></figcaption></figure>

### Cracking Windows Authentication Hashes

**What do we need to set the "format" flag to, in order to crack this?**

Flag should be set to NT

```
python hash-id.py 5460C85BD858A11475115D2DD3A82333
```

<figure><img src="../../.gitbook/assets/image (4) (1) (3).png" alt=""><figcaption></figcaption></figure>

**What is the cracked value of this password?**

```
john --format=nt ntlm.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

### Cracking /etc/shadow Hashes

**What is the root password?**

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt etchashes.txt 
```

<figure><img src="../../.gitbook/assets/image (11) (10).png" alt=""><figcaption></figcaption></figure>

### Single Crack Mode

Identified the hash as MD5 and added Jokers username to the file.

```
cat hash7.txt
python hash-id.py 7bf6d9bb82bed1302f331fc6b816aada
vi hash7.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
john --single --format=raw-md5 hash7.txt
```

<figure><img src="../../.gitbook/assets/image (3) (7) (2).png" alt=""><figcaption></figcaption></figure>

### Cracking Password Protected Zip Files

```
zip2john secure.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

<figure><img src="../../.gitbook/assets/image (4) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Cracking Password Protected RAR Archives

```
/opt/john/rar2john secure.rar > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

<figure><img src="../../.gitbook/assets/image (11) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Cracking SSH Keys with John

```
/opt/john/ssh2john.py idrsa.id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (4).png" alt=""><figcaption></figcaption></figure>

