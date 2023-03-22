# The Lay of the land

**Room Link:** [https://tryhackme.com/room/thelayoftheland](https://tryhackme.com/room/thelayoftheland)



## Active Directory (AD) environment

The output of the systeminfo provides information about the machine, including the operating system name and version, hostname, and other hardware information as well as the AD domain.

**Victim(cmd)**

```
systeminfo | findstr Domain
```

<figure><img src="../../.gitbook/assets/image (13) (3).png" alt=""><figcaption></figcaption></figure>

## Users and Groups Management

**Use the Get-ADUser -Filter \* -SearchBase command to list the available user accounts within THM OU in the thmredteam.com domain. How many users are available?**

**Victim(powershell)**

```
 Get-ADUser -Filter * -SearchBase "OU=THM,DC=THMREDTEAM,DC=COM"
```

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

**Once you run the previous command, what is the UserPrincipalName (email) of the admin account?**

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

## Host Security Solution #1



**Enumerate the attached Windows machine and check whether the host-based firewall is enabled or not! (Y|N)**

**Victim(powershell)**

```
Get-NetFirewallProfile | Format-Table Name, Enabled
```

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

**Using PowerShell cmdlets such Get-MpThreat can provide us with threats details that have been detected using MS Defender. Run it and answer the following: What is the file name that causes this alert to record?**

**Victim(powershell)**

```
Get-MpThreat
```

<figure><img src="../../.gitbook/assets/image (4) (1) (5).png" alt=""><figcaption></figcaption></figure>



**Enumerate the firewall rules of the attached Windows machine. What is the port that is allowed under the THM-Connection rule?**

**Victim(powershell)**

```
Get-NetFirewallRule | select DisplayName, Enabled, Description
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Host Security Solution #2

**Victim(powershell)**

```
net start
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

<pre><code><strong>wmic service where "name like 'THM Service'" get Name,PathName
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

```
Get-Process -Name thm-service
```

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

```
netstat -noa |findstr "LISTENING" |findstr "2784"
```

<figure><img src="../../.gitbook/assets/image (13) (7).png" alt=""><figcaption></figcaption></figure>

**Visit the localhost on the port you found in Question #1. What is the flag?**

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Now enumerate the domain name of the domain controller, thmredteam.com, using the nslookup.exe, and perform a DNS zone transfer. What is the flag for one of the records?**

**Victim(powershell)**

```
nslookup.exe
> server 10.10.119.90
> ls -d thmredteam.com
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (8).png" alt=""><figcaption></figcaption></figure>

****

