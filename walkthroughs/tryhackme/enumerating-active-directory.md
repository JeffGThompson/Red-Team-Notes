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

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

We can now start enumerating information about the AD structure here.

### Users and Computers

Let's take a look at the Active Directory structure. For this task, we will focus on AD Users and Computers. Expand that snap-in and expand the za domain to see the initial Organisational Unit (OU) structure:

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Let's take a look at the People directory. Here we see that the users are divided according to department OUs. Clicking on each of these OUs will show the users that belong to that department.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Clicking on any of these users will allow us to review all of their properties and attributes. We can also see what groups they are a member of:

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

We can also use MMC to find hosts in the environment. If we click on either Servers or Workstations, the list of domain-joined machines will be displayed.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

This will return all AD users for us and can be helpful in determining the size of the domain to stage further attacks. We can also use this sub-option to enumerate more detailed information about a single user account:

**Victim(cmd)**

```
net user zoe.marshall /domain
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Note: If the user is only part of a small number of AD groups, this command will be able to show us group memberships. However, usually, after more than ten group memberships, the command will fail to list them all.\


### Groups

We can use the `net` command to enumerate the groups of the domain by using the `group` sub-option:

**Victim(cmd)**

```
net group /domain
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

This information can help us find specific groups to target for goal execution. We could also enumerate more details such as membership to a group by specifying the group in the same command:

**Victim(cmd)**

```
net group "Tier 1 Admins" /domain
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### Password Policy

We can use the `net` command to enumerate the password policy of the domain by using the `accounts` sub-option:

**Victim(cmd)**

```
net accounts /domain
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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





