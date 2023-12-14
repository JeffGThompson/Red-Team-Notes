# Enumerating Active Directory

**Room Link:** [https://tryhackme.com/room/adenumeration](https://tryhackme.com/room/adenumeration)

## Why AD Enumeration

**Kali**

```
systemd-resolve --interface breachad --set-dns $THMDCIP --set-domain za.tryhackme.com
nslookup thmdc.za.tryhackme.com
```

## Enumeration through Microsoft Management Console

**Kali**

```
xfreerdp +clipboard /u:"mandy.bryan" /v:thmjmp1.za.tryhackme.com:3389 /size:1024x568 /smart-sizing:800x1200
Password: Dbrnjhbz1986
```

You should have completed the [AD Basics room](https://tryhackme.com/jr/activedirectorybasics) by now, where different AD objects were initially introduced. In this task, it will be assumed that you understand what these objects are. Connect to THMJMP1 using RDP and your provisioned credentials from Task 1 to perform this task.\


### Microsoft Management Console 

In this task, we will explore our first enumeration method, which is the only method that makes use of a GUI until the very last task. We will be using the Microsoft Management Console (MMC) with the [Remote Server Administration Tools'](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps) (RSAT) AD Snap-Ins. If you use the provided Windows VM (THMJMP1), it has already been installed for you. However, if you are using your own Windows machine, you can perform the following steps to install the Snap-Ins:

1. Press Start
2. Search "Apps & Features" and press enter
3. Click Manage Optional Features
4. Click Add a feature
5. Search for "RSAT"
6. Select "**RSAT: Active Directory Domain Services and Lightweight Directory Tools"** and click **Install**

You can start MMC by using the Windows Start button, searching run, and typing in MMC. If we just run MMC normally, it would not work as our computer is not domain-joined, and our local account cannot be used to authenticate to the domain.

<figure><img src="../../.gitbook/assets/image (12) (1) (6).png" alt=""><figcaption></figcaption></figure>

This is where the Runas window from the previous task comes into play. In that window, we can start MMC, which will ensure that all MMC network connections will use our injected AD credentials.\


In MMC, we can now attach the AD RSAT Snap-In:

1. Click File -> Add/Remove Snap-in
2. Select and Add all three Active Directory Snap-ins
3. Click through any errors and warnings\

4. Right-click on Active Directory Domains and Trusts and select Change Forest
5. Enter _za.tryhackme.com_ as the Root domain and Click OK
6. Right-click on Active Directory Sites and Services and select Change Forest
7. Enter _za.tryhackme.com_ as the Root domain and Click OK
8. Right-click on Active Directory Users and Computers and select Change Domain
9. Enter _za.tryhackme.com_ as the Domain and Click OK
10. Right-click on Active Directory Users and Computers in the left-hand pane
11. Click on View -> Advanced Features\


If everything up to this point worked correctly, your MMC should now be pointed to, and authenticated against, the target Domain:

<figure><img src="../../.gitbook/assets/image (37) (3) (2).png" alt=""><figcaption></figcaption></figure>

We can now start enumerating information about the AD structure here.

### Users and Computers

Let's take a look at the Active Directory structure. For this task, we will focus on AD Users and Computers. Expand that snap-in and expand the za domain to see the initial Organisational Unit (OU) structure:

<figure><img src="../../.gitbook/assets/image (7) (2) (3) (1).png" alt=""><figcaption></figcaption></figure>

Let's take a look at the People directory. Here we see that the users are divided according to department OUs. Clicking on each of these OUs will show the users that belong to that department.

<figure><img src="../../.gitbook/assets/image (28) (4) (2).png" alt=""><figcaption></figcaption></figure>

Clicking on any of these users will allow us to review all of their properties and attributes. We can also see what groups they are a member of:

<figure><img src="../../.gitbook/assets/image (6) (1) (6).png" alt=""><figcaption></figcaption></figure>

We can also use MMC to find hosts in the environment. If we click on either Servers or Workstations, the list of domain-joined machines will be displayed.

<figure><img src="../../.gitbook/assets/image (11) (4) (2).png" alt=""><figcaption></figcaption></figure>

If we had the relevant permissions, we could also use MMC to directly make changes to AD, such as changing the user's password or adding an account to a specific group. Play around with MMC to better understand the AD domain structure. Make use of the search feature to look for objects.\


#### Benefits

* The GUI provides an excellent method to gain a holistic view of the AD environment.
* Rapid searching of different AD objects can be performed.\

* It provides a direct method to view specific updates of AD objects.
* If we have sufficient privileges, we can directly update existing AD objects or add new ones.

#### Drawbacks

* The GUI requires RDP access to the machine where it is executed.
* Although searching for an object is fast, gathering AD wide properties or attributes cannot be performed.

## Enumeration through Command Prompt

### Command Prompt 

There are times when you just need to perform a quick and dirty AD lookup, and Command Prompt has your back. Good ol' reliable CMD is handy when you perhaps don't have RDP access to a system, defenders are monitoring for PowerShell use, and you need to perform your AD Enumeration through a Remote Access Trojan (RAT). It can even be helpful to embed a couple of simple AD enumeration commands in your phishing payload to help you gain the vital information that can help you stage the final attack.

CMD has a built-in command that we can use to enumerate information about AD, namely `net`. The `net` command is a handy tool to enumerate information about the local system and AD. We will look at a couple of interesting things we can enumerate from this position, but this is not an exhaustive list.

Note: For this task you will have to use THMJMP1 and won't be able to use your own Windows VM. This will be explained in the drawbacks.\


### Users

We can use the `net` command to list all users in the AD domain by using the `user` sub-option:

**Victim(cmd)**

```
net user /domain
```

<figure><img src="../../.gitbook/assets/image (40) (3) (1).png" alt=""><figcaption></figcaption></figure>

This will return all AD users for us and can be helpful in determining the size of the domain to stage further attacks. We can also use this sub-option to enumerate more detailed information about a single user account:

**Victim(cmd)**

```
net user zoe.marshall /domain
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Note: If the user is only part of a small number of AD groups, this command will be able to show us group memberships. However, usually, after more than ten group memberships, the command will fail to list them all.\


### Groups

We can use the `net` command to enumerate the groups of the domain by using the `group` sub-option:

**Victim(cmd)**

```
net group /domain
```

<figure><img src="../../.gitbook/assets/image (43) (2).png" alt=""><figcaption></figcaption></figure>

This information can help us find specific groups to target for goal execution. We could also enumerate more details such as membership to a group by specifying the group in the same command:

**Victim(cmd)**

```
net group "Tier 1 Admins" /domain
```

<figure><img src="../../.gitbook/assets/image (10) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Password Policy

We can use the `net` command to enumerate the password policy of the domain by using the `accounts` sub-option:

**Victim(cmd)**

```
net accounts /domain
```

<figure><img src="../../.gitbook/assets/image (26) (5).png" alt=""><figcaption></figcaption></figure>

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

## Enumeration through PowerShell

### PowerShell

PowerShell is the upgrade of Command Prompt. Microsoft first released it in 2006. While PowerShell has all the standard functionality Command Prompt provides, it also provides access to cmdlets (pronounced command-lets), which are .NET classes to perform specific functions. While we can write our own cmdlets, like the creators of [PowerView](https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerView) did, we can already get very far using the built-in ones.

Since we installed the AD-RSAT tooling in Task 3, it automatically installed the associated cmdlets for us. There are 50+ cmdlets installed. We will be looking at some of these, but refer to [this list for the complete list of cmdlets.](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)

Using our SSH terminal, we can upgrade it to a PowerShell terminal using the following command: `powershell`

### Users

We can use the `Get-ADUser` cmdlet to enumerate AD users:

**Victim(powershell)**

```
Get-ADUser -Identity gordon.stevens -Server za.tryhackme.com -Properties *
```

<figure><img src="../../.gitbook/assets/image (42) (3) (1).png" alt=""><figcaption></figcaption></figure>

The parameters are used for the following:

* \-Identity - The account name that we are enumerating
* \-Properties - Which properties associated with the account will be shown, \* will show all properties
* \-Server - Since we are not domain-joined, we have to use this parameter to point it to our domain controller

For most of these cmdlets, we can also use the`-Filter` parameter that allows more control over enumeration and use the `Format-Table` cmdlet to display the results such as the following neatly:

**Victim(powershell)**

```
Get-ADUser -Filter 'Name -like "*stevens"' -Server za.tryhackme.com | Format-Table Name,SamAccountName -A
```

<figure><img src="../../.gitbook/assets/image (4) (3) (3).png" alt=""><figcaption></figcaption></figure>

### Groups

We can use the `Get-ADGroup` cmdlet to enumerate AD groups:

**Victim(powershell)**

```
Get-ADGroup -Identity Administrators -Server za.tryhackme.com
```

<figure><img src="../../.gitbook/assets/image (9) (2) (3).png" alt=""><figcaption></figcaption></figure>

We can also enumerate group membership using the `Get-ADGroupMember` cmdlet:

**Victim(powershell)**

```
Get-ADGroupMember -Identity Administrators -Server za.tryhackme.com
```

<figure><img src="../../.gitbook/assets/image (23) (2).png" alt=""><figcaption></figcaption></figure>

### AD Objects

A more generic search for any AD objects can be performed using the `Get-ADObject` cmdlet. For example, if we are looking for all AD objects that were changed after a specific date:

**Victim(powershell)**

```
$ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)
Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -includeDeletedObjects -Server za.tryhackme.com
```

<figure><img src="../../.gitbook/assets/image (48) (3).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

```
Get-ADObject -Filter 'badPwdCount -gt 0' -Server za.tryhackme.com
```

This will only show results if one of the users in the network mistyped their password a couple of times.\


### Domains

We can use `Get-ADDomain` to retrieve additional information about the specific domain:

**Victim(powershell)**

```
Get-ADDomain -Server za.tryhackme.com
```

<figure><img src="../../.gitbook/assets/image (49) (2).png" alt=""><figcaption></figcaption></figure>

### Altering AD Objects

The great thing about the AD-RSAT cmdlets is that some even allow you to create new or alter existing AD objects. However, our focus for this network is on enumeration. Creating new objects or altering existing ones would be considered AD exploitation, which is covered later in the AD module.

However, we will show an example of this by force changing the password of our AD user by using the `Set-ADAccountPassword` cmdlet:

**Victim(powershell)**

```
Set-ADAccountPassword -Identity gordon.stevens -Server za.tryhackme.com -OldPassword (ConvertTo-SecureString -AsPlaintext "old" -force) -NewPassword (ConvertTo-SecureString -AsPlainText "new" -Force)
```

Remember to change the identity value and password for the account you were provided with for enumeration on the distributor webpage in Task 1.\


### Benefits

* The PowerShell cmdlets can enumerate significantly more information than the net commands from Command Prompt.
* We can specify the server and domain to execute these commands using runas from a non-domain-joined machine.\

* We can create our own cmdlets to enumerate specific information.
* We can use the AD-RSAT cmdlets to directly change AD objects, such as resetting passwords or adding a user to a specific group.\


### Drawbacks

* PowerShell is often monitored more by the blue teams than Command Prompt.
* We have to install the AD-RSAT tooling or use other, potentially detectable, scripts for PowerShell enumeration.

## Enumeration through Bloodhound

Lastly, we will look at performing AD enumeration using [Bloodhound](https://github.com/BloodHoundAD/BloodHound). Bloodhound is the most powerful AD enumeration tool to date, and when it was released in 2016, it changed the AD enumeration landscape forever.

### Bloodhound History 

For a significant amount of time, red teamers (and, unfortunately, attackers) had the upper hand. So much so that Microsoft integrated their own version of Bloodhound in its Advanced Threat Protection solution. It all came down to the following phrase:

_"Defenders think in lists, Attackers think in graphs." - Unknown_\


Bloodhound allowed attackers (and by now defenders too) to visualise the AD environment in a graph format with interconnected nodes. Each connection is a possible path that could be exploited to reach a goal. In contrast, the defenders used lists, like a list of Domain Admins or a list of all the hosts in the environment.

This graph-based thinking opened up a world to attackers. It allowed for a two-stage attack. In the first stage, the attackers would perform phishing attacks to get an initial entry to enumerate AD information. This initial payload was usually incredibly noisy and would be detected and contained by the blue team before the attackers could perform any actions apart from exfiltrating the enumerated data. However, the attackers could now use this data offline to create an attack path in graph format, showing precisely the steps and hops required. Using this information during the second phishing campaign, the attackers could often reach their goal in minutes once a breach was achieved. It is often even faster than it would take the blue team to receive their first alert. This is the power of thinking in graphs, which is why so many blue teams have also started to use these types of tools to understand their security posture better.

### Sharphound

You will often hear users refer to Sharphound and Bloodhound interchangeably. However, they are not the same. Sharphound is the enumeration tool of Bloodhound. It is used to enumerate the AD information that can then be visually displayed in Bloodhound. Bloodhound is the actual GUI used to display the AD attack graphs. Therefore, we first need to learn how to use Sharphound to enumerate AD before we can look at the results visually using Bloodhound.

There are three different Sharphound collectors:

* Sharphound.ps1 - PowerShell script for running Sharphound. However, the latest release of Sharphound has stopped releasing the Powershell script version. This version is good to use with RATs since the script can be loaded directly into memory, evading on-disk AV scans.\

* Sharphound.exe - A Windows executable version for running Sharphound.
* AzureHound.ps1 - PowerShell script for running Sharphound for Azure (Microsoft Cloud Computing Services) instances. Bloodhound can ingest data enumerated from Azure to find attack paths related to the configuration of Azure Identity and Access Management.

Note: Your Bloodhound and Sharphound versions must match for the best results. Usually there are updates made to Bloodhound which means old Sharphound results cannot be ingested. This network was created using Bloodhound v4.1.0. Please make sure to use this version with the Sharphound results.\


When using these collector scripts on an assessment, there is a high likelihood that these files will be detected as malware and raise an alert to the blue team. This is again where our Windows machine that is non-domain-joined can assist. We can use the `runas` command to inject the AD credentials and point Sharphound to a Domain Controller. Since we control this Windows machine, we can either disable the AV or create exceptions for specific files or folders, which has already been performed for you on the THMJMP1 machine. You can find the Sharphound binaries on this host in the `C:\Tools\` directory. We will use the SharpHound.exe version for our enumeration, but feel free to play around with the other two. We will execute Sharphound as follows:\


```
Sharphound.exe --CollectionMethods <Methods> --Domain za.tryhackme.com --ExcludeDCs
```

Parameters explained:

* CollectionMethods - Determines what kind of data Sharphound would collect. The most common options are Default or All. Also, since Sharphound caches information, once the first run has been completed, you can only use the Session collection method to retrieve new user sessions to speed up the process.
* Domain - Here, we specify the domain we want to enumerate. In some instances, you may want to enumerate a parent or other domain that has trust with your existing domain. You can tell Sharphound which domain should be enumerated by altering this parameter.
* ExcludeDCs -This will instruct Sharphound not to touch domain controllers, which reduces the likelihood that the Sharphound run will raise an alert.\


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

### Session Data Only

The structure of AD does not change very often in large organisations. There may be a couple of new employees, but the overall structure of OUs, Groups, Users, and permission will remain the same.

However, the one thing that does change constantly is active sessions and LogOn events. Since Sharphound creates a point-in-time snapshot of the AD structure, active session data is not always accurate since some users may have already logged off their sessions or new users may have established new sessions. This is an essential thing to note and is why we would want to execute Sharphound at regular intervals.

A good approach is to execute Sharphound with the "All" collection method at the start of your assessment and then execute Sharphound at least twice a day using the "Session" collection method. This will provide you with new session data and ensure that these runs are faster since they do not enumerate the entire AD structure again. The best time to execute these session runs is at around 10:00, when users have their first coffee and start to work and again around 14:00, when they get back from their lunch breaks but before they go home.

You can clear stagnant session data in Bloodhound on the Database Info tab by clicking the "Clear Session Information" before importing the data from these new Sharphound runs.\


#### Benefits

* Provides a GUI for AD enumeration.
* Has the ability to show attack paths for the enumerated AD information.
* Provides more profound insights into AD objects that usually require several manual queries to recover.

#### Drawbacks

* Requires the execution of Sharphound, which is noisy and can often be detected by AV or EDR solutions.
